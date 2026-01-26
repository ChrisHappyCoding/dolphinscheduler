[根目录](../CLAUDE.md) > **dolphinscheduler-api**

---

# dolphinscheduler-api 模块文档

> 最后更新：2026-01-26 13:07:02 CST

---

## 变更记录 (Changelog)

### 2026-01-26 13:07:02 CST
- 初始化模块文档
- 识别核心接口与服务
- 记录主要 Controller 和 Service

---

## 模块职责

`dolphinscheduler-api` 是 **REST API 服务模块**，提供 Web UI 的后端接口支持。

### 核心功能

- **工作流管理**：工作流定义的 CRUD、版本管理、发布/下线
- **任务实例管理**：工作流实例的启动、停止、暂停、恢复
- **任务管理**：任务定义的 CRUD、任务类型配置
- **数据源管理**：数据源的 CRUD、连接测试
- **用户权限管理**：用户、租户、告警组、队列管理
- **监控统计**：项目统计、任务统计、资源监控
- **日志查询**：任务日志查看与下载

---

## 入口与启动

### 入口类

```
org.apache.dolphinscheduler.api.ApiApplicationServer
```

### 启动方式

```bash
# Maven 方式
cd dolphinscheduler-api
mvn spring-boot:run

# JAR 方式
java -jar dolphinscheduler-api/target/dolphinscheduler-api-3.4.0-SNAPSHOT.jar

# 脚本方式
./dolphinscheduler-api/bin/start.sh
```

### 访问地址

```
http://localhost:12345/dolphinscheduler
```

### 默认账号

```
用户名: admin
密码: dolphinscheduler123
```

---

## 对外接口

### 核心 Controller

| Controller | 路径 | 功能描述 |
|-----------|------|---------|
| **WorkflowDefinitionController** | `/projects/{projectCode}/workflow/definition` | 工作流定义管理 |
| **WorkflowInstanceController** | `/projects/{projectCode}/workflow/instance` | 工作流实例管理 |
| **TaskDefinitionController** | `/projects/{projectCode}/task/definition` | 任务定义管理 |
| **TaskInstanceController** | `/projects/{projectCode}/task/instance` | 任务实例管理 |
| **DataSourceController** | `/datasource` | 数据源管理 |
| **ProjectController** | `/projects` | 项目管理 |
| **UsersController** | `/users` | 用户管理 |
| **ResourcesController** | `/resources` | 资源文件管理 |
| **AlertGroupController** | `/alert-group` | 告警组管理 |
| **MonitorController** | `/monitor` | 监控数据 |
| **QueueController** | `/queue` | 队列管理 |
| **TenantController** | `/tenants` | 租户管理 |
| **WorkerGroupController** | `/worker-group` | Worker 分组管理 |
| **UiPluginController** | `/ui-plugin` | UI 插件管理 |

### V2 API (新版本)

| Controller | 路径 | 功能描述 |
|-----------|------|---------|
| **WorkflowV2** | `/v2/projects/{projectCode}/workflows` | 工作流管理 V2 |
| **WorkflowInstanceV2** | `/v2/projects/{projectCode}/workflow-instances` | 工作流实例管理 V2 |
| **TaskInstanceV2** | `/v2/projects/{projectCode}/task-instances` | 任务实例管理 V2 |
| **ProjectV2** | `/v2/projects` | 项目管理 V2 |
| **AccessTokenV2** | `/v2/access-tokens` | 访问令牌管理 V2 |
| **QueueV2** | `/v2/queues` | 队列管理 V2 |

### RPC 服务

API Server 通过 RPC 调用 Master 和 Worker 服务：

```
org.apache.dolphinscheduler.api.service.ExecutorService
├── execWorkflowInstance()  // 执行工作流实例
├── startCheckWorkflowDefinition()  // 启动工作流定义检查
└── ...
```

---

## 关键依赖与配置

### Maven 依赖

```xml
<dependencies>
    <!-- 核心服务层 -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-service</artifactId>
    </dependency>

    <!-- DAO 层 -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-dao</artifactId>
    </dependency>

    <!-- SPI -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-spi</artifactId>
    </dependency>

    <!-- Spring Boot -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- MyBatis -->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
    </dependency>
</dependencies>
```

### 关键配置文件

#### application.yaml

```yaml
server:
  port: 12345
  servlet:
    context-path: /dolphinscheduler

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/dolphinscheduler
    username: root
    password: root
  mvc:
    pathmatch:
      matching-strategy: ant_path_matcher

# API 配置
api:
  # 服务器地址
  server:
    port: 12345
  # 会话超时时间（小时）
  session:
    timeout: 24
  # CORS 配置
  cors:
    enabled: true
    allowed-origins: "*"
```

---

## 数据模型

### 核心实体

| 实体 | 表名 | 说明 |
|-----|------|------|
| `WorkflowDefinition` | `t_ds_workflow_definition` | 工作流定义 |
| `WorkflowInstance` | `t_ds_workflow_instance` | 工作流实例 |
| `TaskDefinition` | `t_ds_task_definition` | 任务定义 |
| `TaskInstance` | `t_ds_task_instance` | 任务实例 |
| `Project` | `t_ds_project` | 项目 |
| `User` | `t_ds_user` | 用户 |
| `DataSource` | `t_ds_datasource` | 数据源 |
| `Resource` | `t_ds_resources` | 资源文件 |

