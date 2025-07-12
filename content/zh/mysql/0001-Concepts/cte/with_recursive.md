---
title: 递归查询
descrit1ion: with recursive
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## 语法


```sql
with recursive cte_name (column_list) as (
select initial_query_result
union all
select recursive_query from cte_name
where condition
)
select * from cte_name;

```


| 项                        | 功能                                                                               |
| :------------------------ | :--------------------------------------------------------------------------------- |
| cte_name                  | 临时递归表名称                                                                     |
| column_list               | 表示cte_name查询表中包含的列名，列名之间用逗号分隔，可以在初始查询和递归查询中引用 |
| initial_query_result      | 表示初始的查询结果，应该与column_list中的列名对应                                  |
| union                     | 表示将两个查询结果集进行联合，使用UNION ALL则表示保留重复数据                      |
| recursive_query           | 表示递归查询语句，应当与column_list中的列名对应                                    |
| condition                 | 表示递归查询的终止条件，需要使用cte_name中的列进行判断                             |
| `select * from cte_name;` | 表示最终返回的查询结果集，可以通过cte_name查询表中的列名进行指定                   |



```sql
with recursive cte as (
select id, user_id, parent_id, 1 as level from t where user_id = 109
union all
select t1.id, t1.user_id, t1.parent_id, t2.level + 1 from t t1 join cte t2 on t1.user_id = t2.parent_id where t1.parent_id is not null
)
select * from cte;

```







基于一组初始数据，进行递归查询，返回符合条件的数据集

树形结构、层级结构的数据处理，以及对数据进行分类汇总



## 使用场景



## 使用限制
查询语句的复杂性和效率，要注意数据量大小和递归层数




## with recursive




## 案例


