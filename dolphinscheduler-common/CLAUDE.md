[根目录](../CLAUDE.md) > **dolphinscheduler-common**

---

# dolphinscheduler-common 模块文档

> 最后更新：2026-01-26 13:07:02 CST

---

## 变更记录 (Changelog)

### 2026-01-26 13:07:02 CST
- 初始化模块文档
- 识别核心工具类与枚举
- 记录公共模型与常量

---

## 模块职责

`dolphinscheduler-common` 是 **公共工具模块**，提供全项目共享的工具类、枚举、常量、模型等基础组件。

### 核心功能

- **枚举定义**：任务类型、工作流状态、执行类型等枚举
- **工具类**：日期、字符串、JSON、文件等工具方法
- **数据模型**：命令、任务实例、工作流实例等模型
- **常量定义**：系统常量、配置常量
- **异常处理**：自定义异常类

---

## 目录结构

```
dolphinscheduler-common/
└── src/main/java/org/apache/dolphinscheduler/common/
    ├── enums/                    # 枚举定义
    │   ├── CommandType.java      # 命令类型
    │   ├── TaskType.java         # 任务类型
    │   ├── ExecutionStatus.java  # 执行状态
    │   ├── WorkflowExecutionStatus.java  # 工作流执行状态
    │   └── ...
    ├── utils/                    # 工具类
    │   ├── DateUtils.java        # 日期工具
    │   ├── JSONUtils.java        # JSON 工具
    │   ├── StringUtils.java      # 字符串工具
    │   ├── FileUtils.java        # 文件工具
    │   └── ...
    ├── model/                    # 数据模型
    │   ├── Command.java          # 命令模型
    │   ├── TaskInstance.java     # 任务实例
    │   └── ...
    ├── constants/                # 常量定义
    │   ├── Constants.java        # 通用常量
    │   └── ...
    ├── config/                   # 配置类
    │   ├── CommonConfiguration.java
    │   └── ...
    ├── exception/                # 异常类
    │   ├── BaseException.java
    │   └── ...
    └── lifecycle/                # 生命周期管理
        └── ServerLifeCycleManager.java
```

---

## 核心枚举

### 任务类型 (TaskType)

```java
public enum TaskType {
    SHELL(0, "Shell"),
    SQL(1, "SQL"),
    PYTHON(2, "Python"),
    SPARK(3, "Spark"),
    FLINK(4, "Flink"),
    HTTP(5, "HTTP"),
    ...
}
```

### 执行状态 (ExecutionStatus)

```java
public enum ExecutionStatus {
    SUBMITTED_SUCCESS(0, "submit success"),
    RUNNING_EXECUTION(1, "running"),
    PAUSE(2, "pause"),
    STOP(3, "stop"),
    SUCCESS(4, "success"),
    FAILURE(5, "failure"),
    ...
}
```

### 工作流执行状态 (WorkflowExecutionStatus)

```java
public enum WorkflowExecutionStatus {
    RUNNING(1, "running"),
    SERIAL_WAIT(2, "serial wait"),
    STOPPED(3, "stopped"),
    FINISHED(4, "finished"),
    FAILED(5, "failed"),
    ...
}
```

### 命令类型 (CommandType)

```java
public enum CommandType {
    START_PROCESS(0, "start workflow"),
    STOP_PROCESS(1, "stop workflow"),
    PAUSE_PROCESS(2, "pause workflow"),
    RECOVER_PROCESS(3, "recover workflow"),
    ...
}
```

---

## 核心工具类

### DateUtils - 日期工具

```java
// 格式化日期
public static String formatDate(Date date, String pattern)

// 获取当前时间
public static Date getCurrentDate()

// 日期计算
public static Date addDays(Date date, int days)

// 时间差计算
public static long diffMinutes(Date startDate, Date endDate)
```

### JSONUtils - JSON 工具

```java
// 对象转 JSON
public static String toJsonString(Object obj)

// JSON 转对象
public static <T> T parseObject(String json, Class<T> clazz)

// JSON 转列表
public static <T> List<T> toList(String json, Class<T> clazz)
```

### StringUtils - 字符串工具

```java
// 判空
public static boolean isEmpty(String str)

// 去空格
public static String trim(String str)

// 字符串连接
public static String join(Collection<?> coll, String separator)
```

### FileUtils - 文件工具

```java
// 文件大小转换
public static String formatFileSize(long size)

// 获取文件扩展名
public static String getExtension(String filename)

// 文件是否存在
public static boolean existFile(String filename)
```

---

## 核心数据模型

### Command - 命令模型

