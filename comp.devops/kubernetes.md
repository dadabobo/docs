---
html:
  embed_local_images: true
  embed_svg: true
  offline: false
  toc: Ansible
print_background: false
---

# Kubernetes
---
@import "./kubernetes-apiserver.md"

---
@import "./kubernetes-controllermanager.md"

---
@import "./kubernetes-scheduler.md"

---
@import "./kubernetes-kubelet.md"

---
@import "./kubernetes-proxy.md"

---
@import "./kubernetes-kubectl.md"

---
@import "./kubernetes-minikube.md"


@import "./kubernetes-kubespray.md"

---
@import "./kubernetes-authentication.md"

---
@import "./kubernetes-kubeconfig.md"

---
@import "./kubernetes-security.md"

---
@import "./kubernetes-concept.md"

---
@import "./kubernetes-basic.md"

---
@import "./kubernetes-coreos.md"


---
## 创建资源对象yaml示例
#### 创建 pod
@import "k8s/pod.yaml"

#### 创建 rc
@import "k8s/rc.yaml"