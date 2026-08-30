# 定义
---
>[!note] Dockerfile
>Dockerfile是一个包含构建指令的文本文件，Docker 会根据其中的指令构建出一个镜像。
# 指令
---

| 指令     | 示例                      | 描述     |
| ------ | ----------------------- | ------ |
| `FROM` | `FROM python:3.11-slim` | 指定基础镜像 |
|        |                         |        |
# 例子
---
假设有一个Spring Boot项目，已经通过 Maven/Gradle 打包完成。
- 本地生成了 target/myapp-0.0.1-SNAPSHOT.jar
- 使用 JDK 17
- 应用端口为 8080
```dockerfile
# 1. 选择轻量的 JRE 基础镜像（推荐 Eclipse Temurin）
FROM eclipse-temurin:17-jre-alpine

# 2. 设置容器内工作目录
WORKDIR /app

# 3. 将本地打包好的 jar 文件复制到容器内并重命名
COPY target/*.jar app.jar

# 4. 暴露端口（与 application.yml/properties 一致）
EXPOSE 8080

# 5. 设置 JVM 环境变量（可选）
ENV JAVA_OPTS=""

# 6. 启动容器时的执行命令
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
```