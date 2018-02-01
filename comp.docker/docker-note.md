## Docker

### 参考资料

书籍
* Coreos Essentials
* Docker -- 从实践到入门
* Docker Cookbook
* Docker in Action
* Docker in Pratice
* Docker Networking Cookbook
* Docker: Up and Running
* Using Docker
* Extending Docker


#### Docker -- 从实践到入门
* Docker 简介
* 基本概念
* 安装 Docker
* 使用镜像
* 操作容器
* 访问仓库
* 数据管理
* 使用网络
* 高级网络配置
* 实战案例
* 安全
* 底层实现
* Docker Compose 项目
* Docker Machine 项目
* Docker Swarm 项目
* Etcd 项目
* CoreOS 项目
* Kubernetes 项目
* Mesos 项目
* 容器与云计算

#### Docker Cookbook
* **Chapter 1** goes through several Docker installation scenarios, including using Docker Machine. It then presents the basic Docker commands to manage containers, mount data volumes, link containers, and so on. At the end of this chapter, you will have a working Docker host and you will have started multiple containers as well as understood the life cycle of containers.
* **Chapter 2** introduces the Dockerfile and Docker Hub and shows how to build/tag/commit an image. The chapter also shows how to run your own Docker registry and set up automated builds. At the end of this chapter, you will know how to create Docker images and share them privately or publicly and have a basic foundation to build continuous delivery pipelines.
* **Chapter 3** explains the networking mechanisms in Docker. You will learn how to get containers’ IP addresses and how to expose a container service on a specific host port. You will also learn about linking containers, and how to use nondefault networking configurations. This chapter contains a few recipes that provide a deeper understanding of networking containers. Concepts such as network namespaces, using an OVS bridge, and GRE tunnels are presented to lay a strong foundation for container networking. Finally, you will also learn about more advanced networking setups and tools, such as Weave, Flannel, and the currently experimental Docker Network feature.
* **Chapter 4** goes through configuration of the Docker daemon, especially security settings and remote access to the Docker API. It also covers a few basic problems, like compiling Docker from source, running its test suite, and using a new Docker binary. A few recipes provide better insight on Linux namespaces and their use in containers.
* **Chapter 5** introduces the new container management platform from Google. Kubernetes provides a way to deploy multicontainer applications on a distributed cluster. In addition, it provides an automated way to expose services and create replicas of containers. The chapter shows how to deploy Kubernetes on your own infrastructure, starting with a local Vagrant cluster and subsequently on a set of machines started in the cloud. I then present the key aspects of Kubernetes: pods, services, and replication controllers.
* **Chapter 6** covers four new Linux distributions that are optimized to run containers: CoreOS, Project Atomic, Ubuntu Core, and RancherOS. These new distributions provide just enough operating system to run and orchestrate Docker containers. Recipes cover installation and access to machines that use these distributions. This chapter also introduces tools that are used with these distributions to ease container orchestration (e.g., etcd, fleet, systemd).
* One of Docker’s strengths is its booming ecosystem. **Chapter 7** introduces several tools that have been created over the last 18 months and that leverage Docker to ease application deployment, continuous integration, service discovery, and orchestration. As an example, you will find recipes about Docker Compose, Docker Swarm, Mesos, Rancher, and Weavescope.
* The Docker daemon can be installed on a developer local machine. However, with cloud computing providing easy access to on-demand servers, it is fair to say that a lot of container-based applications will be deployed in the cloud. **Chapter 8** presents recipes to show how to access a Docker host on Amazon AWS, Google GCE, and Microsoft Azure. The chapter also introduces two new cloud services that use Docker: the AWS Elastic Container Service (ECS) and the Google Container Engine.
* **Chapter 9** addresses concerns about application monitoring when using containers. Monitoring and visibility of the infrastructure and the application have been a huge focus in the DevOps community. As Docker becomes more pervasive as a development and operational mechanism, lessons learned need to be applied to container-based applications.
* **Chapter 10** presents end-to-end application deployment scenarios on both single hosts and clusters. While some basic application deployments are presented in earlier chapters, the recipes presented here are closer to a production deployment setup. This is a more in-depth chapter that puts you on the path toward designing more-complex microservices.


