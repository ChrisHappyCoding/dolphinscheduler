[根目录](../CLAUDE.md) > **dolphinscheduler-spi**

---

# dolphinscheduler-spi 模块文档

> 最后更新：2026-01-26 14:05:25 CST

---

## 变更记录 (Changelog)

### 2026-01-26 14:05:25 CST
- 初始化模块文档
- 识别 SPI 接口定义
- 记录插件机制

---

## 模块职责

`dolphinscheduler-spi` 是 **SPI（Service Provider Interface）模块**，定义了所有插件系统的核心接口。

### 核心功能

- **插件接口定义**：定义任务、数据源、存储等插件的接口
- **插件加载机制**：基于 Java SPI 的插件加载
- **插件生命周期管理**：插件的初始化、启动、停止
- **插件通信**：插件与核心系统的通信接口

---

## 核心 SPI 接口

### 任务插件 SPI

#### TaskChannel - 任务通道

```java
public interface TaskChannel {
    // 创建任务
    Task createTask(TaskExecutionContext taskExecutionContext);

    // 取消任务
    void cancel(TaskExecutionContext taskExecutionContext);
}
```

#### Task - 任务接口

```java
public interface Task {
    // 处理任务
    void handle();

    // 暂停任务
    void pause();

    // 停止任务
    void kill();

    // 获取任务结果
    TaskResult getTaskResult();
}
```

---

### 数据源插件 SPI

#### DataSourceProcessor - 数据源处理器

```java
public interface DataSourceProcessor {
    // 获取数据源类型
    String getType();

    // 创建连接
    Connection getConnection(ConnectionParam connectionParam);

    // 测试连接
    boolean checkConnection(ConnectionParam connectionParam);

    // 获取数据源元数据
    DataSourceClient getClient(ConnectionParam connectionParam);
}
```

---

### 存储插件 SPI

#### StorageOperate - 存储操作

```java
public interface StorageOperate {
    // 初始化
    void initStorage(String storageConfig);

    // 创建目录
    void createDir(String dirPath);

    // 上传文件
    void upload(File file, String savePath);

    // 下载文件
    File download(String filePath, String saveDir);

    // 删除文件
    void delete(String filePath);

    // 判断文件是否存在
    boolean exists(String filePath);
}
```

---

### 注册中心插件 SPI

#### Registry - 注册中心接口

```java
public interface Registry {
    // 初始化
    void init();

    // 注册
    void register(RegistryMetadata registryMetadata);

    // 注销
    void unregister(RegistryMetadata registryMetadata);

    // 订阅
    void subscribe(SubscriberType subscriberType, SubscribeListener listener);

    // 获取节点信息
    Map<String, String> getServerNodes(Set<String> serverSet);

    // 获取锁
    Lock getLock(String key);

    // 关闭
    void close();
}
```

---

### 调度插件 SPI

#### Scheduler - 调度器接口

```java
public interface Scheduler {
    // 初始化
    void init();

    // 启动
    void start();

    // 关闭
    void shutdown();

    // 调度任务
    void scheduleJob(ScheduleJob scheduleJob);

    // 删除任务
    void deleteJob(ScheduleJob scheduleJob);
}
```

---

### 告警示件 SPI

#### AlertChannel - 告警通道

```java
public interface AlertChannel {
    // 发送告警
    AlertResult send(AlertInfo alertInfo);

    // 获取告警类型
    String getType();

    // 校验参数
    void checkParam(Map<String, String> config);
}
```

---

## 插件发现机制

### Java SPI

使用 Java SPI 机制自动发现插件：

```java
// META-INF/services/org.apache.dolphinscheduler.plugin.task.api.TaskChannel
org.apache.dolphinscheduler.plugin.task.shell.ShellTaskChannel
org.apache.dolphinscheduler.plugin.task.sql.SqlTaskChannel
org.apache.dolphinscheduler.plugin.task.spark.SparkTaskChannel
```

### 插件加载

```java
public class TaskPluginManager {
    private final Map<String, TaskChannel> taskChannels = new HashMap<>();

    public void loadPlugins() {
        ServiceLoader<TaskChannel> serviceLoader = ServiceLoader.load(TaskChannel.class);
        for (TaskChannel taskChannel : serviceLoader) {
            taskChannels.put(taskChannel.getType(), taskChannel);
        }
    }
}
```

---

## 目录结构

```
dolphinscheduler-spi/
└── src/main/java/org/apache/dolphinscheduler/spi/
    ├── task/                          # 任务插件 SPI
    │   ├── TaskChannel.java
    │   ├── Task.java
    │   └── TaskConstants.java
    ├── datasource/                    # 数据源插件 SPI
    │   ├── DataSourceProcessor.java
    │   ├── DataSourceClient.java
    │   └── ...
    ├── storage/                       # 存储插件 SPI
    │   ├── StorageOperate.java
    │   └── StorageConstants.java
    ├── registry/                      # 注册中心插件 SPI
    │   ├── Registry.java
    │   ├── RegistryFactory.java
    │   └── ...
    ├── scheduler/                     # 调度插件 SPI
    │   ├── Scheduler.java
    │   ├── SchedulerFactory.java
    │   └── ...
    ├── alert/                         # 告警示件 SPI
    │   ├── AlertChannel.java
    │   ├── AlertInfo.java
    │   └── ...
    └── common/                        # 公共 SPI
        └── ...
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
        <scope>provided</scope>
    </dependency>

    <!-- SLF4J -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
    </dependency>
</dependencies>
```

---

## 常见问题 (FAQ)

### Q1: 如何实现自定义插件？

1. 确定插件类型（任务、数据源、存储等）
2. 实现对应的 SPI 接口
3. 在 `META-INF/services/` 下创建接口文件
4. 在文件中添加实现类的全限定名
5. 打包成 JAR 并部署

### Q2: 如何调试插件？

- 添加日志输出
- 使用单元测试
- 在插件管理器中注册插件

---

## 相关文件清单

```
dolphinscheduler-spi/
└── src/main/java/org/apache/dolphinscheduler/spi/
    ├── task/                          # 任务插件 SPI
    ├── datasource/                    # 数据源插件 SPI
    ├── storage/                       # 存储插件 SPI
    ├── registry/                      # 注册中心插件 SPI
    ├── scheduler/                     # 调度插件 SPI
    ├── alert/                         # 告警示件 SPI
    └── common/                        # 公共 SPI
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 14:05:25 CST
