---
title: milliseconds ago
description: The last packet successfully received from the server was 104,793,784 milliseconds ago
date: 2023-10-24
weight: 70000


---

{{< alert >}}
- docker version > 17


{{< /alert >}}

###

打开浏览器，访问以下入口URL并登陆，即可进入GreatDTS服务页面。

{{<warning>}}
进程的用户
{{</warning>}}



```sql
2023-12-26 13:46:15,322 INFO  [7af086c1a305a4595d0cd7cfb8e71779] com.greatsync.connector.jdbc.internal.multithread.SingleThreadBatchTask - [SingleThreadBatchTask-(1/6)-(1/1)][exe-id:487486]JDBC execute batch success, time costs:0.0s, size:0
2023-12-26 13:46:16,498 INFO  [a248af3cd2bf8ea10d79d5c037c8f5e8] com.greatsync.connector.jdbc.internal.multithread.SingleThreadBatchTask - [SingleThreadBatchTask-(6/6)-(1/1)]Flushing triggered [by:Schedule with fixed Interval[5000]], current batch count:1.
2023-12-26 13:46:16,498 INFO  [a248af3cd2bf8ea10d79d5c037c8f5e8] com.greatsync.connector.jdbc.internal.multithread.SingleThreadBatchTask - [SingleThreadBatchTask-(6/6)-(1/1)][exe-id:7]JDBC execute batch started at 2023-12-26T13:46:16.498549+08:00[GMT+08:00], size:1
2023-12-26 13:46:16,499 ERROR [a248af3cd2bf8ea10d79d5c037c8f5e8] com.greatsync.connector.jdbc.internal.multithread.SingleThreadBatchTask - [SingleThreadBatchTask-(6/6)-(1/1)][exe-id:7]JDBC execute batch error cause of the exception cannot be retryable, consider to add the sql state to the properties 'batch.retryable.sql.states' in sink if want to retry after the error occurs. time costs:0.001s, batchCount:1, sql state:08S01
java.sql.SQLException: JDBC execute batch error with the root cause: The last packet successfully received from the server was 104,793,784 milliseconds ago. The last packet sent successfully to the server was 104,793,787 milliseconds ago. is longer than the server configured value of 'wait_timeout'. You should consider either expiring and/or testing connection validity before use in your application, increasing the server configured values for client timeouts, or using the Connector/J connection property 'autoReconnect=true' to avoid this problem.
Below are error records:

****************************************************
* SQL : INSERT INTO `sub_contract`.`sub_purchase`(`id`, `purchase_name`, `bill_num`, `bill_total_price`, `apply_code`, `apply_name`, `remarks`, `create_user`, `create_dept`, `create_time`, `update_user`, `update_time`, `status`, `is_deleted`, `project_org_name`, `sub_org_company`, `create_user_name`, `company_id`, `project_org_id`, `project_id`, `sub_status`, `bill_type`, `bill_type_info`, `safe_work_proport`, `safe_work_rate`, `bill_total_price_no_rate`, `process_instance_id`) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?) ON DUPLICATE KEY UPDATE `id`=VALUES(`id`), `purchase_name`=VALUES(`purchase_name`), `bill_num`=VALUES(`bill_num`), `bill_total_price`=VALUES(`bill_total_price`), `apply_code`=VALUES(`apply_code`), `apply_name`=VALUES(`apply_name`), `remarks`=VALUES(`remarks`), `create_user`=VALUES(`create_user`), `create_dept`=VALUES(`create_dept`), `create_time`=VALUES(`create_time`), `update_user`=VALUES(`update_user`), `update_time`=VALUES(`update_time`), `status`=VALUES(`status`), `is_deleted`=VALUES(`is_deleted`), `project_org_name`=VALUES(`project_org_name`), `sub_org_company`=VALUES(`sub_org_company`), `create_user_name`=VALUES(`create_user_name`), `company_id`=VALUES(`company_id`), `project_org_id`=VALUES(`project_org_id`), `project_id`=VALUES(`project_id`), `sub_status`=VALUES(`sub_status`), `bill_type`=VALUES(`bill_type`), `bill_type_info`=VALUES(`bill_type_info`), `safe_work_proport`=VALUES(`safe_work_proport`), `safe_work_rate`=VALUES(`safe_work_rate`), `bill_total_price_no_rate`=VALUES(`bill_total_price_no_rate`), `process_instance_id`=VALUES(`process_instance_id`)
****************************************************
* Failed Records [1]:
+I(1681313686276407298,便道包件4,6,1019732.02,null,null,,1279165024162340866,1403382177337905154,2023-07-18T22:43:15,1279165309031079937,2023-08-17T14:47:22,0,0,郑州建设沁伊高速QYTJ-2标项目经理部,郑州建设公司,吴丽凡,1349699314113904642,null,2106111131191380010,null,2,增值税专票,1.5,1,990031.09,null)

****************************************************

	at com.greatsync.connector.jdbc.internal.executor.TableSimpleStatementExecutor.executeBatch(TableSimpleStatementExecutor.java:105) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.internal.executor.TableBufferReducedStatementExecutor.executeBatch(TableBufferReducedStatementExecutor.java:148) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.internal.multithread.SingleThreadBatchTask.internalAttemptFlush(SingleThreadBatchTask.java:260) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.internal.multithread.SingleThreadBatchTask.internalAttemptFlushAll(SingleThreadBatchTask.java:234) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.internal.multithread.SingleThreadBatchTask.lambda$flush$5(SingleThreadBatchTask.java:377) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:515) [?:?]
	at java.util.concurrent.FutureTask.run(FutureTask.java:264) [?:?]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128) [?:?]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628) [?:?]
	at java.lang.Thread.run(Thread.java:829) [?:?]
Caused by: com.mysql.cj.jdbc.exceptions.CommunicationsException: The last packet successfully received from the server was 104,793,784 milliseconds ago. The last packet sent successfully to the server was 104,793,787 milliseconds ago. is longer than the server configured value of 'wait_timeout'. You should consider either expiring and/or testing connection validity before use in your application, increasing the server configured values for client timeouts, or using the Connector/J connection property 'autoReconnect=true' to avoid this problem.
	at com.mysql.cj.jdbc.exceptions.SQLError.createCommunicationsException(SQLError.java:174) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:64) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.ConnectionImpl.isReadOnly(ConnectionImpl.java:1402) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.ConnectionImpl.isReadOnly(ConnectionImpl.java:1387) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.ClientPreparedStatement.executeBatchInternal(ClientPreparedStatement.java:386) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.StatementImpl.executeBatch(StatementImpl.java:795) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl.executeBatch$original$tFndybU4(FieldNamedPreparedStatementImpl.java:79) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl.executeBatch$original$tFndybU4$accessor$YvZYIDXw(FieldNamedPreparedStatementImpl.java) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl$auxiliary$mexWd3fI.call(Unknown Source) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at org.apache.skywalking.apm.agent.core.plugin.interceptor.enhance.v2.InstMethodsInterV2.intercept(InstMethodsInterV2.java:67) ~[skywalking-agent.jar:8.13.0]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl.executeBatch(FieldNamedPreparedStatementImpl.java) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.internal.executor.TableSimpleStatementExecutor.executeBatch(TableSimpleStatementExecutor.java:95) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	... 9 more
Caused by: com.mysql.cj.exceptions.CJCommunicationsException: The last packet successfully received from the server was 104,793,784 milliseconds ago. The last packet sent successfully to the server was 104,793,787 milliseconds ago. is longer than the server configured value of 'wait_timeout'. You should consider either expiring and/or testing connection validity before use in your application, increasing the server configured values for client timeouts, or using the Connector/J connection property 'autoReconnect=true' to avoid this problem.
	at jdk.internal.reflect.GeneratedConstructorAccessor45.newInstance(Unknown Source) ~[?:?]
	at jdk.internal.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45) ~[?:?]
	at java.lang.reflect.Constructor.newInstance(Constructor.java:490) ~[?:?]
	at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:61) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:105) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:151) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.exceptions.ExceptionFactory.createCommunicationsException(ExceptionFactory.java:167) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.protocol.a.NativeProtocol.send(NativeProtocol.java:628) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.protocol.a.NativeProtocol.sendCommand(NativeProtocol.java:683) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.protocol.a.NativeProtocol.sendCommand(NativeProtocol.java:155) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.NativeSession.queryServerVariable(NativeSession.java:597) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.ConnectionImpl.isReadOnly(ConnectionImpl.java:1394) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.ConnectionImpl.isReadOnly(ConnectionImpl.java:1387) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.ClientPreparedStatement.executeBatchInternal(ClientPreparedStatement.java:386) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.StatementImpl.executeBatch(StatementImpl.java:795) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl.executeBatch$original$tFndybU4(FieldNamedPreparedStatementImpl.java:79) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl.executeBatch$original$tFndybU4$accessor$YvZYIDXw(FieldNamedPreparedStatementImpl.java) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl$auxiliary$mexWd3fI.call(Unknown Source) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at org.apache.skywalking.apm.agent.core.plugin.interceptor.enhance.v2.InstMethodsInterV2.intercept(InstMethodsInterV2.java:67) ~[skywalking-agent.jar:8.13.0]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl.executeBatch(FieldNamedPreparedStatementImpl.java) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.internal.executor.TableSimpleStatementExecutor.executeBatch(TableSimpleStatementExecutor.java:95) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	... 9 more
Caused by: java.net.SocketException: Broken pipe (Write failed)
	at java.net.SocketOutputStream.socketWrite0(Native Method) ~[?:?]
	at java.net.SocketOutputStream.socketWrite(SocketOutputStream.java:110) ~[?:?]
	at java.net.SocketOutputStream.write(SocketOutputStream.java:150) ~[?:?]
	at java.io.BufferedOutputStream.flushBuffer(BufferedOutputStream.java:81) ~[?:?]
	at java.io.BufferedOutputStream.flush(BufferedOutputStream.java:142) ~[?:?]
	at com.mysql.cj.protocol.a.SimplePacketSender.send(SimplePacketSender.java:55) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.protocol.a.TimeTrackingPacketSender.send(TimeTrackingPacketSender.java:50) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.protocol.a.NativeProtocol.send(NativeProtocol.java:619) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.protocol.a.NativeProtocol.sendCommand(NativeProtocol.java:683) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.protocol.a.NativeProtocol.sendCommand(NativeProtocol.java:155) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.NativeSession.queryServerVariable(NativeSession.java:597) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.ConnectionImpl.isReadOnly(ConnectionImpl.java:1394) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.ConnectionImpl.isReadOnly(ConnectionImpl.java:1387) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.ClientPreparedStatement.executeBatchInternal(ClientPreparedStatement.java:386) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.mysql.cj.jdbc.StatementImpl.executeBatch(StatementImpl.java:795) ~[mysql-connector-java-8.0.29.jar:8.0.29]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl.executeBatch$original$tFndybU4(FieldNamedPreparedStatementImpl.java:79) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl.executeBatch$original$tFndybU4$accessor$YvZYIDXw(FieldNamedPreparedStatementImpl.java) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl$auxiliary$mexWd3fI.call(Unknown Source) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at org.apache.skywalking.apm.agent.core.plugin.interceptor.enhance.v2.InstMethodsInterV2.intercept(InstMethodsInterV2.java:67) ~[skywalking-agent.jar:8.13.0]
	at com.greatsync.connector.jdbc.statement.FieldNamedPreparedStatementImpl.executeBatch(FieldNamedPreparedStatementImpl.java) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	at com.greatsync.connector.jdbc.internal.executor.TableSimpleStatementExecutor.executeBatch(TableSimpleStatementExecutor.java:95) ~[greatsync-connector-jdbc-1.0.2-SNAPSHOT.jar:?]
	... 9 more
2023-12-26 13:46:16,531 INFO  [eea32b1ee47bd8cc66da75c36bababe5] com.greatsync.connector.jdbc.internal.multithread.SingleThreadBatchTask - [SingleThreadBatchTask-(6/6)-(1/1)]Flushing triggered [by:Schedule with fixed Interval[5000]], current batch count:1.
```