#### Docker: Up and Running
* Chapters 1 and 2 provide an introduction to Docker, and explain what it is and how you can use it.
* Chapter 3 takes you through the steps required to install Docker.
* Chapters 4 through 6 dive into the Docker client, images, and containers, exploring what they are and how you can work with them.
* Chapters 7 and 8 discuss the flow for getting your containers into production and debugging them.
* Chapter 9 delves into deploying containers at scale in public and private clouds.
* Chapter 10 dives into advanced topics that require some familiarity with Docker and can be important as you start to use Docker in your production environment.
* Chapter 11 explores some of the core concepts that have started to solidify in the industry about how to design the next generation of Internet-scale production software.
* Chapter 12 wraps everything up and ties it with a bow. It includes a summary of what you have and how it should help you improve the way you deliver and scale software services.


#### Docker in Action
内容目录
* **Part 1:  Keeping a tidy and trustworthy computer**
* 1  Welcome to Docker
* 2  Running software in containers
* 3  Software installation simplified
* 4  Persistent storage and shared state with volumes
* 5  Network exposure
* 6  Limiting risk with isolation  
* **Part 2:  Packaging software for distribution**
* 7  Packaging software in images
* 8  Build automation and advanced image considerations
* 9  Public and private software distribution
* 10 Running Customized Registries  
* **Part 3:  Rapid and flexible deployment**
* 11 Declarative environments with Docker Compose
* 12 Clusters with Machine and Swarm


#### Using Docker
内容目录
* **Part I. Background and Basics**
* 1  The What and Why of Containers
* 2  Installation
* 3  First Steps
* 4  Docker Fundamentals
* **Part II. The Software Lifecycle with Docker**
* 5  Using Docker in Development
* 6  Creating a Simple Web App
* 7  Image Distribution
* 8  Continuous Integration and Testing with Docker
* 9  Deploying Containers
* 10 Logging and Monitoring
* **Part III. Tools and Techniques**
* 11 Networking and Service Discovery
* 12 Orchestration, Clustering, and Management
* 13 Security and Limiting Containers


#### Docker in Pratice
内容目录
* **PART 1 DOCKER FUNDAMENTALS**
* 1  Discovering Docker
* 2  Understanding Docker—inside the engine room
* **PART 2 DOCKER AND DEVELOPMENT**
* 3  Using Docker as a lightweight virtual machine
* 4  Day-to-day Docker
* 5  Configuration management—getting your house in order
* **PART 3 DOCKER AND DEVOPS**
* 6  Continuous delivery: a perfect fit for Docker principles
* 7  Network simulation: realistic environment testing without the pain
* 8  Continuous integration: speeding up your development pipeline
* **PART 4 DOCKER IN PRODUCTION**
* 9  Container orchestration: managing multiple Docker containers
* 10 Docker and security
* 11 Plain sailing—Docker in production and operational considerations
* 12 Docker in production—dealing with challenges


#### Docker Networking Cookbook
内容目录
1. Linux Networking Constructs
2. Configuring and Monitoring Docker Networks
3. User-Defined Networks
4. Building Docker Networks
5. Container Linking and Docker DNS
6. Securing Container Networks
7. Working with Weave Net
8. Working with Flannel
9. Exploring Network Features
10. Leveraging IPv6
11. Troubleshooting Docker Networks


#### Extending Docker
内容目录
1. Introduction to Extending Docker
2. Introducing First-party Tools
3. Volume Plugins
4. Network Plugins
5. Building Your Own Plugin
6. Extending Your Infrastructure
7. Looking at Schedulers
8. Security, Challenges, and Conclusions
