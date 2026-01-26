[根目录](../CLAUDE.md) > **dolphinscheduler-worker**

---

# dolphinscheduler-worker 模块文档

> 最后更新：2026-01-26 14:05:25 CST

---

## 变更记录 (Changelog)

### 2026-01-26 14:05:25 CST
- 初始化模块文档
- 识别任务执行引擎组件
- 记录 Worker 架构与依赖

---

## 模块职责

`dolphinscheduler-worker` 是 **Worker 执行服务模块**，负责任务的实际执行、日志收集和状态上报。

### 核心功能

- **任务执行**：执行各类任务（Shell、SQL、Spark、Flink 等）
- **任务管理**：任务实例的生命周期管理
- **日志管理**：任务日志的收集与上报
- **心跳上报**：定期向注册中心上报心跳
- **资源隔离**：基于 Linux Group 的资源隔离
- **故障恢复**：任务失败重试机制
- **RPC 服务**：提供 RPC 接口供 Master 调用

---

## 入口与启动

### 入口类

```
org.apache.dolphinscheduler.server.worker.WorkerServer
```

### 启动方式

```bash
# Maven 方式
cd dolphinscheduler-worker
mvn spring-boot:run

# JAR 方式
java -jar dolphinscheduler-worker/target/dolphinscheduler-worker-3.4.0-SNAPSHOT.jar

# 脚本方式
./dolphinscheduler-worker/bin/start.sh
```

### 配置文件

```
src/main/resources/
├── application.yaml              # Spring Boot 配置
├── worker.properties             # Worker 配置
└── logback.xml                   # 日志配置
```

---

## 架构设计

### 核心组件

```
WorkerServer
├── WorkerRegistryClient          # 注册中心客户端
├── WorkerRpcServer               # RPC 服务端
├── TaskExecutorService           # 任务执行服务
│   ├── PhysicalTaskEngine        # 物理任务引擎
│   ├── LogicalTaskEngine         # 逻辑任务引擎
│   └── TaskExecutionContext      # 任务执行上下文
├── WorkerConfigManager           # Worker 配置管理
└── TaskMetrics                   # 任务指标
```

---

## 任务执行引擎

### PhysicalTaskEngine - 物理任务引擎

执行物理任务（实际运行的任务）：

```java
public class PhysicalTaskEngineDelegator {
    // 执行任务
    public void executeTask(TaskExecutionContext taskExecutionContext);

    // 停止任务
    public void stopTask(TaskInstance taskInstance);

    // 暂停任务
    public void pauseTask(TaskInstance taskInstance);
}
```

### 任务执行流程

```
1. 接收 Master 分发的任务
2. 创建任务执行上下文（TaskExecutionContext）
3. 根据任务类型选择对应的 TaskChannel
4. 初始化任务环境
5. 执行任务
6. 收集日志
7. 上报任务状态
8. 清理任务环境
```

---

## 任务插件

### TaskPluginManager - 任务插件管理器

管理所有任务插件：

```java
public class TaskPluginManager {
    // 获取任务 Channel
    public TaskChannel getTaskChannel(String taskType);

    // 加载所有任务插件
    public void loadPlugins();

    // 获取所有任务类型
    public List<String> getTaskTypes();
}
```

### TaskChannel - 任务通道

任务插件与 Worker 的交互接口：

```java
public interface TaskChannel {
    // 创建任务
    Task createTask(TaskExecutionContext taskExecutionContext);

    // 取消任务
    void cancel(TaskExecutionContext taskExecutionContext);
}
```

---

## RPC 服务

### WorkerRpcServer - RPC 服务端

提供 RPC 接口供 Master 调用：

```java
public class WorkerRpcServer {
    // 执行任务
    public void executeTask(TaskExecutionContext context);

    // 停止任务
    public void killTask(TaskInstance taskInstance);

    // 查询任务状态
    public TaskInstanceStatus queryTaskInstanceStatus(long taskInstanceId);
}
```

---

## 日志管理

### 日志收集

- **实时日志**: 任务执行时实时输出
- **日志缓存**: 日志先缓存在本地
- **日志上传**: 定期上传日志到 Master/数据库

### 日志查看

- **Web UI**: 通过 Web UI 查看日志
- **日志下载**: 支持下载完整日志文件
- **日志搜索**: 支持关键字搜索

---

## 心跳机制

### Worker 心跳

```java
public class WorkerRegistryClient {
    // 注册 Worker
    public void registry();

    // 上报心跳
    public void heartbeat();

    // 注销 Worker
    public void unRegistry();
}
```

