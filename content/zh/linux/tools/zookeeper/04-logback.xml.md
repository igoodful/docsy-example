---
title: 4. logback.xml
description: logback.xml
date: 2023-11-05
weight: 400
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}

**占用端口**

| 端口 | 使用说明   |
| :--- | :--------- |
| 2181 | 访问       |
| 2888 | 内部端口， |
| 3888 | 内部端口， |

## 1. logback.xml



```xml
<configuration scan="false">
    <property name="zookeeper.console.threshold" value="INFO" />
    <property name="zookeeper.log.dir" value="/data/zookeeperdata/log" />
    <property name="zookeeper.log.file" value="zookeeper-${USER}-server-${HOSTNAME}.log" />
    <property name="zookeeper.log.threshold" value="INFO" />

    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${zookeeper.log.dir}/${zookeeper.log.file}</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <fileNamePattern>${zookeeper.log.dir}/${zookeeper.log.file}_%d{yyyy-MM-dd}</fileNamePattern>
                <maxHistory>30</maxHistory>
                <cleanHistoryOnStart>true</cleanHistoryOnStart>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n%ex{full}</pattern>
        </encoder>
    </appender>

    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %highlight(%-5level) %logger{50} - %msg%n%ex{full}</pattern>
        </encoder>
    </appender>


    <root level="INFO">
        <appender-ref ref="FILE" />
    </root>
</configuration>

```


以下是您提供的 `logback.xml` 配置文件的逐行详解，并指出潜在问题和优化建议：

---

### **1. 全局配置**
```xml
<configuration scan="true" scanPeriod="120 seconds" debug="false">
```
- **作用**：
  - `scan="true"`：开启配置文件自动扫描，修改后自动重新加载。
  - `scanPeriod="120 seconds"`：每120秒检查一次配置文件变更。
  - `debug="false"`：关闭 Logback 内部调试日志，避免干扰业务日志。

---

### **2. 自定义属性**
```xml
<property name="zookeeper.console.threshold" value="INFO" />
<property name="zookeeper.log.dir" value="/data/zookeeperdata/log" />
<property name="zookeeper.log.file" value="zookeeper-${USER}-server-${HOSTNAME}.log" />
<property name="zookeeper.log.threshold" value="INFO" />
<property name="zookeeper.log.maxfilesize" value="2MB" />
<property name="zookeeper.log.maxbackupindex" value="5" />
```
- **解析**：
  - `zookeeper.log.file` 使用 `${USER}` 和 `${HOSTNAME}` 动态插入当前用户和主机名，生成唯一日志文件名（如 `zookeeper-john-server-node1.log`）。
  - `zookeeper.log.maxfilesize` 和 `zookeeper.log.maxbackupindex` 定义了文件大小滚动策略的参数，但当前配置未直接使用（需结合 `SizeBasedTriggeringPolicy`）。
- **注意**：
  - `${USER}` 和 `${HOSTNAME}` 依赖系统环境变量，需确保运行环境中已正确设置，否则可能解析为空白或占位符文本。
  - 建议为环境变量设置默认值（如 `${HOSTNAME:-unknown-host}`）。

---

### **3. 文件型 Appender 配置**
```xml
<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${zookeeper.log.dir}/${zookeeper.log.file}</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <fileNamePattern>${zookeeper.log.dir}/${zookeeper.log.file}.%d{yyyy-MM-dd-HH-mm}</fileNamePattern>
        <maxHistory>60</maxHistory>
        <cleanHistoryOnStart>true</cleanHistoryOnStart>
    </rollingPolicy>
    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
        <charset>UTF-8</charset>
        <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
    </encoder>
</appender>
```
#### **关键点解析**
1. **`<file>`**
   - 当前活动日志文件路径：`/data/zookeeperdata/log/zookeeper-${USER}-server-${HOSTNAME}.log`。
   - **注意**：如果 `fileNamePattern` 中没有 `%i`，则按时间滚动时会直接重命名此文件。

2. **`<fileNamePattern>`**
   - 滚动后的文件名格式示例：
     `zookeeper-john-server-node1.log.2023-10-15-14-30`。
   - **问题**：
     - `%d{yyyy-MM-dd-HH-mm}` 按分钟滚动，可能导致日志文件过多（每小时60个文件）。
     - 未配置 `%i` 索引，无法按文件大小滚动（即使定义了 `maxfilesize=2MB`）。

