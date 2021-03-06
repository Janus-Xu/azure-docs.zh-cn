---
title: Synapse SQL 中的 CETAS
description: 使用 Synapse SQL 提供的 CETAS
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 71bc20680467d270436e28190bb49db5b9313ca0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420041"
---
# <a name="cetas-with-synapse-sql"></a>Synapse SQL 提供的 CETAS

在 SQL 池或 SQL 按需版本（预览版）中，可以使用 CREATE EXTERNAL TABLE AS SELECT (CETAS) 来完成以下任务：  

- 创建外部表
- 将 Transact-SQL SELECT 语句的结果并行导出到

  - Hadoop
  - Azure 存储 Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>SQL 池中的 CETAS

对于 SQL 池，CETAS 用法和语法，请查看 [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 一文。 此外，有关使用 SQL 池的 CTAS 的指导，请参阅 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 一文。

## <a name="cetas-in-sql-on-demand"></a>SQL 按需版本中的 CETAS

使用 SQL 按需版本资源时，CETAS 用来创建外部表，并将查询结果导出到 Azure 存储 Blob 或 Azure Data Lake Storage Gen2。

## <a name="syntax"></a>语法

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>参数

*[ [ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

要创建的表的一到三部分名称。 对于外部表，SQL 按需版本仅存储表元数据。 不会在 SQL 按需版本中移动或存储任何实际数据。

LOCATION = *'path_to_folder'*

在外部数据源上指定写入 SELECT 语句结果的位置。 根文件夹是外部数据源中指定的数据位置。 LOCATION 必须指向一个文件夹并具有尾随 /。 示例：aggregated_data/

DATA_SOURCE = *external_data_source_name*

指定外部数据源对象的名称，该对象包含外部数据的存储位置。 若要创建外部数据源，请使用 [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source)。

FILE_FORMAT = *external_file_format_name*

指定包含外部数据文件格式的外部文件格式对象的名称。 若要创建外部文件格式，请使用 [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format)。 目前仅支持 FORMAT='PARQUET' 的外部文件格式。

WITH *<common_table_expression>*

指定临时命名的结果集，这些结果集称为公用表表达式 (CTE)。 有关详细信息，请参阅 [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

SELECT <select_criteria>

使用 SELECT 语句的结果填充新表。 select_criteria 是 SELECT 语句的主体，用于确定将哪些数据复制到新表中  。 有关 SELECT 语句的信息，请参阅 [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="permissions"></a>权限

需要具有列出文件夹内容和向 LOCATION 文件夹写入内容的权限才能使用 CETAS。

## <a name="examples"></a>示例

这些示例使用 CETAS 将按年份和州聚合的总人口保存到 population_ds 数据源中的 aggregated_data 文件夹。

此示例依赖于先前创建的凭据、数据源和外部文件格式。 请参阅[外部表](develop-tables-external-tables.md)文档。 若要将查询结果保存到同一数据源中的其他文件夹，请更改 LOCATION 参数。 若要将结果保存到不同的存储帐户，请为 DATA_SOURCE 参数创建并使用一个不同的数据源。

> [!NOTE]
> 下面的示例使用公共 Azure 开放式数据存储帐户。 它是只读的。 若要执行这些查询，需要提供你具有写入权限的数据源。

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

下面的示例使用一个外部表作为 CETAS 的源。 此示例依赖于先前创建的凭据、数据源、外部文件格式和外部表。 请参阅[外部表](develop-tables-external-tables.md)文档。

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>支持的数据类型

CETAS 可用于存储包含以下 SQL 数据类型的结果集：

- binary
- varbinary
- char
- varchar
- date
- time
- datetime2
- Decimal
- numeric
- FLOAT
- real
- bigint
- int
- smallint
- tinyint
- bit

LOB 不能用于 CETAS。

以下数据类型不能在 CETAS 的 SELECT 部分中使用：

- nchar
- nvarchar
- datetime
- smalldatetime
- datetimeoffset
- money
- smallmoney
- uniqueidentifier

## <a name="next-steps"></a>后续步骤

可以尝试查询 [Spark 表](develop-storage-files-spark-tables.md)。
