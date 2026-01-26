[根目录](../CLAUDE.md) > **dolphinscheduler-dao**

---

# dolphinscheduler-dao 模块文档

> 最后更新：2026-01-26 14:05:25 CST

---

## 变更记录 (Changelog)

### 2026-01-26 14:05:25 CST
- 初始化模块文档
- 识别数据模型与 MyBatis 映射
- 记录数据库 Schema 与迁移

---

## 模块职责

`dolphinscheduler-dao` 是 **数据访问层模块**，提供数据库操作的封装，使用 MyBatis Plus 作为 ORM 框架。

### 核心功能

- **实体定义**：数据库表对应的实体类
- **Mapper 接口**：MyBatis Mapper 接口
- **SQL 映射**：MyBatis XML 映射文件
- **数据库初始化**：创建表和初始化数据
- **数据库升级**：版本升级脚本

---

## 数据库支持

### 支持的数据库

- **MySQL**: 5.7+
- **PostgreSQL**: 12+
- **H2**: 2.0+ (用于测试)

### 数据库 Schema

```
dolphinscheduler-dao/src/main/resources/sql/
├── dolphinscheduler_mysql.sql         # MySQL 建表脚本
├── dolphinscheduler_postgresql.sql    # PostgreSQL 建表脚本
├── dolphinscheduler_h2.sql            # H2 建表脚本
└── upgrade/                           # 升级脚本
    ├── 1.2.0_schema/
    ├── 1.3.0_schema/
    ├── 2.0.0_schema/
    ├── 3.0.0_schema/
    └── 3.4.0_schema/
```

---

## 核心实体

### 工作流相关

| 实体类 | 表名 | 说明 |
|-------|------|------|
| `WorkflowDefinition` | `t_ds_workflow_definition` | 工作流定义 |
| `WorkflowInstance` | `t_ds_workflow_instance` | 工作流实例 |
| `WorkflowRelation` | `t_ds_workflow_relation_line` | 工作流关系线 |

### 任务相关

| 实体类 | 表名 | 说明 |
|-------|------|------|
| `TaskDefinition` | `t_ds_task_definition` | 任务定义 |
| `TaskInstance` | `t_ds_task_instance` | 任务实例 |
| `ProcessTaskRelation` | `t_ds_process_task_relation` | 任务关系 |

### 项目与用户

| 实体类 | 表名 | 说明 |
|-------|------|------|
| `Project` | `t_ds_project` | 项目 |
| `User` | `t_ds_user` | 用户 |
| `Tenant` | `t_ds_tenant` | 租户 |
| `UserGroup` | `t_ds_user_group` | 用户组 |

### 数据源与资源

| 实体类 | 表名 | 说明 |
|-------|------|------|
| `DataSource` | `t_ds_datasource` | 数据源 |
| `Resource` | `t_ds_resources` | 资源文件 |
| `UdfFunc` | `t_ds_udfs` | UDF 函数 |

### 调度与告警

| 实体类 | 表名 | 说明 |
|-------|------|------|
| `Schedule` | `t_ds_schedules` | 调度配置 |
| `Command` | `t_ds_command` | 命令 |
| `AlertGroup` | `t_ds_alertgroup` | 告警组 |

### 队列与 Worker

| 实体类 | 表名 | 说明 |
|-------|------|------|
| `Queue` | `t_ds_queue` | 队列 |
| `WorkerGroup` | `t_ds_worker_group` | Worker 分组 |

---

## MyBatis Mapper

### 核心 Mapper

| Mapper | 功能 |
|--------|------|
| `WorkflowDefinitionMapper` | 工作流定义 CRUD |
| `WorkflowInstanceMapper` | 工作流实例 CRUD |
| `TaskDefinitionMapper` | 任务定义 CRUD |
| `TaskInstanceMapper` | 任务实例 CRUD |
| `ProjectMapper` | 项目 CRUD |
| `UserMapper` | 用户 CRUD |
| `DataSourceMapper` | 数据源 CRUD |
| `ResourceMapper` | 资源文件 CRUD |
| `CommandMapper` | 命令 CRUD |

### Mapper 示例

```java
@Mapper
public interface WorkflowDefinitionMapper {
    // 查询工作流定义
    WorkflowDefinition queryByCode(long workflowDefinitionCode);

    // 插入工作流定义
    int insert(WorkflowDefinition workflowDefinition);

    // 更新工作流定义
    int update(WorkflowDefinition workflowDefinition);

    // 删除工作流定义
    int deleteByCode(long workflowDefinitionCode);

    // 分页查询
    List<WorkflowDefinition> queryByCondition(PageInfo pageInfo);
}
```

---

## 数据库配置

### DataSource 配置

