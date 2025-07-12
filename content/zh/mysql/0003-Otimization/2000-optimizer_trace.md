---
title: 2. optimizer_trace、optimizer_switch
description: optimizer_trace、optimizer_switch
date: 2023-10-31
weight: 2000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert  color="secondary">}}



{{</alert>}}

| 配置                  | 参数          | 备注         |
| :-------------------- | :------------ | :----------- |
| version               | 8.0.32        | 版本         |
| engine                | innodb        | 存储引擎     |
| transaction_isolation | read commited | 事务隔离级别 |





## 一、optimizer_trace

```sql
[admin@127.0.0.1:16315 09:12:54(db1)]$ set optimizer_trace="enabled=on",end_markers_in_json=on;
Query OK, 0 rows affected (0.00 sec)

[admin@127.0.0.1:16315 09:13:08(db1)]$ select t1.id from sbtest1 t1 inner join sbtest2 t2 on t1.id=t2.id where t2.k<10 and t1.id < 200;
Empty set (0.00 sec)

[admin@127.0.0.1:16315 09:13:15(db1)]$ select * from information_schema.optimizer_trace limit 30 \g;
*************************** 1. row ***************************
                            QUERY: select t1.id from sbtest1 t1 inner join sbtest2 t2 on t1.id=t2.id where t2.k<10 and t1.id < 200
                            TRACE: {
  "steps": [
    {
      "join_preparation": {
        "select#": 1,
        "steps": [
          {
            "expanded_query": "/* select#1 */ select `t1`.`id` AS `id` from (`sbtest1` `t1` join `sbtest2` `t2` on((`t1`.`id` = `t2`.`id`))) where ((`t2`.`k` < 10) and (`t1`.`id` < 200))"
          },
          {
            "transformations_to_nested_joins": {
              "transformations": [
                "JOIN_condition_to_WHERE",
                "parenthesis_removal"
              ] /* transformations */,
              "expanded_query": "/* select#1 */ select `t1`.`id` AS `id` from `sbtest1` `t1` join `sbtest2` `t2` where ((`t2`.`k` < 10) and (`t1`.`id` < 200) and (`t1`.`id` = `t2`.`id`))"
            } /* transformations_to_nested_joins */
          }
        ] /* steps */
      } /* join_preparation */
    },
    {
      "join_optimization": {
        "select#": 1,
        "steps": [
          {
            "condition_processing": {
              "condition": "WHERE",
              "original_condition": "((`t2`.`k` < 10) and (`t1`.`id` < 200) and (`t1`.`id` = `t2`.`id`))",
              "steps": [
                {
                  "transformation": "equality_propagation",
                  "resulting_condition": "((`t2`.`k` < 10) and (`t1`.`id` < 200) and multiple equal(`t1`.`id`, `t2`.`id`))"
                },
                {
                  "transformation": "constant_propagation",
                  "resulting_condition": "((`t2`.`k` < 10) and (`t1`.`id` < 200) and multiple equal(`t1`.`id`, `t2`.`id`))"
                },
                {
                  "transformation": "trivial_condition_removal",
                  "resulting_condition": "((`t2`.`k` < 10) and (`t1`.`id` < 200) and multiple equal(`t1`.`id`, `t2`.`id`))"
                }
              ] /* steps */
            } /* condition_processing */
          },
          {
            "substitute_generated_columns": {
            } /* substitute_generated_columns */
          },
          {
            "table_dependencies": [
              {
                "table": "`sbtest1` `t1`",
                "row_may_be_null": false,
                "map_bit": 0,
                "depends_on_map_bits": [
                ] /* depends_on_map_bits */
              },
              {
                "table": "`sbtest2` `t2`",
                "row_may_be_null": false,
                "map_bit": 1,
                "depends_on_map_bits": [
                ] /* depends_on_map_bits */
              }
            ] /* table_dependencies */
          },
          {
            "ref_optimizer_key_uses": [
              {
                "table": "`sbtest1` `t1`",
                "field": "id",
                "equals": "`t2`.`id`",
                "null_rejecting": true
              },
              {
                "table": "`sbtest2` `t2`",
                "field": "id",
                "equals": "`t1`.`id`",
                "null_rejecting": true
              }
            ] /* ref_optimizer_key_uses */
          },
          {
            "rows_estimation": [
              {
                "table": "`sbtest1` `t1`",
                "range_analysis": {
                  "table_scan": {
                    "rows": 98928,
                    "cost": 10271.7
                  } /* table_scan */,
                  "potential_range_indexes": [
                    {
                      "index": "PRIMARY",
                      "usable": true,
                      "key_parts": [
                        "id"
                      ] /* key_parts */
                    },
                    {
                      "index": "k_1",
                      "usable": false,
                      "cause": "not_applicable"
                    }
                  ] /* potential_range_indexes */,
                  "best_covering_index_scan": {
                    "index": "k_1",
                    "cost": 9917.18,
                    "chosen": true
                  } /* best_covering_index_scan */,
                  "setup_range_conditions": [
                  ] /* setup_range_conditions */,
                  "group_index_range": {
                    "chosen": false,
                    "cause": "not_single_table"
                  } /* group_index_range */,
                  "skip_scan_range": {
                    "chosen": false,
                    "cause": "not_single_table"
                  } /* skip_scan_range */,
                  "analyzing_range_alternatives": {
                    "range_scan_alternatives": [
                      {
                        "index": "PRIMARY",
                        "ranges": [
                          "id < 200"
                        ] /* ranges */,
                        "index_dives_for_eq_ranges": true,
                        "rowid_ordered": true,
                        "using_mrr": false,
                        "index_only": true,
                        "rows": 199,
                        "cost": 20.21,
                        "chosen": true
                      }
                    ] /* range_scan_alternatives */,
                    "analyzing_roworder_intersect": {
                      "usable": false,
                      "cause": "too_few_roworder_scans"
                    } /* analyzing_roworder_intersect */
                  } /* analyzing_range_alternatives */,
                  "chosen_range_access_summary": {
                    "range_access_plan": {
                      "type": "range_scan",
                      "index": "PRIMARY",
                      "rows": 199,
                      "ranges": [
                        "id < 200"
                      ] /* ranges */
                    } /* range_access_plan */,
                    "rows_for_plan": 199,
                    "cost_for_plan": 20.21,
                    "chosen": true
                  } /* chosen_range_access_summary */
                } /* range_analysis */
              },
              {
                "table": "`sbtest2` `t2`",
                "range_analysis": {
                  "table_scan": {
                    "rows": 99072,
                    "cost": 10270.1
                  } /* table_scan */,
                  "potential_range_indexes": [
                    {
                      "index": "PRIMARY",
                      "usable": true,
                      "key_parts": [
                        "id"
                      ] /* key_parts */
                    },
                    {
                      "index": "k_2",
                      "usable": true,
                      "key_parts": [
                        "k",
                        "id"
                      ] /* key_parts */
                    }
                  ] /* potential_range_indexes */,
                  "best_covering_index_scan": {
                    "index": "k_2",
                    "cost": 9931.61,
                    "chosen": true
                  } /* best_covering_index_scan */,
                  "setup_range_conditions": [
                  ] /* setup_range_conditions */,
                  "group_index_range": {
                    "chosen": false,
                    "cause": "not_single_table"
                  } /* group_index_range */,
                  "skip_scan_range": {
                    "chosen": false,
                    "cause": "not_single_table"
                  } /* skip_scan_range */,
                  "analyzing_range_alternatives": {
                    "range_scan_alternatives": [
                      {
                        "index": "PRIMARY",
                        "ranges": [
                          "id < 200"
                        ] /* ranges */,
                        "index_dives_for_eq_ranges": true,
                        "rowid_ordered": true,
                        "using_mrr": false,
                        "index_only": false,
                        "rows": 199,
                        "cost": 20.2078,
                        "chosen": true
                      },
                      {
                        "index": "k_2",
                        "ranges": [
                          "k < 10"
                        ] /* ranges */,
                        "index_dives_for_eq_ranges": true,
                        "rowid_ordered": false,
                        "using_mrr": false,
                        "index_only": true,
                        "rows": 19,
                        "cost": 2.16439,
                        "chosen": true
                      }
                    ] /* range_scan_alternatives */,
                    "analyzing_roworder_intersect": {
                      "usable": false,
                      "cause": "too_few_roworder_scans"
                    } /* analyzing_roworder_intersect */
                  } /* analyzing_range_alternatives */,
                  "chosen_range_access_summary": {
                    "range_access_plan": {
                      "type": "range_scan",
                      "index": "k_2",
                      "rows": 19,
                      "ranges": [
                        "k < 10"
                      ] /* ranges */
                    } /* range_access_plan */,
                    "rows_for_plan": 19,
                    "cost_for_plan": 2.16439,
                    "chosen": true
                  } /* chosen_range_access_summary */
                } /* range_analysis */
              }
            ] /* rows_estimation */
          },
          {
            "considered_execution_plans": [
              {
                "plan_prefix": [
                ] /* plan_prefix */,
                "table": "`sbtest2` `t2`",
                "best_access_path": {
                  "considered_access_paths": [
                    {
                      "access_type": "ref",
                      "index": "PRIMARY",
                      "usable": false,
                      "chosen": false
                    },
                    {
                      "rows_to_scan": 19,
                      "filtering_effect": [
                      ] /* filtering_effect */,
                      "final_filtering_effect": 0.00263158,
                      "access_type": "range",
                      "range_details": {
                        "used_index": "k_2"
                      } /* range_details */,
                      "resulting_rows": 0.05,
                      "cost": 4.06439,
                      "chosen": true
                    }
                  ] /* considered_access_paths */
                } /* best_access_path */,
                "condition_filtering_pct": 100,
                "rows_for_plan": 0.05,
                "cost_for_plan": 4.06439,
                "rest_of_plan": [
                  {
                    "plan_prefix": [
                      "`sbtest2` `t2`"
                    ] /* plan_prefix */,
                    "table": "`sbtest1` `t1`",
                    "best_access_path": {
                      "considered_access_paths": [
                        {
                          "access_type": "eq_ref",
                          "index": "PRIMARY",
                          "rows": 1,
                          "cost": 0.0175,
                          "chosen": true,
                          "cause": "clustered_pk_chosen_by_heuristics"
                        },
                        {
                          "access_type": "range",
                          "range_details": {
                            "used_index": "PRIMARY"
                          } /* range_details */,
                          "cost": 40.11,
                          "rows": 199,
                          "chosen": false,
                          "cause": "cost"
                        }
                      ] /* considered_access_paths */
                    } /* best_access_path */,
                    "condition_filtering_pct": 100,
                    "rows_for_plan": 0.05,
                    "cost_for_plan": 4.08189,
                    "chosen": true
                  }
                ] /* rest_of_plan */
              },
              {
                "plan_prefix": [
                ] /* plan_prefix */,
                "table": "`sbtest1` `t1`",
                "best_access_path": {
                  "considered_access_paths": [
                    {
                      "access_type": "ref",
                      "index": "PRIMARY",
                      "usable": false,
                      "chosen": false
                    },
                    {
                      "rows_to_scan": 199,
                      "filtering_effect": [
                      ] /* filtering_effect */,
                      "final_filtering_effect": 1,
                      "access_type": "range",
                      "range_details": {
                        "used_index": "PRIMARY"
                      } /* range_details */,
                      "resulting_rows": 199,
                      "cost": 40.11,
                      "chosen": true
                    }
                  ] /* considered_access_paths */
                } /* best_access_path */,
                "condition_filtering_pct": 100,
                "rows_for_plan": 199,
                "cost_for_plan": 40.11,
                "pruned_by_cost": true
              }
            ] /* considered_execution_plans */
          },
          {
            "attaching_conditions_to_tables": {
              "original_condition": "((`t1`.`id` = `t2`.`id`) and (`t2`.`k` < 10) and (`t2`.`id` < 200))",
              "attached_conditions_computation": [
              ] /* attached_conditions_computation */,
              "attached_conditions_summary": [
                {
                  "table": "`sbtest2` `t2`",
                  "attached": "((`t2`.`k` < 10) and (`t2`.`id` < 200))"
                },
                {
                  "table": "`sbtest1` `t1`",
                  "attached": "(`t1`.`id` = `t2`.`id`)"
                }
              ] /* attached_conditions_summary */
            } /* attaching_conditions_to_tables */
          },
          {
            "finalizing_table_conditions": [
              {
                "table": "`sbtest2` `t2`",
                "original_table_condition": "((`t2`.`k` < 10) and (`t2`.`id` < 200))",
                "final_table_condition   ": "((`t2`.`k` < 10) and (`t2`.`id` < 200))"
              },
              {
                "table": "`sbtest1` `t1`",
                "original_table_condition": "(`t1`.`id` = `t2`.`id`)",
                "final_table_condition   ": null
              }
            ] /* finalizing_table_conditions */
          },
          {
            "refine_plan": [
              {
                "table": "`sbtest2` `t2`"
              },
              {
                "table": "`sbtest1` `t1`"
              }
            ] /* refine_plan */
          }
        ] /* steps */
      } /* join_optimization */
    },
    {
      "join_execution": {
        "select#": 1,
        "steps": [
        ] /* steps */
      } /* join_execution */
    }
  ] /* steps */
}
MISSING_BYTES_BEYOND_MAX_MEM_SIZE: 0
          INSUFFICIENT_PRIVILEGES: 0
1 row in set (0.00 sec)

ERROR:
No query specified


```





















## 二、optimizer_switch


```sql
-- 关闭ICP
set optimizer_switch='index_condition_pushdown=off';

-- 打开ICP，则Extra列中显示: Using index condition
set optimizer_switch='index_condition_pushdown=on';


```


四 实现细节
1 ICP只能用于辅助索引，不能用于聚集索引。
2 ICP只用于单表，不是多表连接是的连接条件部分（如开篇强调）
如果表访问的类型为：
3 EQ_REF/REF_OR_NULL/REF/SYSTEM/CONST: 可以使用ICP
4 range：如果不是“index tree only（只读索引）”，则有机会使用ICP
5 ALL/FT/INDEX_MERGE/INDEX_SCAN:  不可以使用ICP
















