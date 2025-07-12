---
title: 存储过程
description: procedure
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## 定义

#### 1. `delimiter $$`
> 命令行结束标志符号

默认情况下，mysql的结束标志符`delimiter`是分号，输入分号后回车，mysql将会提交命令。

在存储过程使用中，默认的结束标志符会与存储过程语句中的分号冲突，导致存储过程语句未执行完就提交，进而就报错了。

所以在创建存储过程前，我们一般会声明标志符为其他符号。比如`$$`，当然其他符号也可以。

注意`delimiter`声明语句后面没有分号



#### 2. 局部变量

1. `declare`来声明局部变量

2. 局部变量位置：存储过程begin到end的语句里，且只能在语句的开头

3. 变量设置默认值，我们可以使用default子句（值可以是常数，也可以指定为一个表达式），如果没有默认值，变量初始值为null

4. 先定义再使用，未定义的变量，select返回空。

5. 局部变量仅存在于存储过程中，当存储过程语句结束后，局部变量消失。相对地，用户变量可以用在存储过程内部和外部

6. 局部变量前往不要与表名和列名相同

```sql
declare i int default 10


```

#### 3. 用户变量

1. 局部变量只有变量名字，没有`@`符号；用户变量名前有`@`符号

2. 存储过程使用，我们也可以传入事先set的用户变量，用户变量命名以`@`开头。





```sql
set @a = 10;
delimiter $$
create procedure p2(inout num int)
begin
	select num;
end $$
delimiter;
call p1(@a);
select @a;



```

用户变量的参数类型：in、out、inout
如参数类型命名，我们解释下其区别：
in：储存过程可以调用，但储存过程调用修改该变量后，该变量是不被修改的，只进不出。我们事先set用户变量并设置某个值，存储过程中可以修改，但修改仅作用于存储过程内部。存储过程结束后，返回的还是原来的值。
inout：可以传入，存储过程中可以修改，修改结果对于存储过程结束后同样有效。比如上面的案例，传入时用户变量@a 是10，存储过程+1变成了11，再输出这个参数时就是11了。可进可出。
out：变量传入存储过程为null（可理解为变量原来的值是不能传入存储过程的），但存储过程修改后，变量的修改于存储过程结束后同样有效，只出不进。



#### 4. 异常处理

语法：
```sql
DECLARE handler_action HANDLER
    FOR condition_value [, condition_value] ...
    statement

handler_action: {
    CONTINUE
  | EXIT
  | UNDO
}

condition_value: {
    mysql_error_code
  | SQLSTATE [VALUE] sqlstate_value
  | condition_name
  | SQLWARNING
  | NOT FOUND
  | SQLEXCEPTION
}

```

- handler_action：指示处理程序在执行处理程序语句后采取的操作，`CONTINUE,EXIT`
- condition_value：特定条件或条件类
- statement：执行指定的处理程序







```sql

---
declare continue handler for not found set done = 1;

declare continue handler for 1051

declare continue handler for sqlstate '42s02'

declare continue handler for sqlstate '23000' set @x2 = 1;

declare continue handler for sqlwarning

---要忽略某个条件，请CONTINUE 为其声明一个处理程序并将其与一个空块关联
declare continue handler for sqlwarning begin end;

declare exit handler for sqlwarning begin end;

declare continue handler for sqlwarning
begin
set @retry = 1;
end;

declare continue handler for not found

declare continue handler for sqlexception



```





## 语句



#### 1. if语句


```sql
if search_condition then statement_list
elseif search_condition then statement_list
else statement_list
end if


```


```sql
if n > m then set s = '>';
elseif n = m then set s = '=';
else set s = '<';
end if;

```


#### 2. case语句

```sql


```




#### 3. while语句

```sql


```



#### 4. loop语句

```sql


```



#### 5.repeat语句

```sql


```



#### 1. iterate语句


```sql


```


#### 1. leave语句

```sql

leave label

```




















## 存储过程的定义与使用

```sql
delimiter $$

create procedure killsessions()
begin
    declare done int default 0;
    declare current_id int;
    declare current_users varchar(50);
    declare current_host varchar(50);
	declare current_db varchar(50);
	declare current_command varchar(255);
	declare execute_time int;
    declare cur cursor for select id,user,host,db,command,time from information_schema.processlist where id != connection_id() and user not in ('event_scheduler','system user') and command not in('Binlog Dump GTID');
    declare continue handler for not found set done = 1;
	select * from information_schema.processlist where id != connection_id() and user not in ('event_scheduler','system user') and command not in('Binlog Dump GTID');
    open cur;

    read_loop: loop
        fetch cur into current_id, current_users, current_host,current_db,current_command,execute_time;
        if done then
            leave read_loop;
        end if;
        kill current_id;
    end loop;
    close cur;

end $$

delimiter ;

```





```sql
DELIMITER $$
CREATE PROCEDURE build_email_list ( INOUT email_list VARCHAR ( 4000 ) ) BEGIN
	DECLARE v_finished INTEGER DEFAULT 0;
	DECLARE v_email VARCHAR ( 100 ) DEFAULT "";
	DECLARE email_cursor CURSOR FOR
	SELECT email FROM employees;
	DECLARE CONTINUE HANDLER FOR NOT FOUND SET v_finished = 1;
	OPEN email_cursor;
	get_email :LOOP
		FETCH email_cursor INTO v_email;
		IF v_finished = 1 THEN
			LEAVE get_email;
		END IF;
		SET email_list = CONCAT( v_email, ";", email_list );
	END LOOP get_email;
	CLOSE email_cursor;
END $$
DELIMITER ;

```





