3. **`<maxHistory>`**
   - 保留最近60个归档文件（若按分钟滚动，仅保留1小时的日志）。
   - **建议**：根据业务需求调整滚动周期（如按天滚动 `%d{yyyy-MM-dd}` + `maxHistory=30`）。

4. **`<cleanHistoryOnStart>`**
   - 应用启动时清理过期日志文件，避免磁盘空间不足。

5. **`<encoder>`**
   - 日志格式包含时间戳、线程名、日志级别、Logger 名称（最长50字符）和消息。
   - **优化建议**：
     - 添加高亮或颜色（控制台场景）：`%highlight(%-5level)`。
     - 避免高开销占位符（如 `%caller`, `%L`）。

---

### **4. Root Logger 配置**
```xml
<root level="INFO">
    <appender-ref ref="FILE" />
</root>
```
- **作用**：所有 INFO 及以上级别的日志输出到 `FILE` Appender。
- **潜在问题**：未配置控制台输出（`ConsoleAppender`），调试时可能无法查看日志。

---

### **5. 配置问题与优化建议**

#### **问题1：滚动策略未按文件大小触发**
- **现象**：配置了 `zookeeper.log.maxfilesize=2MB`，但未启用 `SizeBasedTriggeringPolicy`，实际不会按文件大小滚动。
- **修复**：
  修改滚动策略为 `SizeAndTimeBasedRollingPolicy`（需 Logback 版本 ≥1.1.7）：
  ```xml
  <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
      <fileNamePattern>${zookeeper.log.dir}/${zookeeper.log.file}.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
      <maxFileSize>${zookeeper.log.maxfilesize}</maxFileSize>
      <maxHistory>60</maxHistory>
      <totalSizeCap>1GB</totalSizeCap>
  </rollingPolicy>
  ```

#### **问题2：滚动周期过短**
- **现象**：按分钟滚动（`%d{yyyy-MM-dd-HH-mm}`）可能导致文件碎片化。
- **建议**：按小时或天滚动，例如：
  ```xml
  <fileNamePattern>${zookeeper.log.dir}/${zookeeper.log.file}.%d{yyyy-MM-dd-HH}.log</fileNamePattern>
  ```

#### **问题3：未启用异步日志**
- **现象**：高并发场景下同步写文件可能阻塞线程。
- **建议**：添加 `AsyncAppender`：
  ```xml
  <appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
      <queueSize>512</queueSize>
      <appender-ref ref="FILE" />
  </appender>
  <root level="INFO">
      <appender-ref ref="ASYNC" />
  </root>
  ```

#### **问题4：缺乏异常堆栈配置**
- **现象**：当前 `pattern` 中未配置 `%ex`，异常堆栈可能丢失。
- **修复**：
  ```xml
  <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n%ex{full}</pattern>
  ```

---

### **6. 完整优化配置示例**
```xml
<configuration scan="true" scanPeriod="120 seconds" debug="false">
    <property name="zookeeper.console.threshold" value="INFO" />
    <property name="zookeeper.log.dir" value="/data/zookeeperdata/log" />
    <property name="zookeeper.log.file" value="zookeeper-${USER:-unknown}-server-${HOSTNAME:-unknown-host}.log" />
    <property name="zookeeper.log.maxfilesize" value="2MB" />
    <property name="zookeeper.log.maxbackupindex" value="5" />

    <!-- 异步日志 -->
    <appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
        <queueSize>512</queueSize>
        <appender-ref ref="FILE" />
    </appender>

    <!-- 文件输出（按时间和大小滚动） -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${zookeeper.log.dir}/${zookeeper.log.file}</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>${zookeeper.log.dir}/${zookeeper.log.file}.%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <maxFileSize>${zookeeper.log.maxfilesize}</maxFileSize>
            <maxHistory>30</maxHistory>
            <totalSizeCap>10GB</totalSizeCap>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>UTF-8</charset>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n%ex{5}</pattern>
        </encoder>
    </appender>

    <!-- 控制台输出（可选） -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss} %highlight(%-5level) %cyan(%logger{20}) - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="ASYNC" />
        <appender-ref ref="CONSOLE" /> <!-- 按需开启 -->
    </root>
</configuration>
```

---

### **总结**
您的配置实现了基于时间和文件名的动态日志滚动，但需注意以下改进：
1. **滚动策略**：结合时间和大小滚动，避免文件碎片化。
2. **异常堆栈**：显式配置 `%ex` 输出异常信息。
3. **环境变量容错**：为 `${USER}` 和 `${HOSTNAME}` 设置默认值。
4. **性能优化**：启用异步日志和合理的滚动周期。









