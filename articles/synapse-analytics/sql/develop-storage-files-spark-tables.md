---
title: 使用 SQL 按需版本（预览版）查询 Spark 表
description: 概述如何使用 SQL 按需版本（预览版）查询 Spark 表
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420071"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>通过 Azure Synapse Analytics 使用 SQL 按需版本（预览版）查询 Spark 表

SQL 按需版本（预览版）可以自动从 Synapse 工作区（预览版）中的 Spark 池同步元数据。 将为 Spark 池（预览版）中存在的每个数据库创建 SQL 按需版本数据库。 对于每个基于 Parquet 或 CSV 的 Spark 表，都将在 SQL 按需版本数据库中创建一个外部表。 因此，在关闭 Spark 池的情况下，仍可从 SQL 按需版本查询 Spark 表。

在 Spark 中对表进行分区时，存储中的文件将按文件夹进行组织。 SQL 按需版本会利用分区元数据，仅以相关文件夹和文件作为查询目标。

将为 Azure Synapse 工作区中预配的每个 Spark 池自动配置元数据同步。 可以立即开始查询 Spark 表。

每个 Spark 表都通过与 SQL 按需版本数据库对应的 dbo 架构中的一个外部表来表示。 对于 Spark 表查询，请运行以外部 [spark_table] 为目标的查询。 在运行下面的示例之前，对于文件所在的存储帐户，请确保拥有正确的[存储帐户访问权限](develop-storage-files-storage-access-control.md)。

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Spark 数据类型到 SQL 数据类型的映射

| Spark 数据类型 | SQL 数据类型               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | FLOAT                       |
| DecimalType     | Decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (into JSON)** |
| MapType         | varchar(max)* (into JSON)** |
| StructType      | varchar(max)* (into JSON)** |

\* 使用的排序规则为 Latin1_General_100_BIN2_UTF8。

** ArrayType、MapType 和 StructType 都表示为 JSON 格式。



## <a name="next-steps"></a>后续步骤

转到[存储访问控制](develop-storage-files-storage-access-control.md)一文，详细了解存储访问控制。
