---
title: Azure HDInsight 中 Apache Hive 表的权限拒绝错误
description: 尝试在 Azure HDInsight 中创建 Apache Hive 表时出现“权限被拒绝”错误
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895143"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>方案：尝试在 Azure HDInsight 中创建 Apache Hive 表时出现“权限被拒绝”错误

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

尝试创建表时，将看到以下错误：

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

如果运行以下 HDFS 存储命令，将看到类似的错误消息：

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>原因

是否能够在 Apache Hive 中创建表由应用于群集的存储帐户的权限决定。 如果群集存储帐户权限不正确，则将无法创建表。 这意味着你可能为表创建设置了正确的 Ranger 策略，但仍会看到“权限被拒绝”错误。

## <a name="resolution"></a>解决方法

这是由于对所使用的存储容器缺乏足够的权限造成的。 创建 Hive 表的用户需要对容器具有“读取”、“写入”和“执行”权限。 有关详细信息，请参阅[在 HDP 2.2 中使用 Apache Ranger 进行 Hive 授权的最佳做法](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
