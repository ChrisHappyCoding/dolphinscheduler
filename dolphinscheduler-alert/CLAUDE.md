[根目录](../CLAUDE.md) > **dolphinscheduler-alert**

---

# dolphinscheduler-alert 模块文档

> 最后更新：2026-01-26 14:05:25 CST

---

## 变更记录 (Changelog)

### 2026-01-26 14:05:25 CST
- 初始化模块文档
- 识别告警服务与插件
- 记录告警渠道与配置

---

## 模块职责

`dolphinscheduler-alert` 是 **告警服务模块**，提供任务失败、成功等事件的告警通知功能。

### 核心功能

- **告警服务**：接收告警请求，发送告警通知
- **告警插件**：支持多种告警渠道（邮件、钉钉、微信等）
- **告警组管理**：管理告警组和告警实例
- **告警日志**：记录告警发送历史
- **高可用**：多 Alert Server 实例支持

---

## 模块结构

```
dolphinscheduler-alert/
├── dolphinscheduler-alert-server/        # 告警服务端
└── dolphinscheduler-alert-plugins/       # 告警示件
    ├── dolphinscheduler-alert-api/       # 告警示件 API
    ├── dolphinscheduler-alert-all/       # 告警示件聚合
    ├── dolphinscheduler-alert-email/     # 邮件告警
    ├── dolphinscheduler-alert-dingtalk/  # 钉钉告警
    ├── dolphinscheduler-alert-feishu/    # 飞书告警
    ├── dolphinscheduler-alert-wechat/    # 微信告警
    ├── dolphinscheduler-alert-slack/     # Slack 告警
    ├── dolphinscheduler-alert-http/      # HTTP 告警
    ├── dolphinscheduler-alert-telegram/  # Telegram 告警
    ├── dolphinscheduler-alert-webexteams/# Webex Teams 告警
    ├── dolphinscheduler-alert-script/    # 脚本告警
    ├── dolphinscheduler-alert-pagerduty/ # PagerDuty 告警
    ├── dolphinscheduler-alert-prometheus/# Prometheus 告警
    └── dolphinscheduler-alert-aliyunVoice/# 阿里云语音告警
```

---

## 入口与启动

### 入口类

```
org.apache.dolphinscheduler.alert.AlertServer
```

### 启动方式

```bash
# Maven 方式
cd dolphinscheduler-alert/dolphinscheduler-alert-server
mvn spring-boot:run

# JAR 方式
java -jar dolphinscheduler-alert-server/target/dolphinscheduler-alert-server-3.4.0-SNAPSHOT.jar

# 脚本方式
./dolphinscheduler-alert-server/bin/start.sh
```

---

## 告警示件

### 支持的告警渠道

| 告警渠道 | 插件模块 | 说明 |
|---------|---------|------|
| **邮件** | `alert-email` | SMTP 邮件发送 |
| **钉钉** | `alert-dingtalk` | 钉钉机器人 |
| **飞书** | `alert-feishu` | 飞书机器人 |
| **企业微信** | `alert-wechat` | 企业微信机器人 |
| **Slack** | `alert-slack` | Slack Webhook |
| **HTTP** | `alert-http` | 通用 HTTP Webhook |
| **Telegram** | `alert-telegram` | Telegram Bot |
| **Webex Teams** | `alert-webexteams` | Webex Teams Webhook |
| **脚本** | `alert-script` | 自定义脚本告警 |
| **PagerDuty** | `alert-pagerduty` | PagerDuty 集成 |
| **Prometheus** | `alert-prometheus` | Prometheus Alertmanager |
| **阿里云语音** | `alert-aliyunVoice` | 阿里云语音电话 |

---

## 告警 API

### AlertPluginManager - 告警示件管理器

```java
public class AlertPluginManager {
    // 获取告警示件
    public AlertChannel getAlertChannel(String alertType);

    // 发送告警
    public AlertResult send(AlertInfo alertInfo);

    // 加载所有告警示件
    public void loadPlugins();
}
```

### AlertChannel - 告警通道接口

```java
public interface AlertChannel {
    // 发送告警
    AlertResult send(AlertInfo alertInfo);

    // 获取告警类型
    String getType();

    // 校验告警配置
    void checkParam(Map<String, String> config);
}
```

---

## RPC 服务

### AlertRpcServer - RPC 服务端

提供 RPC 接口供其他服务调用：

```java
public class AlertRpcServer {
    // 发送告警
    public Map<String, String> sendAlert(AlertInfo alertInfo);

    // 批量发送告警
    public Map<String, String> sendAlertList(List<AlertInfo> alertInfoList);
}
```

---

## 告警组管理

### AlertGroup - 告警组

```java
public class AlertGroup {
    private int id;                      // 告警组 ID
    private String groupName;            // 告警组名称
    private String alertType;            // 告警类型
    private String alertInstanceIds;     // 告警实例 ID 列表
    private ...
}
```

