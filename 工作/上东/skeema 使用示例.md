---
date: 2025-08-25
---

### 编辑 .skeema 文件

```shell
mkdir jicai-db-shemas && cs jicai-db-schemas
vim .seekma
```

```toml
default-character-set=utf8mb4
default-collation=utf8mb4_general_ci
generator=skeema:1.13.0-community

[dev]
flavor=mysql:5.6
host=rm-uf60dh6h51450636yjo.mysql.rds.aliyuncs.com
port=3306
schema=test_jicai
user=test_for_anfang

[prod]
flavor=mysql:5.6
host=127.0.0.1
port=65535
schema=jicai
user=pro_jicai_rds
```


### 拉取测试环境的库表 SQL

```shell
skeema pull dev -p123456
```


### 和生产库进行比对



```shell
# 首先确保已经安装了 Docker，再执行以下命令进行对比
 skeema diff prod -p789789 -w docker --ignore-func '.*' --allow-unsafe > db_diff.sql
```


### 输出的内容为：

```sql
USE `jicai`;
ALTER TABLE `spd_bank_op_logs` MODIFY COLUMN `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键ID', AUTO_INCREMENT = 1;
ALTER TABLE `t_qiye_ckgx_h` MODIFY COLUMN `seller` varchar(32) COLLATE utf8mb4_general_ci NOT NULL COMMENT '卖家';
ALTER TABLE `finance_credit_material` MODIFY COLUMN `trade_year` decimal(7,1) NOT NULL DEFAULT '0.0' COMMENT '交易年度';

... ...

```