```java
@Configuration
public class DataSourceConfiguration {

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.hikari")
    public DataSource dataSource() {
        return DataSourceBuilder.create().type(HikariDataSource.class).build();
    }

    @Bean
    public MybatisSqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) {
        MybatisSqlSessionFactoryBean sqlSessionFactory = new MybatisSqlSessionFactoryBean();
        sqlSessionFactory.setDataSource(dataSource);
        sqlSessionFactory.setMapperLocations(new PathMatchingResourcePatternResolver()
                .getResources("classpath*:mapper/*Mapper.xml"));
        return sqlSessionFactory;
    }
}
```

### 配置文件

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/dolphinscheduler?useUnicode=true&characterEncoding=UTF-8
    username: root
    password: root
    hikari:
      minimum-idle: 5
      maximum-pool-size: 30
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
```

---

## 数据库升级

### 升级脚本

```
dolphinscheduler-dao/src/main/resources/sql/upgrade/
├── 1.2.0_schema/
│   ├── dolphinscheduler_mysql.sql
│   └── dolphinscheduler_postgresql.sql
├── 1.3.0_schema/
│   └── ...
└── 3.4.0_schema/
    └── ...
```

### 升级步骤

1. 备份现有数据库
2. 查看当前版本
3. 依次执行升级脚本
4. 验证升级结果

---

## 目录结构

```
dolphinscheduler-dao/
└── src/main/
    ├── java/org/apache/dolphinscheduler/dao/
    │   ├── entity/                      # 实体类
    │   │   ├── Command.java
    │   │   ├── ProcessInstance.java
    │   │   ├── TaskInstance.java
    │   │   ├── Project.java
    │   │   ├── User.java
    │   │   └── ...
    │   ├── mapper/                      # Mapper 接口
    │   │   ├── CommandMapper.java
    │   │   ├── ProcessInstanceMapper.java
    │   │   ├── TaskInstanceMapper.java
    │   │   ├── ProjectMapper.java
    │   │   ├── UserMapper.java
    │   │   └── ...
    │   ├── repository/                  # 仓储层
    │   │   ├── CommandRepository.java
    │   │   ├── ProcessInstanceRepository.java
    │   │   ├── TaskInstanceRepository.java
    │   │   └── ...
    │   ├── upgrade/                     # 升级工具
    │   │   ├── SchemaUtils.java
    │   │   └── ...
    │   └── utils/                       # 工具类
    └── resources/
        ├── mapper/                      # MyBatis XML 映射
        │   ├── CommandMapper.xml
        │   ├── ProcessInstanceMapper.xml
        │   ├── TaskInstanceMapper.xml
        │   ├── ProjectMapper.xml
        │   ├── UserMapper.xml
        │   └── ...
        └── sql/                         # SQL 脚本
            ├── dolphinscheduler_mysql.sql
            ├── dolphinscheduler_postgresql.sql
            ├── dolphinscheduler_h2.sql
            └── upgrade/                 # 升级脚本
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

    <!-- Task API -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-task-api</artifactId>
    </dependency>

    <!-- DAO Plugin -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-dao-plugin-all</artifactId>
    </dependency>

    <!-- MyBatis Plus -->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus</artifactId>
    </dependency>

    <!-- HikariCP -->
    <dependency>
        <groupId>com.zaxxer</groupId>
        <artifactId>HikariCP</artifactId>
    </dependency>

    <!-- Spring Boot -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
</dependencies>
```

---

## 常见问题 (FAQ)

### Q1: 如何添加新的实体类？

1. 创建实体类（如 `NewEntity.java`）
2. 创建 Mapper 接口（如 `NewEntityMapper.java`）
3. 创建 Mapper XML（如 `NewEntityMapper.xml`）
4. 创建数据库表 SQL
5. 创建 Repository 类（可选）

### Q2: 如何进行数据库升级？

1. 查看当前数据库版本
2. 备份现有数据库
3. 下载新版本的升级脚本
4. 按顺序执行升级脚本
5. 验证升级结果

### Q3: 如何切换数据库？

1. 修改 `application.yaml` 中的数据库配置
2. 切换对应的 DAO 插件（MySQL/PostgreSQL/H2）
3. 执行对应的建表脚本
4. 重启服务

---

## 相关文件清单

```
dolphinscheduler-dao/
└── src/main/
    ├── java/org/apache/dolphinscheduler/dao/
    │   ├── entity/                      # 实体类
    │   ├── mapper/                      # Mapper 接口
    │   ├── repository/                  # 仓储层
    │   └── upgrade/                     # 升级工具
    └── resources/
        ├── mapper/                      # MyBatis XML
        └── sql/                         # SQL 脚本
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 14:05:25 CST