### AlertInstance - 告警实例

```java
public class AlertInstance {
    private int id;                      // 告警实例 ID
    private String alertInstanceName;    // 告警实例名称
    private String alertType;            // 告警类型
    private Map<String, String> params;  // 告警参数
    private ...
}
```

---

## 告警配置示例

### 邮件告警配置

```json
{
  "type": "email",
  "serverHost": "smtp.example.com",
  "serverPort": "25",
  "sender": "noreply@example.com",
  "enableSmtpAuth": true,
  "user": "user@example.com",
  "password": "password",
  "receivers": "user1@example.com,user2@example.com",
  "receiversCc": "cc@example.com"
}
```

### 钉钉告警配置

```json
{
  "type": "dingtalk",
  "webhook": "https://oapi.dingtalk.com/robot/send?access_token=xxx",
  "keyword": "DolphinScheduler",
  "secret": "SECxxxxx",
  "msgType": "text",
  "atMobiles": "13800138000",
  "isAtAll": false
}
```

### 企业微信告警配置

```json
{
  "type": "wechat",
  "webhook": "https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=xxx",
  "msgType": "markdown"
}
```

---

## 告警模板

### 告警内容模板

```java
public class AlertTemplate {
    // 任务成功告警模板
    public String getTaskSuccessTemplate(TaskInstance taskInstance);

    // 任务失败告警模板
    public String getTaskFailedTemplate(TaskInstance taskInstance);

    // 工作流成功告警模板
    public String getWorkflowSuccessTemplate(WorkflowInstance workflowInstance);

    // 工作流失败告警模板
    public String getWorkflowFailedTemplate(WorkflowInstance workflowInstance);
}
```

---

## 监控指标

### AlertServerMetrics - 指标收集

```java
public class AlertServerMetrics {
    // 告警发送总数
    public Counter getAlertSendCount();

    // 告警成功数
    public Counter getAlertSuccessCount();

    // 告警失败数
    public Counter getAlertFailedCount();

    // 告警延迟
    public Timer getAlertLatency();
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

    <!-- DAO -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-dao</artifactId>
    </dependency>

    <!-- Alert Plugin -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-alert-all</artifactId>
    </dependency>

    <!-- Registry -->
    <dependency>
        <groupId>org.apache.dolphinscheduler</groupId>
        <artifactId>dolphinscheduler-registry-all</artifactId>
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
dolphinscheduler-alert/
├── dolphinscheduler-alert-server/
│   └── src/main/java/org/apache/dolphinscheduler/alert/
│       ├── AlertServer.java             # 主入口
│       ├── plugin/                      # 插件管理
│       │   └── AlertPluginManager.java
│       ├── rpc/                         # RPC 服务
│       │   └── AlertRpcServer.java
│       ├── service/                     # 服务层
│       │   ├── AlertBootstrapService.java
│       │   └── AlertHAServer.java
│       └── registry/                    # 注册中心
│           └── AlertRegistryClient.java
└── dolphinscheduler-alert-plugins/
    ├── dolphinscheduler-alert-api/       # 插件 API
    │   └── src/main/java/org/apache/dolphinscheduler/plugin/alert/
    │       └── api/
    │           ├── AlertChannel.java    # 告警通道接口
    │           ├── AlertInfo.java       # 告警信息
    │           └── AlertResult.java     # 告警结果
    └── [各告警示件模块]
```

---

## 常见问题 (FAQ)

### Q1: 如何添加新的告警渠道？

1. 在 `dolphinscheduler-alert-plugins` 下创建新模块
2. 实现 `AlertChannel` 接口
3. 在 `AlertPluginManager` 中注册
4. 添加前端配置表单
5. 编写单元测试

### Q2: 如何配置告警组？

1. 在 Web UI 的"告警组管理"中创建告警组
2. 添加告警实例（邮件、钉钉等）
3. 在工作流或任务中选择告警组
4. 配置告警策略（失败、成功、全部）

### Q3: 告警发送失败如何处理？

- 查看告警日志
- 检查告警配置（邮箱、Webhook 等）
- 检查网络连接
- 查看告警插件日志

---

## 相关文件清单

```
dolphinscheduler-alert/
├── dolphinscheduler-alert-server/       # 告警服务端
│   └── src/main/java/org/apache/dolphinscheduler/alert/
│       ├── AlertServer.java
│       ├── plugin/
│       ├── rpc/
│       ├── service/
│       └── registry/
└── dolphinscheduler-alert-plugins/      # 告警示件
    ├── dolphinscheduler-alert-api/      # 插件 API
    ├── dolphinscheduler-alert-email/    # 邮件告警
    ├── dolphinscheduler-alert-dingtalk/ # 钉钉告警
    ├── dolphinscheduler-alert-feishu/   # 飞书告警
    ├── dolphinscheduler-alert-wechat/   # 微信告警
    └── [其他告警示件]
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 14:05:25 CST
