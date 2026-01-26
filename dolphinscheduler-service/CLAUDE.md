[根目录](../CLAUDE.md) > **dolphinscheduler-service**

---

# dolphinscheduler-service 模块文档

> 最后更新：2026-01-26 14:05:25 CST

---

## 变更记录 (Changelog)

### 2026-01-26 14:05:25 CST
- 初始化模块文档
- 识别业务逻辑服务
- 记录服务层架构

---

## 模块职责

`dolphinscheduler-service` 是 **业务逻辑服务层模块**，提供核心业务逻辑的实现，介于 DAO 层和 Controller 层之间。

### 核心功能

- **工作流服务**：工作流定义、实例管理
- **任务服务**：任务定义、实例管理
- **项目服务**：项目管理
- **用户服务**：用户、租户管理
- **数据源服务**：数据源管理
- **资源服务**：资源文件管理
- **调度服务**：调度配置管理
- **告警服务**：告警组管理

---

## 核心服务

### WorkflowService - 工作流服务

```java
@Service
public class WorkflowService {
    // 创建工作流定义
    public WorkflowDefinition createWorkflowDefinition(WorkflowDefinitionDTO dto);

    // 更新工作流定义
    public WorkflowDefinition updateWorkflowDefinition(WorkflowDefinitionDTO dto);

    // 删除工作流定义
    public void deleteWorkflowDefinition(long workflowDefinitionCode);

    // 发布工作流
    public void publishWorkflowDefinition(long workflowDefinitionCode);

    // 查询工作流定义
    public WorkflowDefinition queryWorkflowDefinition(long workflowDefinitionCode);

    // 启动工作流实例
    public WorkflowInstance startWorkflowInstance(StartWorkflowInstanceCommand command);
}
```

### TaskService - 任务服务

```java
@Service
public class TaskService {
    // 创建任务定义
    public TaskDefinition createTaskDefinition(TaskDefinitionDTO dto);

    // 更新任务定义
    public TaskDefinition updateTaskDefinition(TaskDefinitionDTO dto);

    // 删除任务定义
    public void deleteTaskDefinition(long taskDefinitionCode);

    // 查询任务定义
    public TaskDefinition queryTaskDefinition(long taskDefinitionCode);

    // 查询任务实例
    public TaskInstance queryTaskInstance(long taskInstanceId);
}
```

### ProjectService - 项目服务

```java
@Service
public class ProjectService {
    // 创建项目
    public Project createProject(ProjectDTO dto);

    // 更新项目
    public Project updateProject(ProjectDTO dto);

    // 删除项目
    public void deleteProject(long projectCode);

    // 查询项目
    public Project queryProject(long projectCode);

    // 查询项目列表
    public List<Project> queryProjectList(PageInfo pageInfo);
}
```

### DataSourceService - 数据源服务

```java
@Service
public class DataSourceService {
    // 创建数据源
    public DataSource createDataSource(DataSourceDTO dto);

    // 更新数据源
    public DataSource updateDataSource(DataSourceDTO dto);

    // 删除数据源
    public void deleteDataSource(int dataSourceId);

    // 查询数据源
    public DataSource queryDataSource(int dataSourceId);

    // 测试连接
    public boolean checkConnection(DataSourceDTO dto);
}
```

---

## 调度服务

### ScheduleService - 调度服务

```java
@Service
public class ScheduleService {
    // 创建调度配置
    public Schedule createSchedule(ScheduleDTO dto);

    // 更新调度配置
    public Schedule updateSchedule(ScheduleDTO dto);

    // 删除调度配置
    public void deleteSchedule(int scheduleId);

    // 发布调度
    public void publishSchedule(int scheduleId);

    // 下线调度
    public void offlineSchedule(int scheduleId);

    // 查询调度列表
    public List<Schedule> queryScheduleList(long workflowDefinitionCode);
}
```

---

## 权限服务

### PermissionService - 权限服务

```java
@Service
public class PermissionService {
    // 创建用户
    public User createUser(UserDTO dto);

    // 创建租户
    public Tenant createTenant(TenantDTO dto);

    // 授予权限
    public void grantPermission(long userId, long projectCode, String permission);

    // 撤销权限
    public void revokePermission(long userId, long projectCode, String permission);

    // 检查权限
    public boolean checkPermission(long userId, long projectCode, String permission);
}
```

---

## Executor 服务

### ExecutorService - 执行器服务

