---
title: 托管实例：长期备份保留（PowerShell）
description: 了解如何使用 PowerShell 在 Azure SQL 数据库托管实例的单独 Azure Blob 存储容器上存储和还原自动备份。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/29/2020
ms.openlocfilehash: fba1dcdb8c4e5cfaa8071ae58ad75432b526c887
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509697"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>管理 Azure SQL 数据库托管实例长期备份保留（PowerShell）

在 Azure SQL 数据库托管实例中，你可以将[长期备份保留](sql-database-long-term-retention.md#managed-instance-support)策略（LTR）配置为有限的公共预览功能。 这使你可以将数据库备份自动保留在单独的 Azure Blob 存储容器中，最长可达10年。 然后，可以使用 PowerShell 通过这些备份来恢复数据库。

   > [!IMPORTANT]
   > 对于托管实例，LTR 目前处于有限预览版中，并可用于每种情况下的 EA 和 CSP 订阅。 若要请求注册，请创建[Azure 支持票证](https://azure.microsoft.com/support/create-ticket/)。 对于 "问题类型"，请选择 "服务" "服务"，选择 "SQL 数据库托管实例" 和 "对于问题类型"，选择 "**备份"、"还原" 和 "业务连续性/长期备份保留**。 在你的请求中，请陈述你希望在面向托管实例的按范围有限的公共预览版中进行注册。

以下各部分展示了如何使用 PowerShell 配置长期备份保留、查看 Azure SQL 存储中的备份，以及从 Azure SQL 存储中的备份进行还原。

## <a name="rbac-roles-to-manage-long-term-retention"></a>用于管理长期保留的 RBAC 角色

对于**AzSqlInstanceDatabaseLongTermRetentionBackup**和**AzSqlInstanceDatabase**，你将需要具有以下角色之一：

- “订阅所有者”角色或
- 托管实例参与者角色或
- 具有以下权限的自定义角色：
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

对于**AzSqlInstanceDatabaseLongTermRetentionBackup**，你将需要具有以下角色之一：

- “订阅所有者”角色或
- 具有以下权限的自定义角色：
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> 托管实例参与者角色没有删除 LTR 备份的权限。

可以在“订阅”** 或“资源组”** 范围内授予 RBAC 权限。 但是，若要访问属于已删除实例的 LTR 备份，必须在该实例的*订阅*作用域中授予权限。

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>创建 LTR 策略

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>查看 LTR 策略

此示例演示如何列出实例中的 LTR 策略

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>清除 LTR 策略

此示例展示了如何从数据库中清除 LTR 策略

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>查看 LTR 备份

此示例演示如何列出实例中的 LTR 备份。

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>删除 LTR 备份

此示例展示了如何从备份列表中删除 LTR 备份。

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> 删除 LTR 备份操作是不可逆的。 若要在删除实例后删除 LTR 备份，则必须具有订阅范围权限。 可以通过筛选“删除长期保留备份”操作来在 Azure Monitor 中设置有关每次删除的通知。 活动日志包含有关发出请求的人员和时间的信息。 有关详细说明，请参阅[创建活动日志警报](../azure-monitor/platform/alerts-activity-log.md)。

## <a name="restore-from-ltr-backups"></a>从 LTR 备份进行还原

此示例展示了如何从 LTR 备份进行还原。 请注意，此接口没有更改，但是资源 ID 参数现在要求提供 LTR 备份资源 ID。

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> 若要在删除实例后从 LTR 备份进行还原，则必须具有该实例的订阅的权限，并且该订阅必须处于活动状态。 还必须省略可选的 -ResourceGroupName 参数。

> [!NOTE]
> 从此处，可使用 SQL Server Management Studio 连接到已还原的数据库，执行所需任务，例如从恢复的数据库中提取一部分数据，复制到现有数据库或删除现有数据库，并将已还原的数据库重命名为现有数据库名。 请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="next-steps"></a>后续步骤

- 若要了解服务生成的自动备份，请参阅[自动备份](sql-database-automated-backups.md)
- 若要了解长期备份保留，请参阅[长期备份保留](sql-database-long-term-retention.md)
