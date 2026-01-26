[根目录](../CLAUDE.md) > **dolphinscheduler-master**

---

# dolphinscheduler-master 模块文档

> 最后更新：2026-01-26 14:05:25 CST

---

## 变更记录 (Changelog)

### 2026-01-26 14:05:25 CST
- 初始化模块文档
- 识别核心调度引擎组件
- 记录 Master 架构与依赖

---

## 模块职责

`dolphinscheduler-master` 是 **Master 调度服务模块**，负责工作流的调度、依赖解析、任务分发和故障转移。

### 核心功能

- **工作流调度**：解析 DAG 工作流，按依赖关系调度任务
- **任务分发**：将任务分发给合适的 Worker 执行
- **依赖管理**：解析任务依赖关系，控制执行顺序
- **故障转移**：Master/Worker 故障检测与恢复
- **高可用**：多 Master 集群，支持 Leader 选举
- **工作流实例管理**：工作流实例的生命周期管理
- **任务实例管理**：任务实例的状态跟踪与更新
- **事件处理**：处理任务完成、失败等事件

---

## 入口与启动

### 入口类

```
org.apache.dolphinscheduler.server.master.MasterServer
```

### 启动方式

```bash
# Maven 方式
cd dolphinscheduler-master
mvn spring-boot:run

# JAR 方式
java -jar dolphinscheduler-master/target/dolphinscheduler-master-3.4.0-SNAPSHOT.jar

# 脚本方式
./dolphinscheduler-master/bin/start.sh
```

### 配置文件

```
src/main/resources/
├── application.yaml              # Spring Boot 配置
├── master.properties             # Master 配置
└── logback.xml                   # 日志配置
```

---

## 架构设计

### 核心组件

```
MasterServer
├── MasterCoordinator             # 主协调器
│   ├── WorkflowEngine           # 工作流引擎
│   ├── CommandProcessor         # 命令处理器
│   └── EventDispatcher         # 事件分发器
├── ClusterManager                # 集群管理器
│   ├── MasterClusters          # Master 集群管理
│   └── WorkerClusters          # Worker 集群管理
├── MasterRegistryClient          # 注册中心客户端
└── MasterRpcServer               # RPC 服务端
```

### 核心引擎

#### 1. WorkflowEngine - 工作流引擎

负责工作流的解析和执行：

```java
public class WorkflowEngine {
    // 工作流实例管理
    public void startWorkflowInstance(WorkflowInstance workflowInstance);

    // 任务实例管理
    public void startTaskInstance(TaskInstance taskInstance);

    // DAG 解析
    public DAG buildDAG(WorkflowDefinition workflowDefinition);
}
```

#### 2. MasterCoordinator - 主协调器

协调所有 Master 核心组件：

```java
public class MasterCoordinator {
    // 启动协调器
    public void start();

    // 停止协调器
    public void stop();

    // 处理工作流命令
    public void processCommand(Command command);
}
```

#### 3. CommandProcessor - 命令处理器

处理各类调度命令：

- **START_PROCESS**: 启动工作流
- **STOP_PROCESS**: 停止工作流
- **PAUSE_PROCESS**: 暂停工作流
- **RECOVER_PROCESS**: 恢复工作流

---

## 集群管理

### Master 集群

- **多 Master 架构**：支持多个 Master 实例
- **Leader 选举**：通过注册中心选举 Leader
- **负载均衡**：基于权重的负载均衡
- **故障检测**：心跳检测与故障转移

### Worker 集群

- **Worker 分组**：支持 Worker Group
- **负载均衡策略**：
  - 随机（Random）
  - 加权随机（Weighted Random）
  - 最少连接（Least Connections）
  - 一致性哈希（Consistent Hash）

### 负载均衡器

```java
public interface WorkerGroupLoadBalancer {
    // 选择 Worker
    WorkerSelector select(Collection<WorkerMetadata> workers);

    // 获取负载均衡策略
    LoadBalanceStrategy getStrategy();
}
```

---

## RPC 服务

### MasterRpcServer - RPC 服务端

提供 RPC 接口供 API Server 和其他服务调用：

```java
public class MasterRpcServer {
    // 执行工作流
    public void execWorkflowInstance(Command command, WorkflowInstance workflowInstance);

    // 查询工作流状态
    public WorkflowInstanceStatus queryWorkflowInstanceStatus(long workflowInstanceId);

    // 停止工作流
    public void stopWorkflowInstance(long workflowInstanceId);
}
```

---

## 调度策略

### FIFO 调度

- 先进先出
- 适用于简单场景

### 优先级调度

- 基于工作流优先级
- 优先级高的先执行