### 心跳信息

- Worker IP 和端口
- Worker 负载（CPU、内存）
- 运行中的任务数量
- Worker 启动时间

---

## 资源隔离

### Linux Group 隔离

基于 Linux cgroup 实现资源隔离：

```java
public class ResourceProcessor {
    // 创建资源组
    public void createGroup(String groupName);

    // 设置资源限制
    public void setResourceLimit(String groupName, ResourceLimit limit);

    // 删除资源组
    public void removeGroup(String groupName);
}
```

### 资源限制

- **CPU 限制**: 限制任务使用的 CPU 核心数
- **内存限制**: 限制任务使用的内存大小
- **IO 限制**: 限制任务的 I/O 速率

---

## 任务重试

### 重试策略

```java
public enum RetryStrategy {
    RETRY_0,     // 不重试
    RETRY_1,     // 重试 1 次
    RETRY_2,     // 重试 2 次
    RETRY_3,     // 重试 3 次
    RETRY_ALL,   // 无限重试
}
```

### 重试机制

- 任务失败后自动重试
- 重试次数可配置
- 重试间隔可配置
- 达到最大重试次数后标记为失败

---

## 监控指标

### WorkerServerMetrics - 指标收集

```java
public class WorkerServerMetrics {
    // 运行中的任务数
    public Gauge getRunningTaskCount();

    // 成功任务数
    public Counter getSuccessTaskCount();

    // 失败任务数
    public Counter getFailedTaskCount();

    // 任务执行时长
    public Timer getTaskExecutionTime();
}
```

---

## 关键依赖与配置

### Maven 依赖

```xml
<dependencies>
    <!-- Common -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-common</artifactId>
    </dependency>

    <!-- Task Plugin -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-task-all</artifactId>
    </dependency>

    <!-- Task Executor -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-task-executor</artifactId>
    </dependency>

    <!-- Registry -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-registry-all</artifactId>
    </dependency>

    <!-- Yarn AOP -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-yarn-aop</artifactId>
    </dependency>

    <!-- AWS SDK -->
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-s3</artifactId>
    </dependency>

    <!-- Spring Boot -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
</dependencies>
```

---

## 目录结构

```
dolphinscheduler-worker/
└── src/main/java/org/apache/dolphinscheduler/server/worker/
    ├── WorkerServer.java                # 主入口
    ├── config/                          # 配置
    │   ├── WorkerConfig.java
    │   └── WorkerServerLoadProtection.java
    ├── executor/                        # 执行器
    │   └── PhysicalTaskEngineDelegator.java
    ├── metrics/                         # 指标
    │   └── WorkerServerMetrics.java
    ├── plugin/                          # 插件
    │   ├── TaskPluginManager.java
    │   └── TaskChannel.java
    ├── registry/                        # 注册中心
    │   └── WorkerRegistryClient.java
    ├── rpc/                             # RPC
    │   └── WorkerRpcServer.java
    ├── runner/                          # 任务运行器
    │   ├── TaskExecuteThread.java
    │   └── TaskExecuteRunnable.java
    └── utils/                           # 工具类
```

---

## 测试与质量

### 单元测试

- **测试框架**: JUnit 5 + Mockito
- **测试位置**: `src/test/java/org/apache/dolphinscheduler/server/worker/`
- **覆盖率**: 60%+

---

## 常见问题 (FAQ)

### Q1: 如何添加新的任务类型？

1. 在 `dolphinscheduler-task-plugin` 下创建新模块
2. 实现 `TaskChannel` 接口
3. 实现 `Task` 接口
4. 在 `TaskPluginManager` 中注册

### Q2: 如何配置 Worker Group？

1. 在 Web UI 中创建 Worker Group
2. 启动 Worker 时指定 Worker Group
3. 在工作流中配置任务使用的 Worker Group

### Q3: 如何查看任务日志？

- 通过 Web UI 的"任务实例"页面
- 点击"查看日志"按钮
- 支持实时日志刷新

---

## 相关文件清单

```
dolphinscheduler-worker/
├── src/main/java/org/apache/dolphinscheduler/server/worker/
│   ├── WorkerServer.java                  # 主入口
│   ├── config/                            # 配置
│   ├── executor/                          # 执行器
│   ├── metrics/                           # 指标
│   ├── plugin/                            # 插件
│   ├── registry/                          # 注册中心
│   ├── rpc/                               # RPC
│   └── runner/                            # 任务运行器
└── src/main/resources/
    ├── application.yaml                    # 配置文件
    └── worker.properties                  # Worker 配置
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 14:05:25 CST
