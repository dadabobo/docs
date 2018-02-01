# Servlet笔记
##

Web 企业支持涉及两大计算平台--Web 浏览器和 Web 服务器。耍了解 Web 支持企业如何利用 J2EE Web 技术实现 Web 企业支持，一定要了解 Web 浏览器和 Web 服务器基本体系结构，了解其使用中遇到的常见问题与解决方法。

#### Web 浏览器架构

Web 客户机是向 HTTP 服务器发送和从 HTTP 服务器接收信息的任何应用程序。最常见的 Web 客户机是 Web 浏览器。Web 浏览器应用程序的主要目的是将 GUI 请求变成 HTTP 请求，将 HTTP 响应变成 GUI 显示内容。当然，HTTP 请求发送到 Web 服务器，HTTP 响应从 Web 服务器接收。Web 内容请求变成 URL，标识可以通过 Internet 访问的远程资源媒介。Web 响应通常是文档形式，最常见的是 Web 页面，包括多媒体和 HTML 表示内容，如文本、静态与动态图像、超链接、GUI 组件、声频片断和视频片断。此外，HTTP Web 响应中还可以返回外部处理器管理的不同类型文档的引用、Java 小程序和可执行浏览器脚本语言命令(如 JavaScript)。

Web 浏览器的核心是主控制器过程。这些过程管理本地内容缓存、处理 HTTP 请求与响应、配置 Web 浏览器和驱动 Web 页面内容的基本表示。请求处理器将 GUI 请求映射为 HTTP 网络请求，响应处理器将 HTTP 响应映射为 GUI 事件，显示请求的内容。每个请求与响应驱动与网络接口的 HTTP 数据 I/O。HTTP 协议用于非安全连接，HTTPS 在 SSL 连接中使用 HTTP。

**Web浏览器架构**  
![Web浏览器架构](d:/calibre/img/servlet/web_browser_arch.png "Web浏览器架构")

Web 浏览器体系结构通常用缓存管理器存储前面请求的数据，避免进行多余的网络请求。也可以用状态管理器用 cookies 提供会话信息管理和支持其他形式的会话跟踪。此外，可以用配置管理器配置 Web 浏览器的属性与行为。文档表示接口驱动 Web 浏览器 GUI 内容的实际显示。Web 浏览器通常支持下面一种或多种文档表示接口。
* **HTML表示管理器**: 所有 Web 浏览器都有某种形式的 HTML 表示管理器，可以通过 HTML 实体输出 HTML 显示内容和接收用户输入，如输入窗体与超链接。
* **XML表示管理器**: 许多 Web 浏览器也支持分析 XML 文档。
* **Java 运行环境**: Java 运行环境可以嵌入 Web 浏览器中，执行 Java 小程序和调用下载 JavaBean 组件的服务。
* **ActiveX 运行引擎**: Microsoft ActiveX 运行引擎嵌入在 Microsoft Web 浏览器中，执行下载的 ActiveX/COM 组件。 
* **脚本语言运行引擎**: 也可以用一个或几个脚本语言引擎执行嵌入 HTML 页面中的脚本命令(如 JavaScript)。
* **外部内容处理器**: 可以用外部内容处理器让 Web 浏览器外部过程中运行的应用程序动态执行所读取 URL 信息的内容(如用 Unzip 实用程序打开压缩档案文件)。
* **插入内容处理器**: 也可以通过内容处理器插入件在 Web 浏览器窗口中直接用某些 Web 浏览器执行读取 URL 信息的内容。这些插入件是浏览器特定扩展，事先要由 Web 浏览器装入。

#### Web 服务器架构

Web 服务器是个服务器端应用程序，主要作用是处理或委托 HTTP 请求和生成与路由 HTTP 响应。Web 服务器有多种类型，支持不同需求。最简单的 Web 客户机只是接收 GET 或 POST 请求，根据请求 URL 读取本地文件和将文件数据流化到 Web 客户机中。高端企业级 Web 服务器支持可伸缩 Web 客户机个数的并发请求，实现某种安全访问控制和支持各种 API，可以扩展 Web 服务器功能，动态按应用程序特定方式生成 Web 文档。

Web 服务器控制器是运行 Web 服务器的主要过程控制器情境。Web 服务器控制器通常管理一个线程池，用于处理收到的 Web 客户机请求。分配的 Web 处理器线程管理特定客户机请求和响应。每个请求和响应通过网络接口传递。HTTP 协议用于非安全连接，HTTPS 在 SSL 连接中使用 HTTP。

