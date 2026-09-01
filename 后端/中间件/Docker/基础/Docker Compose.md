# 定义
---
>[!note] Docker Compose
>Docker Compose 是一个用于定义和运行多容器应用程序的工具。

当项目变得很大的时候，容器以及数据卷、网络管理越来越复杂。因此需要一个统一的地方进行管理，这就是Docker Compose。
一个项目可能包含了多个服务、多个数据卷和多个网络，其中服务可能是一个容器也可能是一组配置相同的容器。
# 例子
---
```yaml
version: '3.8'  # Compose 文件版本

services:       # 定义服务
  web:          # 服务名
    build: .    # 构建配置
    ports:      # 端口映射
      - "8080:80"
    volumes:    # 挂载
      - ./app:/app
    environment: # 环境变量
      - NODE_ENV=production
    depends_on: # 依赖关系
      - db

  db:           # 数据库服务
    image: mysql:8.0
    volumes:
      - db_data:/var/lib/mysql

volumes:        # 定义卷
  db_data:

networks:       # 定义网络
  frontend:
  backend:
```