```sql
CREATE TABLE `sz_promotion_tree` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_id` bigint DEFAULT NULL COMMENT '用户id',
  `parent_id` bigint DEFAULT NULL COMMENT '推荐者id',
  `create_time` datetime DEFAULT NULL COMMENT '推广时间',
  PRIMARY KEY (`id`),
  KEY `user_id` (`user_id`) USING BTREE,
  KEY `parent_id` (`parent_id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci COMMENT='推广记录表';

CREATE TABLE `sz_user` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
  `nickname` varchar(100) COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '昵称',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=111 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci COMMENT='用户表';


INSERT INTO sz_user(id, nickname) VALUES (99, 'S');
INSERT INTO sz_user(id, nickname) VALUES (100, 'A');
INSERT INTO sz_user(id, nickname) VALUES (101, 'B');
INSERT INTO sz_user(id, nickname) VALUES (102, 'C');
INSERT INTO sz_user(id, nickname) VALUES (103, 'D');
INSERT INTO sz_user(id, nickname) VALUES (104, 'E');
INSERT INTO sz_user(id, nickname) VALUES (105, 'F');
INSERT INTO sz_user(id, nickname) VALUES (106, 'G');
INSERT INTO sz_user(id, nickname) VALUES (107, 'H');
INSERT INTO sz_user(id, nickname) VALUES (108, 'I');
INSERT INTO sz_user(id, nickname) VALUES (109, 'J');

INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (1, 100, 99, NULL);
INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (2, 101, 100, NULL);
INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (3, 102, 100, NULL);
INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (4, 103, 101, NULL);
INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (5, 104, 101, NULL);
INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (6, 105, 102, NULL);
INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (7, 106, 102, NULL);
INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (8, 107, 103, NULL);
INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (9, 108, 103, NULL);
INSERT INTO sz_promotion_tree(id, user_id, parent_id, create_time) VALUES (10, 109, 104, NULL);

WITH RECURSIVE promotion_tree AS (
  SELECT id, user_id, parent_id, 1 AS level
  FROM sz_promotion_tree
  WHERE user_id = 109
  UNION ALL
  SELECT pt.id, pt.user_id, pt.parent_id, pt2.level + 1
  FROM sz_promotion_tree pt
  JOIN promotion_tree pt2 ON pt.user_id = pt2.parent_id
  WHERE pt.parent_id IS NOT NULL
)
SELECT *
FROM promotion_tree;




WITH RECURSIVE promotion_tree AS (
  SELECT id, user_id, parent_id, 1 AS level
  FROM sz_promotion_tree
  WHERE user_id = 109
  UNION ALL
  SELECT pt.id, pt.user_id, pt.parent_id, pt2.level + 1
  FROM sz_promotion_tree pt
  JOIN promotion_tree pt2 ON pt.user_id = pt2.parent_id
  WHERE pt.parent_id IS NOT NULL
)
SELECT *
FROM promotion_tree;

# ①首先，使用 WITH RECURSIVE 关键字声明了一个递归查询公共表表达式（CTE），命名为 promotion_tree。

# ②在初始查询中，从 sz_promotion_tree 表中选择符合条件 user_id = 109 的记录，并为它们分配一个初始级别 1。这些记录作为递归查询的起始点。

# ③使用 UNION ALL 运算符，将初始查询的结果与递归查询的结果连接起来。

# ④在递归查询中，选择 sz_promotion_tree 表中的记录，连接到前一级的递归结果（promotion_tree），并通过条件 pt.user_id = pt2.parent_id 进行连接。这样可以构建一个向上层级递归的结构。

# ⑤在递归查询的每一次迭代中，将前一级的层级 pt2.level 加上 1，并将结果作为当前级别 level。

# ⑥递归查询继续迭代，直到不再满足条件 pt.parent_id IS NOT NULL，即没有上级用户时停止递归。

# ⑦最后，从递归查询公共表表达式 promotion_tree 中选择所有列，并返回结果。


--- 问题二（类似向下递归）

CREATE TABLE `sz_order` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_id` bigint DEFAULT NULL COMMENT '用户id',
  `recharge_amount` decimal(10,2) DEFAULT NULL COMMENT '花费金额',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci COMMENT='购买记录';

INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (8, 99, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (9, 100, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (10, 101, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (11, 102, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (12, 103, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (13, 104, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (14, 105, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (15, 106, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (16, 107, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (17, 108, 10000);
INSERT INTO sz_order(id, user_id, recharge_amount) VALUES (18, 109, 10000);


WITH RECURSIVE promotion_tree AS (
  SELECT id, user_id, parent_id, CAST(user_id AS CHAR(200)) AS chain
  FROM sz_promotion_tree
  WHERE parent_id = (SELECT user_id FROM sz_promotion_tree WHERE user_id = 100)
  UNION ALL
  SELECT pt.id, pt.user_id, pt.parent_id, CONCAT(pt2.chain, '->', pt.user_id)
  FROM sz_promotion_tree pt
  JOIN promotion_tree pt2 ON pt.parent_id = pt2.user_id
),
chain_summary AS (
  SELECT pt.chain, SUM(so.recharge_amount) AS total_amount
  FROM promotion_tree pt
  LEFT JOIN sz_order so ON pt.user_id = so.user_id
  GROUP BY pt.chain
)
SELECT pt.user_id, sum(cs.total_amount) as total_amount
FROM promotion_tree pt
left JOIN chain_summary cs ON cs.chain LIKE CONCAT(pt.chain, '%')
WHERE pt.parent_id = (SELECT user_id FROM sz_promotion_tree WHERE user_id = 100)
group by pt.user_id;




```

如果再加一张消费记录表，每个人在平台的消费都会给记录到消费记录表中，比如A，A下面有B、C，A邀请的第一级有几个人，就算几个分支，现在需要查询出A下面有多少个分支，并且，每个分支的消费额（比如B分支，包含B和B的所有子节点）有多少。

准备数据：给刚才的A-I，没人插入一条1w的购物记录


四、注意事项

1、递归的层数应该尽可能的少，过多的递归层数可能导致查询效率低下或者程序崩溃。

2、MySQL with Recursive功能在MySQL 8.0版本中才被正式引入，使用该功能建议使用该版本或者以上版本。

3、使用时需要考虑数据量的大小，如果数据量过大可能会影响递归查询的效率。




























