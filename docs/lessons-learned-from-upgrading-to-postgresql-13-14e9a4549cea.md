# 升级到 PostgreSQL 13 的经验教训

> 原文：<https://levelup.gitconnected.com/lessons-learned-from-upgrading-to-postgresql-13-14e9a4549cea>

记录 VACUUM、ANALYZE 和 REINDEX 的性能改进，以及一些 AWS RDS 意外情况。

![](img/2937bc1284776aa0ba0245cab623432e.png)

由[凯文·Ku](https://unsplash.com/@ikukevk?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

由于 PostgreSQL 社区计划在 2022 年 11 月 10 日之后取消对 PostgreSQL 10 的支持，而 [AWS RDS 也将于 2023 年 4 月 17 日](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)跟进，我最近受命将我们的数据库升级到 PostgreSQL 13。为了准备主要版本升级，我们遵循了 [RDS 升级用户指南](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.PostgreSQL.html#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process)。

# 准备升级

首先，我们确定了最新的兼容主版本升级目标:

```
$ aws rds describe-db-engine-versions --engine postgres  --engine-version 10.18--query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text> 13.4
```

然后，我们检查了不支持的用法:

*   **准备好的交易:** `SELECT count(*) FROM pg_catalog.pg_prepared_xacts;`
*   **Reg*数据类型:**
    `SELECT count(*) FROM pg_catalog.pg_class c, pg_catalog.pg_namespace n, pg_catalog.pg_attribute a
    WHERE c.oid = a.attrelid
    AND NOT a.attisdropped
    AND a.atttypid IN (‘pg_catalog.regproc’::pg_catalog.regtype,
    ‘pg_catalog.regprocedure’::pg_catalog.regtype,
    ‘pg_catalog.regoper’::pg_catalog.regtype,
    ‘pg_catalog.regoperator’::pg_catalog.regtype,
    ‘pg_catalog.regconfig’::pg_catalog.regtype,
    ‘pg_catalog.regdictionary’::pg_catalog.regtype)
    AND c.relnamespace = n.oid
    AND n.nspname NOT IN (‘pg_catalog’, ‘information_schema’);`

因为我们没有使用复制插槽或其他 PostgreSQL 扩展，所以我们在较低的环境中进行了一次试运行。升级花费了 10-15 分钟，没有出现任何问题。

# RDS 备份注意事项

然而，在升级过程中，我们遇到了一个意外的问题。对于 UAT 和 Prod，我们让主要版本升级在维护窗口期间生效。但是，当我们更改时间点恢复(PITR)保留期以符合新的法规遵从性标准时，升级立即发生了。

这在 [AWS 备份开发人员指南](https://docs.aws.amazon.com/aws-backup/latest/devguide/point-in-time-recovery.html)中有所记录，但它让我们大吃一惊:

```
When you change your PITR retention period, AWS Backup calls ModifyDBInstance and applies that change immediately. **If you have other configuration updates pending the next maintenance window, changing your PITR retention period will also apply those configuration updates immediately**. For more information, see [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) [in the *Amazon Relational Database Service API Reference*](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html).
```

值得庆幸的是，PITR 保留期的更改发生在非工作时间，但令人担忧的是，由于看似无关的更改，升级发生在维护窗口之外。

# PostgreSQL 13 中的性能更新

在升级后运行 VACUUM、ANALYZE 和 REINDEX 来提高和优化数据库性能总是一个好主意。这是因为在升级(或还原)过程中，索引可能会产生碎片，导致数据库可能会使用非优化的查询计划。尽管 RDS 已经打开了自动清空功能，但由于升级可能发生在维护窗口期间，所以这是同时运行这些任务的好时机。

在 PostgreSQL 13 中运行 REINDEX 的另一个原因是对 PostgreSQL 如何处理重复的 B 树索引的改进。对于基数较低的表，比如一个值与多行相关联的表，索引值中有很多重复。以加密账户持有者(例如，个人、银行、交易所等)及其相关加密地址的简单表格为例。如果表是按帐户索引的，PostgreSQL 13 之前的底层 B 树索引看起来类似于:

```
+------------+---------+
|  Account   | Address |
+------------+---------+
| Customer A | bc1q... |
| Customer A | 0xe82.. |
| Customer A | 3Kzh9.. |
| Customer B | 0x95g.. |
+------------+---------+
```

通过重复数据删除，该表可以将每个键存储一次，从而节省存储空间:

```
+------------+---------------------------+
|  Account   |          Address          |
+------------+---------------------------+
| Customer A | bc1q..., 0xe82.., 3Kzh9.. |
| Customer B | 0x95g..                   |
+------------+---------------------------+
```

在极端的情况下，尺寸上的差异可以节省高达 90%的成本。

要开始性能改进，首先发出 VACUUM 命令。我们可以使用 [vacuumdb](https://www.postgresql.org/docs/current/app-vacuumdb.html) 或者直接在数据库中使用 PSQL:

```
SELECT 
   'VACUUM(FULL, ANALYZE, VERBOSE) '|| '"' || table_name || '"' || ';'
FROM 
   information_schema.tables
WHERE 
  table_catalog = '<your-database>' AND 
  table_schema = 'public';
```

上述查询将为每个表生成相应的 VACUUM 语句。

同样，我们可以为所有表生成 REINDEX 语句:

```
SELECT 'REINDEX TABLE CONCURRENTLY ' || quote_ident(relname) || ' /*' || pg_size_pretty(pg_total_relation_size(C.oid)) || '*/;'
FROM pg_class C
LEFT JOIN pg_namespace N ON (N.oid = C.relnamespace)
WHERE nspname = 'public'
  AND C.relkind = 'r'
  AND nspname !~ '^pg_toast'
ORDER BY pg_total_relation_size(C.oid) ASC;
```

这将输出具有相关表大小的`REINDEX TABLE CONCURRENTLY <table>`语句。我们可以从较小的表开始，在整个数据库中调用它之前获得信心。

若要跟踪重新索引操作的进度，可以使用以下查询:

```
SELECT
  now()::TIME(0),
  a.query,
  p.phase,
  round(p.blocks_done / p.blocks_total::numeric * 100, 2) AS "% done",
  p.blocks_total,
  p.blocks_done,
  p.tuples_total,
  p.tuples_done, 
  p.current_locker_pid,
  ai.schemaname,
  ai.relname,
  ai.indexrelname
FROM pg_stat_progress_create_index p
JOIN pg_stat_activity a ON p.pid = a.pid
LEFT JOIN pg_stat_all_indexes ai on ai.relid = p.relid AND ai.indexrelid = p.index_relid;
```

请注意，如果对整个数据库(而不是单个表)发出 REINDEX 命令，查询将不会显示进度。

至此，PostgreSQL 升级完成，数据库应该得到优化。可选地，检查`pg_upgrade_internal.log`和`pg_upgrade_server.log`是否有任何错误或建议。

## 资源

*   [https://www . per ConA . com/blog/2020/09/10/index-improvements-in-PostgreSQL-13/](https://www.percona.com/blog/2020/09/10/index-improvements-in-postgresql-13/)
*   [https://adamj . eu/tech/2021/04/13/reindexing-all-tables-after-upgrading-to-PostgreSQL-13/](https://adamj.eu/tech/2021/04/13/reindexing-all-tables-after-upgrading-to-postgresql-13/)
*   [https://confluence . atlassian . com/jira kb/reindex-progresses-very-slow-after-postgres-upgrade-or-restore-1070073091 . html](https://confluence.atlassian.com/jirakb/reindex-progresses-very-slowly-after-postgres-upgrade-or-restore-1070073091.html)