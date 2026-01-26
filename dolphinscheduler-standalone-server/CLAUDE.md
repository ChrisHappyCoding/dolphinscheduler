[根目录](../CLAUDE.md) > **dolphinscheduler-standalone-server**

---

# dolphinscheduler-standalone-server 模块文档

> 最后更新：2026-01-26 14:05:25 CST

---

## 变更记录 (Changelog)

### 2026-01-26 14:05:25 CST
- 初始化模块文档
- 识别单机版服务器配置
- 记录嵌入式组件

---

## 模块职责

`dolphinscheduler-standalone-server` 是 **单机版服务器模块**，将所有服务（API、Master、Worker、Alert）集成在一个进程中，适用于开发测试和小规模部署。

### 核心功能

- **集成所有服务**：API Server、Master Server、Worker Server、Alert Server
- **嵌入式数据库**：支持 H2 嵌入式数据库
- **一键启动**：无需单独启动多个服务
- **快速体验**：适合快速体验和开发测试

---

## 入口与启动

### 入口类

```
org.apache.dolphinscheduler.StandaloneServer
```

### 启动方式

```bash
# Maven 方式
cd dolphinscheduler-standalone-server
mvn spring-boot:run

# JAR 方式
java -jar dolphinscheduler-standalone-server/target/dolphinscheduler-standalone-server-3.4.0-SNAPSHOT.jar

# 脚本方式
./dolphinscheduler-standalone-server/bin/start.sh
```

### 访问地址

```
http://localhost:12345/dolphinscheduler
默认账号: admin/dolphinscheduler123
```

---

## 集成的服务

### 1. API Server

- **端口**: 12345
- **功能**: 提供 REST API 和 Web UI
- **配置**: `application-api.yaml`

### 2. Master Server

- **端口**: 5678
- **功能**: 工作流调度
- **配置**: `application-master.yaml`

### 3. Worker Server

- **端口**: 1234
- **功能**: 任务执行
- **配置**: `application-worker.yaml`

### 4. Alert Server

- **端口**: 50052
- **功能**: 告警服务
- **配置**: `application-alert.yaml`

---

## 嵌入式数据库

### H2 数据库

默认使用 H2 嵌入式数据库：

```yaml
spring:
  datasource:
    driver-class-name: org.h2.Driver
    url: jdbc:h2:~/dolphinscheduler;MODE=MySQL;DB_CLOSE_DELAY=-1;DATABASE_TO_LOWER=TRUE
    username: sa
    password: ""
```

### 切换到 MySQL

修改配置文件：

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/dolphinscheduler?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: root
```

---

## 配置文件

### application.yaml

主配置文件，包含所有服务的配置：

```yaml
# 配置类导入
spring:
  profiles:
    include:
      - api
      - master
      - worker
      - alert
      - registry
      - datasource

# 服务器配置
server:
  port: 12345
```

### application-api.yaml

API Server 配置：

```yaml
api:
  server:
    port: 12345
  session:
    timeout: 24
```

### application-master.yaml

Master Server 配置：

```yaml
master:
  server:
    port: 5678
  queue:
    size: 100
```

### application-worker.yaml

Worker Server 配置：

```yaml
worker:
  server:
    port: 1234
  execute:
    threads: 10
```

### application-alert.yaml

Alert Server 配置：

```yaml
alert:
  server:
    port: 50052
```

---

## 部署模式

### Standalone 模式

适用于开发测试：

```bash
# 启动 Standalone Server
./bin/start.sh

# 停止 Standalone Server
./bin/stop.sh
```

### 集群模式

生产环境建议使用集群模式，独立部署各个服务。

---

## 目录结构

```
dolphinscheduler-standalone-server/
└── src/main/java/org/apache/dolphinscheduler/
    └── StandaloneServer.java          # 主入口
```

---

## 关键依赖与配置

### Maven 依赖

```xml
<dependencies>
    <!-- API Server -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-api</artifactId>
    </dependency>

    <!-- Master Server -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-master</artifactId>
    </dependency>

    <!-- Worker Server -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-worker</artifactId>
    </dependency>

    <!-- Alert Server -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-alert-server</artifactId>
    </dependency>

    <!-- H2 Database -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
    </dependency>
</dependencies>
```

---

## 常见问题 (FAQ)

### Q1: Standalone 模式适合生产环境吗？

不建议，Standalone 模式适合开发测试。生产环境建议使用集群模式，独立部署各个服务。

### Q2: 如何切换到 MySQL？

1. 修改 `application.yaml` 中的数据源配置
2. 将 MySQL 驱动添加到 classpath
3. 创建 MySQL 数据库
4. 执行建表脚本

### Q3: 如何修改端口？

修改对应的配置文件：

- API Server: `application-api.yaml`
- Master Server: `application-master.yaml`
- Worker Server: `application-worker.yaml`
- Alert Server: `application-alert.yaml`

---

## 相关文件清单

```
dolphinscheduler-standalone-server/
├── src/main/java/org/apache/dolphinscheduler/
│   └── StandaloneServer.java          # 主入口
└── src/main/resources/
    ├── application.yaml                # 主配置
    ├── application-api.yaml           # API 配置
    ├── application-master.yaml        # Master 配置
    ├── application-worker.yaml        # Worker 配置
    ├── application-alert.yaml         # Alert 配置
    └── ...
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 14:05:25 CST
