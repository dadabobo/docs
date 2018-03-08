## Authenticating
[Authenticating](https://kubernetes.io/docs/admin/authentication/)

#### Kubernetes的用户
所有Kubernetes集群都有两类用户：由Kubernetes管理的服务帐户(`service accounts`)和普通用户。

普通用户由外部独立服务管理。管理员分发私钥，用户存储如Keystone或Google帐户，甚至包含用户名和密码列表的文件。在这方面，Kubernetes没有表示代表正常用户账户的对象。普通用户无法通过API调用添加到群集中。

相反，`Service Accounts` 是由Kubernetes API管理的用户。它们绑定到特定的名称空间，并由API服务器自动创建或通过API调用手动创建。`Service Accounts` 绑定到一组存储为凭证的 `Secrets`，这些凭证将挂载到容器中，从而允许群集内进程与Kubernetes API对话。

API请求与普通用户或服务帐户绑定，或被视为匿名请求。这意味着集群内部或外部的每个进程（从在工作站上键入 `kubectl` 的人类用户 到 节点上的 `kubelets`, 到控制平面成员）都必须在向API服务器发出请求时进行身份验证，或者将其视为匿名用户。

#### 认证策略
Kubernetes使用客户端证书，`bearer tokens`，身份验证代理或HTTP基本身份验证来通过身份验证插件对API请求进行身份验证。由于向API服务器发出HTTP请求，插件会尝试将以下属性与请求关联：
* 用户名：标识最终用户的字符串。常见的值可能是 `kube-admin` 或`jane@example.com`。
* UID：标识最终用户并尝试比用户名更一致和唯一的字符串。
* 组：一组将用户与一组常用用户关联的字符串。
* 额外字段：包含附加信息的字符串列表的字符串映射授权人可能会觉得有用。

所有值对于认证系统都是不透明的，并且只有授权人解释时才具有重要性。

您可以一次启用多种身份验证方法。您通常应该至少使用两种方法：
* `service accounts` 的 `service account tokens`
* 至少一种用于用户认证的其他方法。

当启用多个验证器模块时，第一个模块成功验证请求短路评估。API服务器不保证订单验证器运行。

`system:authenticated` 组包含在所有经过身份验证的用户的组列表中。

与其他身份验证协议（`LDAP`，`SAML`，`Kerberos`，备用x509方案等）的集成可以使用 `身份验证代理` 或 `身份验证 webhook` 完成。

#### X509客户端证书
通过将 `--client-ca-file=SOMEFILE` 选项传递给API服务器来启用客户端证书认证。引用的文件必须包含一个或多个证书颁发机构，用于验证呈现给API服务器的客户端证书。如果提供并验证客户端证书，则使用主题的公用名称(`CN`)作为请求的`用户名`。从Kubernetes 1.4开始，客户端证书还可以使用证书的组织字段(`O`)来指示用户的`组`成员身份。要为用户包含多个组成员身份，请在证书中包含多个组织字段。

例如，使用openssl命令行工具生成证书签名请求：
`openssl req -new -key jbeda.pem -out jbeda-csr.pem -subj "/CN=jbeda/O=app1/O=app2"`
这将为用户名 “jbeda” 创建一个CSR，属于两个组 “app1” 和 “app2”。

#### 静态令牌文件
API服务器 `--token-auth-file=SOMEFILE` 在命令行上给出选项时从文件中读取承载令牌。目前，令牌持续无限期，并且无需重新启动API服务器即可更改令牌列表。

令牌文件是一个至少包含3列的csv文件：`令牌`，`用户名`，`用户uid`，后跟可选`组名`。请注意，如果您有多个组，则该列必须使用双引号，例如

`token,user,uid,"group1,group2,group3"`

###### 在请求中放置无记名标记
当使用来自http客户端的承载令牌认证时，API服务器需要一个值为 `Bearer THETOKEN` 的 `Authorization` 报头。不记名标记必须是一个字符序列，只需使用HTTP的编码和引用功能即可将其置于HTTP标头值中。例如：如果不记名令牌是 `31ada4fd-adec-460c-809a-9e56ceb75269` 那么它将出现在HTTP头中，如下所示。
`Authorization: Bearer 31ada4fd-adec-460c-809a-9e56ceb75269`

#### 引导令牌
为了允许为新集群提供简化的引导，Kubernetes包括一个名为 `Bootstrap Token` 的动态管理的 Bearer token 类型。这些令牌作为 `Secrets` 存储在 `kube-system` 命名空间中，在那里可以动态管理和创建它们。控制器管理器包含一个 TokenCleaner controller，用于在引导令牌到期时删除引导令牌。

代币是这种形式的 `[a-z0-9]{6}.[a-z0-9]{16}`。第一个组件是一个令牌ID，第二个组件是令牌密钥。您可以在HTTP标头中指定标记，如下所示：

`Authorization: Bearer 781292.db7bc3a58fc5f07e`

您必须使用 `--enable-bootstrap-token-auth` API服务器上的标志启用引导令牌认证器 。您必须通过 `--controllers` 控制器管理器上的标志启用 TokenCleaner 控制器。这是用类似的东西来完成的 `--controllers=*,tokencleaner`。 如果你正在使用 `kubeadm` 来引导群集，它会为你做这件事。

认证者认证为 `system:bootstrap:<Token ID>`。它包含在 `system:bootstrappers` 组中。命名和组有意限制用户阻止用户通过引导使用这些令牌。用户名和组可用于（并用于kubeadm）制定适当的授权策略以支持引导群集。

有关Bootstrap令牌验证器和控制器的深入文档以及如何通过 `kubeadm` 管理这些令牌，请参阅[Bootstrap令牌](https://kubernetes.io/docs/admin/bootstrap-tokens/)。

#### 静态密码文件
通过将 `--basic-auth-file=SOMEFILE` 选项传递给API服务器来启用基本身份验证。目前，基本身份验证凭证无限期地存在，并且无需重新启动API服务器即可更改密码。请注意，为方便起见，目前支持基本身份验证，但完成上述更安全的模式更易于使用。

基本身份验证文件是一个csv文件，最少有3列：`密码，用户名，用户标识`。在Kubernetes版本1.6及更高版本中，您可以指定包含以逗号分隔的组名称的可选第四列。如果您有多个组，则必须将第四列值用双引号（“）括起来。看下面的例子：

`password,user,uid,"group1,group2,group3"`

当使用来自http客户端的基本认证时，API服务器需要一个值为 `Basic BASE64ENCODED(USER:PASSWORD)` 的 `Authorization` 标头。

#### 服务帐户令牌
服务帐户是自动启用的验证器，它使用签名的承载令牌来验证请求。该插件需要两个可选标志：
* `--service-account-key-file` 包含用于签署持票人令牌的PEM编码密钥的文件。如果未指定，则将使用API​​服务器的TLS私钥。
* `--service-account-lookup` 如果启用，将从API中删除的令牌将被撤销。

服务帐户通常由API服务器自动创建，并通过 `ServiceAccount` 准入控制器与集群中运行的 `Pod` 相关联。承载令牌被安装到众所周知的位置，并允许群集内进程与API服务器进行通信。帐户可以使用 `PodSpec` 的 `serviceAccountName` 字段显式地与 `pod` 关联。

注意：`serviceAccountName` 通常会被忽略，因为这是自动完成的。
```yaml
apiVersion: apps/v1 # this apiVersion is relevant as of Kubernetes 1.9
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: default
spec:
  replicas: 3
  template:
    metadata:
    # ...
    spec:
      serviceAccountName: bob-the-bot
      containers:
      - name: nginx
        image: nginx:1.7.9
```

服务帐户持票人令牌非常适合在集群外部使用，并且可以用于为希望与Kubernetes API交谈的长期作业创建身份。要手动创建服务帐户，只需使用该 `kubectl create serviceaccount (NAME)` 命令即可。这会在当前命名空间和相关的秘密中创建一个服务帐户。

```yaml
$ kubectl create serviceaccount jenkins
serviceaccount "jenkins" created

$ kubectl get serviceaccounts jenkins -o yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  # ...
secrets:
- name: jenkins-token-1yvwg
```

创建的密钥包含API服务器的公共CA和签名的JSON Web令牌（JWT）。
```yaml
$ kubectl get secret jenkins-token-1yvwg -o yaml
apiVersion: v1
data:
  ca.crt: (APISERVER'S CA BASE64 ENCODED)
  namespace: ZGVmYXVsdA==
  token: (BEARER TOKEN BASE64 ENCODED)
kind: Secret
metadata:
  # ...
type: kubernetes.io/service-account-token
```

注意：值是base64编码的，因为秘密总是以base64编码。

签名的JWT可以用作不记名令牌，以作为给定的服务帐户进行验证。请参阅上面关于令牌如何包含在请求中。通常情况下，这些机密会安装到容器中，以便对群集内的API服务器进行访问，但也可以在群集外部使用。

服务帐户使用用户名进行身份验证 `system:serviceaccount:(NAMESPACE):(SERVICEACCOUNT)`，并分配给组 `system:serviceaccounts` 和 `system:serviceaccounts:(NAMESPACE)`。

警告：由于服务帐户令牌存储在秘密中，因此具有对这些秘密的读取权限的任何用户都可以作为服务帐户进行身份验证。授予服务帐户权限和读取秘密功能时要小心谨慎。

#### OpenID连接令牌
OpenID Connect 是 OAuth2 提供商支持的 OAuth2，特别是Azure Active Directory，Salesforce 和 Google。该协议的OAuth2的主要扩展是一个附加字段，返回的访问令牌称为ID令牌。此令牌是一个JSON Web令牌（JWT），具有众所周知的字段，例如由服务器签名的用户电子邮件。

为了识别用户，认证者使用OAuth2 令牌响应中的id_token（不是access_token） 作为承载令牌。请参阅上面关于令牌如何包含在请求中。

> 具体内容略。
> ....

#### Webhook令牌认证
Webhook认证是验证承载令牌的一个钩子。
* `--authentication-token-webhook-config-file` 描述如何访问远程webhook服务的kubeconfig文件。
* `--authentication-token-webhook-cache-ttl` 多久才能缓存身份验证决策。默认为两分钟。

配置文件使用kubeconfig 文件格式。文件内的“用户”是指API服务器webhook，“集群”是指远程服务。一个例子是：
```yaml
# clusters refers to the remote service.
clusters:
  - name: name-of-remote-authn-service
    cluster:
      certificate-authority: /path/to/ca.pem         # CA for verifying the remote service.
      server: https://authn.example.com/authenticate # URL of remote service to query. Must use 'https'.

# users refers to the API server's webhook configuration.
users:
  - name: name-of-api-server
    user:
      client-certificate: /path/to/cert.pem # cert for the webhook plugin to use
      client-key: /path/to/key.pem          # key matching the cert

# kubeconfig files require a context. Provide one for the API server.
current-context: webhook
contexts:
- context:
    cluster: name-of-remote-authn-service
    user: name-of-api-sever
  name: webhook
```  
如上所述，当客户端尝试使用承载令牌对API服务器进行认证时，认证webhook将 `authentication.k8s.io/v1beta1` TokenReview包含令牌的JSON序列化对象POST 发送到远程服务。Kubernetes不会质疑没有这种标题的请求。

#### 验证代理
可以将API服务器配置为根据请求标头值标识用户，例如 `X-Remote-User`。它旨在与用于设置请求标头值的身份验证代理结合使用。
* `--requestheader-username-headers` 必需，不区分大小写。标题名称按顺序检查用户标识。包含一个值的第一个标题用作用户名。
* `--requestheader-group-headers` 1.6+。可选，不区分大小写。建议使用“`X-Remote-Group`”。标题名称按顺序检查用户的组。所有指定标题中的所有值均用作组名称。
* `--requestheader-extra-headers-prefix` 1.6+。可选，不区分大小写。建议使用“`X-Remote-Extra-`”。查找头部前缀以确定有关用户的额外信息（通常由配置的授权插件使用）。任何以任何指定前缀开头的标头都会删除前缀，标头名称的其余部分将成为额外的关键字，而标头值则是额外的值。

例如，使用此配置：
```yaml
--requestheader-username-headers=X-Remote-User
--requestheader-group-headers=X-Remote-Group
--requestheader-extra-headers-prefix=X-Remote-Extra-
```
这个请求：
```
GET / HTTP/1.1
X-Remote-User: fido
X-Remote-Group: dogs
X-Remote-Group: dachshunds
X-Remote-Extra-Scopes: openid
X-Remote-Extra-Scopes: profile
```
会导致这个用户信息：
```
name: fido
groups:
- dogs
- dachshunds
extra:
  scopes:
  - openid
  - profile
```  
为了防止标头欺骗，验证代理需要在验证请求标头之前向API服务器提供有效的客户端证书，以针对指定的CA进行验证。
* `--requestheader-client-ca-file` 需要。PEM编码证书包。在检查用户名的请求标题之前，必须根据指定文件中的证书颁发机构提交并验证有效的客户端证书。
* `--requestheader-allowed-names` 可选的。常用名称列表（cn）。如果已设置，则在检查请求标头的用户名之前，必须先呈现指定列表中具有公用名称（cn）的有效客户端证书。如果为空，则允许使用任何通用名称。

#### 梯形失真密码
通过 `--experimental-keystone-url=<AuthURL>` 在启动期间将选项传递给API服务器来启用Keystone认证。该插件已实施  `plugin/pkg/auth/authenticator/password/keystone/keystone.go` 并且目前使用基本身份验证通过用户名和密码验证用户。

如果您为Keystone服务器配置了自签名证书，则可能需要 `--experimental-keystone-ca-file=SOMEFILE` 在启动Kubernetes API服务器时设置该选项。如果您设置了该选项，Keystone服务器的证书将由中的一个权威机构验证 `experimental-keystone-ca-file`。否则，证书由主机的根证书颁发机构验证。

有关如何使用keystone管理项目和用户的详细信息，请参阅 Keystone文档。请注意，此插件仍处于试验阶段，正在积极开发之中，并可能在后续版本中进行更改。

有关更多详细信息，请参阅讨论， 蓝图和建议的更改。

#### 匿名请求
启用时，未被其他已配置身份验证方法拒绝的请求会被视为匿名请求，并且会被给予用户名 `system:anonymous` 和一组用户名 `system:unauthenticated`。

例如，在配置了令牌认证和启用匿名访问的服务器上，提供无效承载令牌的请求会收到 `401 Unauthorized` 错误。提供不记名令牌的请求将被视为匿名请求。

在1.5.1-1.5.x中，默认情况下禁用匿名访问，并且可以通过将 `--anonymous-auth=true` 选项传递给API服务器来启用。

在1.6+中，如果使用非授权方式的授权模式，则默认启用匿名访问 `AlwaysAllow` ，并且可以通过将 `--anonymous-auth=false` 选项传递给API服务器来禁用匿名访问。从1.6开始，ABAC和RBAC授权人需要明确授权 `system:anonymous` 用户或 `system:unauthenticated` 组，因此授予`*`用户或`*`组访问权限的传统策略规则不包括匿名用户。

#### 用户模仿
用户可以通过模拟标题充当另一个用户。这些让请求手动覆盖请求认证为的用户信息。例如，管理员可以使用此功能通过暂时模仿其他用户并查看请求是否被拒绝来调试授权策略。

模拟请求首先作为请求用户进行身份验证，然后切换到模拟的用户信息。
* 用户使用他们的凭据和模拟头进行API调用。
* API服务器认证用户。
* API服务器确保经过身份验证的用户具有模拟权限。
* 请求用户信息被替换为模拟值。
* 评估请求，授权对模拟的用户信息起作用。

以下HTTP标头可用于执行模拟请求：
* `Impersonate-User`：充当的用户名。
* `Impersonate-Group`：充当的组名称。可以提供多次设置多个组。可选的。需要“模拟用户”
* `Impersonate-Extra-( extra name )`：用于将额外字段与用户关联的动态标题。可选的。需要“模拟用户”

一组示例头文件：
```
Impersonate-User: jane.doe@example.com
Impersonate-Group: developers
Impersonate-Group: admins
Impersonate-Extra-dn: cn=jane,ou=engineers,dc=example,dc=com
Impersonate-Extra-scopes: view
Impersonate-Extra-scopes: development
```

当使用 `kubectl` 设置 `--as` 标志来配置 `Impersonate-User` 标题时，设置 `--as-group` 标志来配置 `Impersonate-Group` 标题。
```bash
$ kubectl drain mynode
Error from server (Forbidden): User "clark" cannot get nodes at the cluster scope. (get nodes mynode)

$ kubectl drain mynode --as=superman --as-group=system:masters
node "mynode" cordoned
node "mynode" drained
```

为模仿用户，组或设置额外字段，模拟用户必须能够对正在模拟的属性类型（“用户”，“组”等）执行“模仿”动词。对于启用RBAC授权插件的集群，以下 `ClusterRole` 包含设置用户和组模拟标头所需的规则：
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: impersonator
rules:
- apiGroups: [""]
  resources: ["users", "groups", "serviceaccounts"]
  verbs: ["impersonate"]
```

额外的字段被评估为资源 “`userextras`” 的子资源。要允许用户使用额外字段“范围”的模拟标头，应授予用户以下角色：
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: scopes-impersonator
rules:
# Can set "Impersonate-Extra-scopes" header.
- apiGroups: ["authentication.k8s.io"]
  resources: ["userextras/scopes"]
  verbs: ["impersonate"]
```

模拟头文件的值也可以通过限制 `resourceNames` 可以使用的资源集来限制。
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: limited-impersonator
rules:
# Can impersonate the user "jane.doe@example.com"
- apiGroups: [""]
  resources: ["users"]
  verbs: ["impersonate"]
  resourceNames: ["jane.doe@example.com"]

# Can impersonate the groups "developers" and "admins"
- apiGroups: [""]
  resources: ["groups"]
  verbs: ["impersonate"]
  resourceNames: ["developers","admins"]

# Can impersonate the extras field "scopes" with the values "view" and "development"
- apiGroups: ["authentication.k8s.io"]
  resources: ["userextras/scopes"]
  verbs: ["impersonate"]
  resourceNames: ["view", "development"]
```