### 资源调度

- 基于 Worker Group
- 考虑 Worker 资源使用情况

---

## 事件总线

### SystemEventBus - 系统事件总线

处理 Master 内部事件：

```java
public class SystemEventBus {
    // 发布事件
    public void publishEvent(SystemEvent event);

    // 订阅事件
    public void subscribeEvent(EventListener listener);
}
```

### 事件类型

- **TaskSuccessEvent**: 任务成功事件
- **TaskFailedEvent**: 任务失败事件
- **WorkflowStartEvent**: 工作流开始事件
- **WorkflowEndEvent**: 工作流结束事件
- **MasterFailoverEvent**: Master 故障转移事件

---

## 高可用机制

### Leader 选举

```java
public class MasterRegistryClient {
    // 注册 Master
    public void registry();

    // 检查是否是 Leader
    public boolean isLeader();

    // 放弃 Leader
    public void releaseLeader();
}
```

### 故障转移

- **Master 故障**: 自动选举新 Leader
- **Worker 故障**: 任务重新分发到其他 Worker
- **任务故障**: 根据失败策略重试或跳过

---

## 监控指标

### MasterServerMetrics - 指标收集

```java
public class MasterServerMetrics {
    // 工作流实例数量
    public Gauge getWorkflowInstanceCount();

    // 任务实例数量
    public Gauge getTaskInstanceCount();

    // 调度延迟
    public Timer getScheduleLatency();
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

    <!-- Service -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-service</artifactId>
    </dependency>

    <!-- SPI -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-spi</artifactId>
    </dependency>

    <!-- Registry -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-registry-all</artifactId>
    </dependency>

    <!-- Scheduler -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-scheduler-all</artifactId>
    </dependency>

    <!-- Task Plugin -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-task-all</artifactId>
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
dolphinscheduler-master/
└── src/main/java/org/apache/dolphinscheduler/server/master/
    ├── MasterServer.java                # 主入口
    ├── cache/                           # 缓存
    ├── cluster/                         # 集群管理
    │   ├── ClusterManager.java
    │   ├── MasterClusters.java
    │   ├── WorkerClusters.java
    │   └── loadbalancer/               # 负载均衡器
    ├── config/                          # 配置
    │   ├── MasterConfig.java
    │   └── MasterServerLoadProtection.java
    ├── engine/                          # 引擎
    │   ├── MasterCoordinator.java      # 主协调器
    │   ├── WorkflowEngine.java         # 工作流引擎
    │   ├── task/                       # 任务引擎
    │   │   ├── dispatcher/            # 任务分发器
    │   │   └── event/                 # 事件处理
    │   └── system/                     # 系统引擎
    │       ├── SystemEventBus.java
    │       └── event/                 # 事件定义
    ├── metrics/                         # 指标
    │   └── MasterServerMetrics.java
    ├── registry/                        # 注册中心
    │   └── MasterRegistryClient.java
    ├── rpc/                             # RPC
    │   └── MasterRpcServer.java
    └── utils/                           # 工具类
```

---

## 测试与质量

### 单元测试

- **测试框架**: JUnit 5 + Mockito
- **测试位置**: `src/test/java/org/apache/dolphinscheduler/server/master/`
- **覆盖率**: 60%+

### 集成测试

- **测试场景**: 工作流调度、故障转移、负载均衡
- **测试工具**: Curator Test (ZooKeeper 测试)

---

## 常见问题 (FAQ)

### Q1: 如何添加新的调度策略？

1. 实现 `LoadBalanceStrategy` 接口
2. 在 `WorkerGroupLoadBalancer` 中注册
3. 配置文件中指定策略

### Q2: 如何处理 Worker 故障？

- Worker 故障会自动检测
- 任务自动重新分发到其他 Worker
- 可配置重试次数

### Q3: 如何扩展高可用？

- 部署多个 Master 实例（奇数个，建议 3 个）
- 部署多个 Worker 实例
- 配置 ZooKeeper/Etcd 作为注册中心

---

## 相关文件清单

```
dolphinscheduler-master/
├── src/main/java/org/apache/dolphinscheduler/server/master/
│   ├── MasterServer.java                  # 主入口
│   ├── cache/                             # 缓存
│   ├── cluster/                           # 集群管理
│   ├── config/                            # 配置
│   ├── engine/                            # 引擎
│   ├── metrics/                           # 指标
│   ├── registry/                          # 注册中心
│   └── rpc/                               # RPC
└── src/main/resources/
    ├── application.yaml                    # 配置文件
    └── master.properties                  # Master 配置
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 14:05:25 CST
