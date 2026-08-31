# 定义
---
>[!note] docker run
>创建并运行一个容器。
>格式：docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

# 参数
---

| 选项              | 用法                  | 描述              |
| --------------- | ------------------- | --------------- |
| `-d, --detach`  | `-d`                | 在后台运行容器并打印容器 ID |
| `-e, --env`     | `-e KEY=value`      | 设置环境变量          |
| `--name`        | `--name name`       | 自定义容器名称         |
| `--network`     | `--network network` | 将容器连接到网络        |
| `-p, --publish` | `-p 主机端口:容器端口`      | 映射主机和容器的端口      |
