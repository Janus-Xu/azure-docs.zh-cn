---
title: Azure Cosmos DB 查询语言中的 ROUND
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 ROUND。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302111"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
 返回一个数值，四舍五入到最接近的整数值。  
  
## <a name="syntax"></a>语法
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="remarks"></a>备注
  
  执行的四舍五入运算遵循远离零的中点四舍五入。 如果输入是正好介于两个整数之间的数值表达式，则结果将是离零最近的整数值。  
  
  |<numeric_expr>|已四舍五入|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
## <a name="examples"></a>示例
  
  以下示例将以下的正数和负数舍入到最接近的整数。  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  下面是结果集：  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中受益。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
