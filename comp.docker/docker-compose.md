## Docker Compose
Compose 文件是一个YAML文件，用于定义`services`、`network`和`volumes`。
Compose 文件的默认路径为 `./docker-compose.yml` (后缀为`.yml`和`.yaml`都可以)。

一个 `service` 配置将会应用到容器的启动中，很像将命令行参数传递给 `docker run`。
同样，`network` 和 `volume` 定义类似于 `docker network create` 和 `docker volume create`。 
与Docker运行一样，默认情况下尊重 `Dockerfile` 中指定的选项（例如`CMD`，`EXPOSE`，`VOLUME`，`ENV`） - 您不需要在 `docker-compose.yml` 中再次指定它们。

您可以在配置中使用具有类似Bash的 `${VARIABLE}` 语法使用环境变量 - 有关详细信息，请参阅Variable substitution。

###### Version 1
* the legacy format. 可省略版本号.
* 版本1文件无法声明命名卷，网络或构建参数。
* 示例  
```yaml
web:
  build: .
  ports:
   - "5000:5000"
  volumes:
   - .:/code
  links:
   - redis
redis:
  image: redis
```

###### Version 2.x
* 版本号：`'2'` 或 `'2.1'`
* 可以在volumes键下声明命名卷，并且可以在networks关键字下声明网络。
* 2.1 引入以下附加参数：`link_local_ips`, `isolation`, `labels` for volumes and networks

在大多数情况下，从版本1移动到2是一个非常简单的过程：
- 将整个文件缩进一级，并在顶部放置一个`services`:键。
- 添加一个`version: '2'`行在文件的顶部。

简单示例  
```yaml
version: '2'
services:
  web:
    build: .
    ports:
     - "5000:5000"
    volumes:
     - .:/code
  redis:
    image: redis
```
一个更广泛的例子,定义卷和网络  
```yaml
version: '2'
services:
  web:
    build: .
    ports:
     - "5000:5000"
    volumes:
     - .:/code
    networks:
      - front-tier
      - back-tier
  redis:
    image: redis
    volumes:
      - redis-data:/var/lib/redis
    networks:
      - back-tier
volumes:
  redis-data:
    driver: local
networks:
  front-tier:
    driver: bridge
  back-tier:
    driver: bridge
```

###### Version 3.x. 
版本号：'3' 或 '3.1'.
需要 Docker Engine release 1.13.0+