### DTO 对象

- `WorkflowDefinitionDTO`：工作流定义数据传输对象
- `WorkflowInstanceDTO`：工作流实例数据传输对象
- `TaskDefinitionDTO`：任务定义数据传输对象
- `DataSourceDTO`：数据源数据传输对象
- `PageResult<T>`：分页结果对象

---

## 测试与质量

### 单元测试

- **测试框架**: JUnit 5 + Mockito
- **测试位置**: `src/test/java/org/apache/dolphinscheduler/api/`
- **覆盖率**: 60%+

### 测试示例

```java
@SpringBootTest
@AutoConfigureMockMvc
public class WorkflowDefinitionControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testCreateWorkflowDefinition() throws Exception {
        mockMvc.perform(post("/projects/{projectCode}/workflow/definition", 123456)
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"name\":\"test-workflow\"}"))
                .andExpect(status().isCreated());
    }
}
```

### 集成测试

- **API 测试**: `dolphinscheduler-api-test` 模块
- **测试工具**: RestAssured
- **测试范围**: 完整的 API 端到端测试

---

## 架构设计

### 分层架构

```
┌─────────────────────────────────────┐
│         Controller Layer            │  接口层
│  (处理 HTTP 请求，参数验证)          │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│          Service Layer              │  业务逻辑层
│  (业务逻辑处理，事务管理)            │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│           DAO Layer                 │  数据访问层
│  (数据库操作，MyBatis 映射)         │
└─────────────────────────────────────┘
```

### 核心包结构

```
org.apache.dolphinscheduler.api/
├── controller/              # Controller 层
│   ├── v2/                 # V2 API
│   └── ...
├── service/                # Service 层
│   ├── impl/              # Service 实现
│   └── ...
├── executor/               # RPC 执行器
│   ├── workflow/          # 工作流执行
│   └── task/              # 任务执行
├── dto/                    # 数据传输对象
├── exceptions/             # 异常处理
├── configuration/          # 配置类
├── interceptor/            # 拦截器
├── utils/                  # 工具类
└── permission/             # 权限检查
```

---

## 常见问题 (FAQ)

### Q1: 如何添加新的 API 接口？

1. 在 `controller` 包下创建新的 Controller
2. 使用 `@RestController` 和 `@RequestMapping` 注解
3. 注入对应的 Service
4. 在 `service` 包下实现业务逻辑
5. 添加单元测试
6. 更新 API 文档

### Q2: 如何进行权限控制？

使用 `@ApiAccess` 注解：

```java
@PostMapping
@ApiAccess(accessLevel = AccessLevel.API_WRITE)
public Result create(@PathVariable long projectCode, @RequestBody WorkflowDefinitionDTO dto) {
    // ...
}
```

### Q3: 如何处理异常？

使用统一的异常处理：

```java
@RestControllerAdvice
public class ApiExceptionHandler {

    @ExceptionHandler(Exception.class)
    public Result handleException(Exception e) {
        return Result.error(e.getMessage());
    }
}
```

### Q4: 如何调用 Master/Worker RPC 服务？

通过 `ExecutorService` 调用：

```java
@Autowired
private ExecutorService executorService;

public void execWorkflowInstance(Command command) {
    executorService.execWorkflowInstance(command, workflowInstance);
}
```

---

## 相关文件清单

### 主要源文件

```
dolphinscheduler-api/
├── src/main/java/org/apache/dolphinscheduler/api/
│   ├── ApiApplicationServer.java              # 入口类
│   ├── configuration/                         # 配置类
│   │   ├── ApiConfig.java
│   │   └── ...
│   ├── controller/                            # Controller
│   │   ├── v2/
│   │   │   ├── WorkflowV2.java
│   │   │   └── ...
│   │   ├── WorkflowDefinitionController.java
│   │   ├── WorkflowInstanceController.java
│   │   └── ...
│   ├── service/                               # Service
│   │   ├── WorkflowDefinitionService.java
│   │   ├── WorkflowInstanceService.java
│   │   └── ...
│   ├── dto/                                   # DTO
│   │   ├── WorkflowDefinitionDTO.java
│   │   └── ...
│   ├── exceptions/                            # 异常处理
│   │   └── ApiExceptionHandler.java
│   ├── interceptor/                           # 拦截器
│   │   ├── LoginHandlerInterceptor.java
│   │   └── ...
│   └── permission/                            # 权限检查
└── src/main/resources/
    ├── application.yaml                       # 配置文件
    └── i18n/                                  # 国际化
```

### 测试文件

```
dolphinscheduler-api/src/test/java/org/apache/dolphinscheduler/api/
├── controller/                                # Controller 测试
│   ├── WorkflowDefinitionControllerTest.java
│   └── ...
├── service/                                   # Service 测试
│   └── ...
└── AbstractControllerTest.java               # 测试基类
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 13:07:02 CST