```java
public class Command {
    private int type;                    // 命令类型
    private long processInstanceId;      // 流程实例 ID
    private String taskDependType;       // 任务依赖类型
    private String failurePolicy;        // 失败策略
    private String warningType;          // 告警类型
    private ...
}
```

### TaskInstance - 任务实例模型

```java
public class TaskInstance {
    private int id;                      // 任务实例 ID
    private String name;                 // 任务名称
    private TaskType taskType;           // 任务类型
    private ExecutionStatus state;       // 执行状态
    private Date startTime;              // 开始时间
    private Date endTime;                // 结束时间
    private ...
}
```

---

## 核心常量

### Constants - 通用常量

```java
public class Constants {
    // 字符串常量
    public static final String STRING_NULL = "null";
    public static final String SLASH = "/";
    public static final String COMMA = ",";

    // 数字常量
    public static final int INT_0 = 0;
    public static final int INT_1 = 1;

    // 时间常量
    public static final long MINUTE_TIME_MS = 60 * 1000L;
    public static final long HOUR_TIME_MS = 60 * MINUTE_TIME_MS;

    // HTTP 状态码
    public static final int STATUS_SUCCESS = 0;
    public static final int STATUS_ERROR = 1;
}
```

---

## 配置类

### CommonConfiguration - 通用配置

```java
@Configuration
public class CommonConfiguration {
    // 通用 Bean 配置
    @Bean
    public Jackson2ObjectMapperBuilderCustomizer customizer() {
        // ...
    }
}
```

---

## 生命周期管理

### ServerLifeCycleManager - 服务器生命周期

```java
public class ServerLifeCycleManager {
    private static volatile boolean isRunning = false;
    private static volatile long serverStartupTime = 0L;

    // 标记服务运行
    public static void toRunning() {
        isRunning = true;
        serverStartupTime = System.currentTimeMillis();
    }

    // 标记服务停止
    public static boolean toStopped() {
        // ...
    }

    // 检查运行状态
    public static boolean isRunning() {
        return isRunning;
    }
}
```

---

## 测试与质量

### 单元测试

- **测试框架**: JUnit 5
- **测试位置**: `src/test/java/org/apache/dolphinscheduler/common/`
- **覆盖率**: 70%+

### 测试示例

```java
@Test
public void testDateUtils() {
    Date now = new Date();
    String formatted = DateUtils.formatDate(now, "yyyy-MM-dd HH:mm:ss");
    assertNotNull(formatted);
}
```

---

## 依赖关系

### 被依赖方

本模块被几乎所有其他模块依赖：

- `dolphinscheduler-api`
- `dolphinscheduler-master`
- `dolphinscheduler-worker`
- `dolphinscheduler-service`
- `dolphinscheduler-dao`
- 所有插件模块

### 自身依赖

```xml
<dependencies>
    <!-- JSON 处理 -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>

    <!-- 日志 -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
    </dependency>

    <!-- Lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
</dependencies>
```

---

## 常见问题 (FAQ)

### Q1: 如何添加新的枚举类型？

1. 在 `enums/` 包下创建新的枚举类
2. 继承或参考现有枚举的模式
3. 添加枚举值的描述和代码
4. 添加单元测试

### Q2: 如何添加新的工具方法？

1. 在 `utils/` 包下选择合适的工具类
2. 添加静态方法
3. 添加方法注释（JavaDoc）
4. 添加单元测试

### Q3: 如何使用常量？

```java
import org.apache.dolphinscheduler.common.constants.Constants;

public class Example {
    public void example() {
        long oneHour = Constants.HOUR_TIME_MS;
    }
}
```

---

## 相关文件清单

```
dolphinscheduler-common/
└── src/main/java/org/apache/dolphinscheduler/common/
    ├── enums/                        # 枚举定义
    │   ├── CommandType.java
    │   ├── TaskType.java
    │   ├── ExecutionStatus.java
    │   ├── WorkflowExecutionStatus.java
    │   └── ...
    ├── utils/                        # 工具类
    │   ├── DateUtils.java
    │   ├── JSONUtils.java
    │   ├── StringUtils.java
    │   ├── FileUtils.java
    │   └── ...
    ├── model/                        # 数据模型
    │   ├── Command.java
    │   └── ...
    ├── constants/                    # 常量定义
    │   ├── Constants.java
    │   └── ...
    ├── config/                       # 配置类
    │   └── CommonConfiguration.java
    ├── exception/                    # 异常类
    │   └── ...
    └── lifecycle/                    # 生命周期管理
        └── ServerLifeCycleManager.java
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 13:07:02 CST