```java
@Service
public class ExecutorService {
    // 执行工作流实例
    public void execWorkflowInstance(Command command, WorkflowInstance workflowInstance);

    // 停止工作流实例
    public void stopWorkflowInstance(long workflowInstanceId);

    // 暂停工作流实例
    public void pauseWorkflowInstance(long workflowInstanceId);

    // 恢复工作流实例
    public void recoverWorkflowInstance(long workflowInstanceId);

    // 查询工作流实例状态
    public WorkflowInstanceStatus queryWorkflowInstanceStatus(long workflowInstanceId);
}
```

---

## 资源服务

### ResourceService - 资源服务

```java
@Service
public class ResourceService {
    // 上传资源文件
    public Resource uploadResource(MultipartFile file, String resourceType);

    // 更新资源文件
    public Resource updateResource(int resourceId, MultipartFile file);

    // 删除资源文件
    public void deleteResource(int resourceId);

    // 查询资源文件
    public Resource queryResource(int resourceId);

    // 查询资源列表
    public List<Resource> queryResourceList(ResourceQueryCondition condition);

    // 下载资源文件
    public InputStream downloadResource(int resourceId);
}
```

---

## 告警服务

### AlertGroupService - 告警组服务

```java
@Service
public class AlertGroupService {
    // 创建告警组
    public AlertGroup createAlertGroup(AlertGroupDTO dto);

    // 更新告警组
    public AlertGroup updateAlertGroup(AlertGroupDTO dto);

    // 删除告警组
    public void deleteAlertGroup(int alertGroupId);

    // 查询告警组
    public AlertGroup queryAlertGroup(int alertGroupId);

    // 查询告警组列表
    public List<AlertGroup> queryAlertGroupList(PageInfo pageInfo);
}
```

---

## 目录结构

```
dolphinscheduler-service/
└── src/main/java/org/apache/dolphinscheduler/service/
    ├── workflow/                       # 工作流服务
    │   ├── WorkflowService.java
    │   └── WorkflowInstanceService.java
    ├── task/                           # 任务服务
    │   ├── TaskService.java
    │   └── TaskInstanceService.java
    ├── project/                        # 项目服务
    │   └── ProjectService.java
    ├── datasource/                     # 数据源服务
    │   └── DataSourceService.java
    ├── resource/                       # 资源服务
    │   └── ResourceService.java
    ├── schedule/                       # 调度服务
    │   └── ScheduleService.java
    ├── alert/                          # 告警服务
    │   └── AlertGroupService.java
    ├── permission/                     # 权限服务
    │   ├── UserService.java
    │   ├── TenantService.java
    │   └── PermissionService.java
    ├── executor/                       # 执行器服务
    │   └── ExecutorService.java
    └── quartz/                         # Quartz 调度
        └── QuartzExecutors.java
```

---

## 关键依赖与配置

### Maven 依赖

```xml
<dependencies>
    <!-- DAO -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-dao</artifactId>
    </dependency>

    <!-- SPI -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-spi</artifactId>
    </dependency>

    <!-- Registry API -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-registry-api</artifactId>
    </dependency>

    <!-- Task API -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-task-api</artifactId>
    </dependency>

    <!-- Extract Master/Worker -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-extract-master</artifactId>
    </dependency>

    <!-- Cron Utils -->
    <dependency>
        <groupId>com.cronutils</groupId>
        <artifactId>cron-utils</artifactId>
    </dependency>

    <!-- Micrometer -->
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-core</artifactId>
    </dependency>
</dependencies>
```

---

## 常见问题 (FAQ)

### Q1: 如何添加新的服务？

1. 在对应的包下创建服务接口
2. 创建服务实现类
3. 使用 `@Service` 注解
4. 注入对应的 Repository/Mapper
5. 实现业务逻辑
6. 添加单元测试

### Q2: 如何处理事务？

使用 Spring `@Transactional` 注解：

```java
@Service
public class WorkflowService {

    @Transactional(rollbackFor = Exception.class)
    public WorkflowDefinition createWorkflowDefinition(WorkflowDefinitionDTO dto) {
        // 业务逻辑
    }
}
```

---

## 相关文件清单

```
dolphinscheduler-service/
└── src/main/java/org/apache/dolphinscheduler/service/
    ├── workflow/                       # 工作流服务
    ├── task/                           # 任务服务
    ├── project/                        # 项目服务
    ├── datasource/                     # 数据源服务
    ├── resource/                       # 资源服务
    ├── schedule/                       # 调度服务
    ├── alert/                          # 告警服务
    ├── permission/                     # 权限服务
    └── executor/                       # 执行器服务
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 14:05:25 CST
