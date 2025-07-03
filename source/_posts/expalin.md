title: MySQL执行计划（chatGPT聊天最终获取总结）
author: Laiyong Wang
date: 2025-06-19 22:52:40
tags:
---
## 一、MySQL 执行计划概述

1. **作用**

   * 帮助理解优化器如何访问表、使用索引，以及各个操作的成本，定位性能瓶颈。

2. **主要列说明**

   * `id`：查询中各个 SELECT／子查询的序号，越大优先级越高。
   * `select_type`：查询的类型，如 SIMPLE、PRIMARY、SUBQUERY（子查询）、DERIVED（派生表）等。
   * `table`：当前行所指的表或临时表。
   * `partitions`：涉及的分区信息（如果表做了分区）。
   * `possible_keys`：优化器认为可用的索引列表。
   * `key`：优化器实际使用的索引。
   * `key_len`：使用索引的长度（字节数）。
   * `ref`：哪个列或常数与 key 做对比。
   * `rows`：优化器估计要读取的行数。
   * `type`：最重要的访问方式枚举（见下文详解）。
   * `Extra`：补充信息枚举（见下文详解）。

3. **优化思路**

   * **减少全表扫描**：尽量让 `type` 值靠前（优先级：`ALL` 最差 → `system` 最好）。
   * **合理建索引**：针对 `WHERE`、`JOIN`、`ORDER BY`、`GROUP BY` 设计合适的索引。
   * **避免文件排序与临时表**：`Extra` 中出现 `Using filesort`、`Using temporary` 时要警惕。
   * **分析并调整 SQL**：拆分复杂查询、消除隐式类型转换、避免在索引列上做函数运算等。

---

## 二、type 列的枚举值（按访问效率从高到低排列）

| 枚举值               | 含义                                             |
| ----------------- | ---------------------------------------------- |
| `system`          | 表只有一行（等同于常量表），是 `const` 的特殊情况。                 |
| `const`           | 通过常数（主键或唯一索引）查询出最多一行，速度极快。                     |
| `eq_ref`          | 对于每个外键表的行，使用唯一索引查找主表，最多一行，对应多表 JOIN 时性能好。      |
| `ref`             | 使用非唯一索引或前缀索引查找，可能匹配多行，通常用于外键关联。                |
| `fulltext`        | 使用全文索引扫描，适用于 `MATCH(...) AGAINST(...)` 查询。     |
| `ref_or_null`     | 类似 `ref`，但索引列可能为 `NULL`，匹配时会额外测试 `IS NULL`。    |
| `index_merge`     | 合并多个索引的扫描结果（`AND` 或 `OR` 条件下），然后合并去重。          |
| `unique_subquery` | 内部优化：对子查询（`IN (SELECT ...)`）使用唯一索引。            |
| `index_subquery`  | 内部优化：对子查询（`IN (SELECT ...)`）使用普通索引。            |
| `range`           | 对索引的区间扫描，如 `>`, `<`, `BETWEEN`, `IN(...)` 等操作。 |
| `index`           | 全索引扫描（相当于全表扫描，但扫描的是索引结构，通常比 `ALL` 略快）。         |
| `all`             | 全表扫描，最差的访问方式，读取所有行来检查条件。                       |

> **说明**：
>
> * 优化目标是让访问方式尽量往上靠，如能从 `ALL` 优化到 `range`、`ref`、甚至 `const`。
> * 访问方式越往下，扫描行数越多，查询越慢。

---

## 三、Extra 列的枚举值

`Extra` 提供了额外的执行细节，常见枚举包括：

1. **访问与过滤相关**

   * `Using where`：在存取数据后应用了 WHERE 过滤。
   * `Using index`：覆盖索引（仅从索引读取数据，无需回表），性能较好。
   * `Using index condition`：索引条件下推（Index Condition Pushdown, ICP），部分过滤在存取索引时完成。
   * `Using join buffer`：在某些 JOIN 情况下（如 `ref` 无法直接满足）使用 join buffer。

2. **排序与分组相关**

   * `Using filesort`：需要在 MySQL 层做额外的排序（并非文件写入，仅为命名），会影响性能。
   * `Using temporary`：使用临时表存放中间结果，常见于 `GROUP BY`、`DISTINCT`、复杂子查询。

3. **其他**

   * `Distinct`：在 SELECT DISTINCT 时，对行去重。
   * `Impossible WHERE`：WHERE 条件永远为假，该部分查询不执行。
   * `No tables used`：查询不涉及任何表（例如 `SELECT 1+1`）。
   * `FirstMatch(table)`：MySQL 内部优化，找到首个匹配行即可停止扫描。
   * `Range checked for each record (index map: ...)`：对每一行都做一次范围检查（较慢，通常发生在子查询）。
   * `Using MRR`：多范围读取（Multi-Range Read），将多个范围请求合并、排序后按顺序读取，提升 HDD 性能。
   * `Using union(...)`：内部优化，把多次扫描合并为一次，见 `index_merge`。
   * `Using intersection(...)`：内部优化，多个索引扫描取交集，见 `index_merge`。
   * `LooseScan(table)`：在聚合函数里（如 `GROUP_CONCAT`）做宽松扫描，只取每组的第一行。

> **小结**：
>
> * 出现 `Using filesort`、`Using temporary`、`Impossible WHERE` 时，应重点优化 SQL 或调整索引。
> * 覆盖索引（`Using index`）和 ICP（`Using index condition`）是值得追求的优化方向。