Web 服务器控制器也可以维护客户机连续请求之间的会话管理信息，使无状态 HTTP 协议实现状态。Web 服务器还可以维护缓存响应信息，使同一请求的连续实例可以迅速生成缓存响应。Web 服务器行为通常可以通过配置服务器环境而进行管理。管理 Web 服务器环境还可以包括指定 ACL，限制特定由用户对服务器端资源、的访问。此外，大多数 Web 服务器环境还提供登记 Web 服务器请求与响应的机制。

**Web服务器构**  
![Web服务器构](d:/calibre/img/servlet/web_server_arch.png "Web服务器构")

根据 HTTP 请求生成 Web 文档的接口是 Web 服务器体系结构的中心。Web 服务器通常支持下列文档服务接口中的一种或几种。

* **文件请求处理器**: 大多数 Web 服务器都有文件请求处理器，将 URL 映射为 Web 服务器本地的文件(如 HTML 文件)，可以读取和通过 HTTP 响应流返回客户机。
* **CGI引擎**: 公共网关接口(CGI )提供了用任何语言派生外部进程的标准接口控制，可以处理 HTTP 请求和生成 HTTP 响应。
* **ISAPI**: Internet 服务器应用程序接口(ISAPI) 定义了调用 Microso 位平台 DLL 的接口，处理 HTTP 请求和生成 HTTP 响应。
* **NSAPI**: Netscape 服务器应用程序接口(NSAPI) 定义了调用二进制库的接口，处理 HTTP 请求和生成 HTTP 响应。
* **脚本语言运行引擎**: 脚本语言运行引擎可以在 Web 服务器过程空间中执行阳 ML 文件中存储的脚本语言命令，如 JavaScript 与 VBScript。这种命令可以生成功态 HTML 内容，发送回请求客户机。
* **Java Servlet引擎**: Java Servlet 引擎可以在 Web 服务器过程空间中执行符合特定接口的 Java 代码，处理 HTTP 请求和生成 HTTP 响应。
* **JSP引擎**: Java 服务器页面(JSP) 引擎将特殊 Java 脚本语言命令编译成可执行的 Java Servlet 内容，然后在 Web 服务器进程空间中执行，处理 HTTP 请求和生成 HTTP 响应。

#### Web 服务
Web 服务涉及基于 XML 的请求(即 SOAP 请求)，按特定格式从 Web 服务客户机发送到 Web 服务服务器，然后从服务器向客户机发送类似的基于 XML 的响应(即 SOAP 响应)。

通常，这种 SOAP 请求与响应是在 HTTP 协议上发送的。但是，Web 服务也可以使用其他传输协议。由于 HTTP 的 SOAP 是实现 Web 服务的最常见方式，因此大多数 Web 服务器平台已经或正在支持 Web 服务通信。在 Web 服务器体系架构情境中，可以提供一个特殊的 Web 服务引擎，作为另一种文档服务接口。即 Web 服务引擎要支持从 HTTP 请求流分析出 SOAP 请求，将 SOAP 请求变成 Web 服务端点可用的信息格式(即反序列化或拆装)，然后将这个信息交给 Web 服务端点。同样，Web 服务引擎还要支持从 Web 服务端点接收响应信息，将信息变成 SOAP 响应(即序列化与编组)，将 SOAP 响应包装成 SOAP 响应，发回客户机。

#### Web 开发
Web 客户机与服务器表示 Web 开发人员编写 Web 应用程序的平台。在服务器端，Web 开发人员要编写在 Web 服务器环境中运行和操作的应用程序。因此，编写服务器端 Web 应用程序时必须考虑 Web 服务器平台的服务和限制。在客户端，Web 开发人员要生成 Web 服务器提供的表示内容，以便利用服务和接受 Web 浏览器的限制。
* CGI 编程
* 脚本语言
* 活动服务器页面
* 基于 Java 的 Web 编程
* Web 服务编程

#### Web 容器
Web 容器管理和运行 Servlet。

![Web容器](d:/calibre/img/servlet/servlet_container.png "Web容器")
![Web容器](d:/calibre/img/servlet/servlet_container2.png "Web容器")

Web 容器提供：
* 通信支持
* 生命周期管理
* 多线程支持
* 安全声明
* JSP 支持


![Servlet生命周期](d:/calibre/img/servlet/servlet1002.png "Servlet生命周期")
