---
title: 从备份还原数据库
description: 了解时间点还原，该功能可让你将 Azure SQL 数据库回滚到过去最多 35 天的时间点。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268738"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>使用自动数据库备份恢复 Azure SQL 数据库

默认情况下，Azure SQL 数据库备份存储在异地复制的 Blob 存储（RA-GRS 存储类型）中。 以下选项适用于使用[自动数据库备份](sql-database-automated-backups.md)的数据库恢复： 方法：

- 在恢复到保留期内指定时间点的同一 SQL 数据库服务器上创建新数据库。
- 在恢复到已删除数据库的删除时间的同一 SQL 数据库服务器上创建数据库。
- 在恢复到最新备份点的相同区域中的任一 SQL 数据库服务器上创建新数据库。
- 在恢复到最近复制备份点的其他任何区域中的任一 SQL 数据库服务器上创建新数据库。

如果已配置[备份长期保留](sql-database-long-term-retention.md)，还可以从任一 SQL 数据库服务器上的任何长期保留备份创建新数据库。

> [!IMPORTANT]
> 还原期间无法覆盖现有数据库。

使用“标准”或“高级”服务层级时，数据库还原可能会产生额外的存储费用。 如果还原的数据库的最大大小大于目标数据库的服务层级和性能级别包含的存储量，则会产生额外的费用。 有关额外存储定价的详细信息，请参阅 [SQL 数据库定价页面](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，可以通过将数据库最大大小设置为附送的量，来避免产生额外的费用。

## <a name="recovery-time"></a>恢复时间

使用自动数据库备份还原数据库的恢复时间受几个因素的影响：

- 数据库的大小。
- 数据库的计算大小。
- 所涉及的事务日志数。
- 需要重新播放以恢复到还原点的活动数量。
- 还原到不同区域时的网络带宽。
- 目标区域中正在处理的并行还原请求数。

对于较大或非常活跃的数据库，还原可能要花费几个小时。 如果某个区域出现长时间的服务中断，则灾难恢复可能会发起大量的异地还原请求。 存在很多请求时，单个数据库的恢复时间可能会增加。 大部分数据库还原操作可在 12 小时内完成。

对于单个订阅，并发还原请求的数目存在限制。 这些限制适用于时间点还原、异地还原和从长期保留备份中还原的任意组合。

| | **处理的并发请求数最多为 #** | **提交的并发请求数最多为 #** |
| :--- | --: | --: |
|单个数据库（每个订阅）|10 个|60|
|弹性池（每个池）|4|200|
||||

没有任何内置的方法可以还原整个服务器。 有关如何完成此任务的示例，请参阅 [Azure SQL 数据库：完整服务器恢复](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)。

> [!IMPORTANT]
> 若要使用自动备份进行恢复，用户必须是订阅中的 SQL Server 参与者角色的成员或是订阅所有者。 有关详细信息，请参阅 [RBAC：内置角色](../role-based-access-control/built-in-roles.md)。 可以使用 Azure 门户、PowerShell 或 REST API 进行恢复。 不能使用 Transact-SQL。

## <a name="point-in-time-restore"></a>时间点还原

可以使用 Azure 门户、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases)，将独立数据库、共用数据库或实例数据库还原到早期的时间点。 请求可以指定要还原的数据库的任何服务层级或计算大小。 确保要将数据库还原到其中的服务器上有足够的资源。 完成后，还原过程将在原始数据库所在的同一台服务器上创建新数据库。 还原的数据库将基于其服务层级和计算大小按标准费率计费。 在数据库还原完成之前，不会产生费用。

通常，为恢复目的将数据库还原到较早点。 可将还原的数据库视为原始数据库的替代数据库，或使用它作为数据源来更新原始数据库。

- **数据库替换**

  如果你打算用还原的数据库替换原始数据库，则应该指定原始数据库的计算大小和服务层级。 然后，可以使用 T-SQL 中的 [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) 命令来重命名原始数据库，并为还原的数据库指定原有的名称。

- **数据恢复**

  如果你打算从还原的数据库检索数据以从用户或应用程序错误中恢复，则需要编写并执行一个数据恢复脚本，用于从还原的数据库提取数据并将其应用到原始数据库。 尽管还原操作可能需要很长时间才能完成，但整个还原过程中，都可在数据库列表中看到还原数据库。 如果在还原期间删除数据库，将取消还原操作，则不会针对未完成还原的数据库向你收费。
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>使用 Azure 门户进行时间点还原

可以在 Azure 门户中通过要还原的数据库的概述边栏选项卡，将单个 SQL 数据库或实例数据库恢复到某个时间点。

#### <a name="single-azure-sql-database"></a>单个 Azure SQL 数据库

若要使用 Azure 门户将单一数据库或共用数据库恢复到某个时间点，请打开该数据库的概述页，并在工具栏上选择“还原”  。 选择备份源，然后选择要从中创建新数据库的时间点备份点。 

  ![数据库还原选项的屏幕截图](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>托管实例数据库

若要使用 Azure 门户将托管实例数据库恢复到某个时间点，请打开该数据库的概述页，并在工具栏上选择“还原”  。 选择要从中创建新数据库的时间点备份点。 

  ![数据库还原选项的屏幕截图](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> 若要以编程方式从备份还原数据库，请参阅[使用自动备份以编程方式执行恢复](sql-database-recovery-using-backups.md)。

## <a name="deleted-database-restore"></a>已删除的数据库还原

可将已删除的数据库还原到同一 SQL 数据库服务器或同一托管实例上的删除时间或早期的时间点。 可以通过 Azure 门户、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) 或 [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) 完成此操作。 通过从备份创建新数据库来还原已删除的数据库。

> [!IMPORTANT]
> 如果删除 Azure SQL 数据库服务器或托管实例，其所有数据库也会一并删除，并且无法恢复。 无法还原已删除的服务器或托管实例。

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>使用 Azure 门户还原已删除的数据库

通过 Azure 门户还原在服务器和实例资源中删除的数据库。

#### <a name="single-azure-sql-database"></a>单个 Azure SQL 数据库

若要使用 Azure 门户将已删除的单一数据库或共用数据库恢复到删除时间，请打开服务器概览页，然后选择“删除的数据库”。  选择要还原的已删除数据库，然后键入要使用从备份还原的数据创建的新数据库的名称。

  ![还原已删除的 Azure SQL 数据库的屏幕截图](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>托管实例数据库

若要使用 Azure 门户恢复托管数据库，请打开托管实例概览页，然后选择“删除的数据库”。  选择要还原的已删除数据库，然后键入要使用从备份还原的数据创建的新数据库的名称。

  ![还原已删除的 Azure SQL 实例数据库的屏幕截图](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>使用 PowerShell 还原已删除的数据库

在 PowerShell 中使用以下示例脚本还原 Azure SQL 数据库和托管实例的已删除数据库。

#### <a name="single-azure-sql-database"></a>单个 Azure SQL 数据库

有关演示如何还原已删除的 Azure SQL 数据库的示例 PowerShell 脚本，请参阅[使用 PowerShell 还原 SQL 数据库](scripts/sql-database-restore-database-powershell.md)。

#### <a name="managed-instance-database"></a>托管实例数据库

有关演示如何还原已删除的实例数据库的示例 PowerShell 脚本，请参阅[使用 PowerShell 还原托管实例上已删除的数据库](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> 若要以编程方式还原已删除的数据库，请参阅[使用自动备份以编程方式执行恢复](sql-database-recovery-using-backups.md)。

## <a name="geo-restore"></a>异地还原

可在任何 Azure 区域的任何服务器上从最新异地复制的备份中还原 SQL 数据库。 异地还原使用异地复制的备份作为源。 即使由于服务中断而无法访问数据库或数据中心，也依然能够请求异地还原。

当数据库因其所在的区域发生事故而不可用时，异地还原是默认的恢复选项。 可将数据库还原到任何其他区域中的服务器。 创建备份后，将其异地复制到其他区域中的 Azure Blob 时会出现延迟。 因此，还原的数据库最多可能比原始数据库晚一个小时。 下图演示了如何从另一区域中的最后一个可用备份还原数据库。

![异地还原插图](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>使用 Azure 门户进行异地还原

在 Azure 门户中，创建一个新的单一数据库或托管实例数据库，并选择可用的异地还原备份。 新建的数据库包含异地还原的备份数据。

#### <a name="single-azure-sql-database"></a>单个 Azure SQL 数据库

若要通过 Azure 门户异地还原所选区域和服务器中的单个 SQL 数据库，请执行以下步骤：

1. 在“仪表板”中，选择“添加” > “创建 SQL 数据库”。    在“基本信息”选项卡上输入所需的信息。 
2. 选择“其他设置”。 
3. 对于“使用现有数据”，请选择“备份”。  
4. 对于“备份”，请从可用的异地还原备份列表中选择一个备份。 

    ![“创建 SQL 数据库”选项的屏幕截图](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

完成从备份创建新数据库的过程。 创建单个 Azure SQL 数据库时，该数据库包含已还原的异地还原备份。

#### <a name="managed-instance-database"></a>托管实例数据库

若要通过 Azure 门户将托管实例数据库异地还原到所选区域中的现有托管实例，请选择要在其上还原数据库的托管实例。 执行以下步骤：

1. 选择“新建数据库”。 
2. 键入所需的数据库名称。
3. 在“使用现有数据”下，选择“备份”。  
4. 从可用的异地还原备份列表中选择一个备份。

    ![“新建数据库”选项的屏幕截图](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

完成创建新数据库的过程。 创建实例数据库时，该数据库包含已还原的异地还原备份。

### <a name="geo-restore-by-using-powershell"></a>使用 PowerShell 进行异地还原

#### <a name="single-azure-sql-database"></a>单个 Azure SQL 数据库

有关演示如何对单个 SQL 数据库执行异地还原的 PowerShell 脚本，请参阅[使用 PowerShell 将 Azure SQL 单一数据库还原到早期的时间点](scripts/sql-database-restore-database-powershell.md)。

#### <a name="managed-instance-database"></a>托管实例数据库

有关演示如何对托管实例数据库执行异地还原的 PowerShell 脚本，请参阅[使用 PowerShell 将托管实例数据库还原到另一个地理区域](scripts/sql-managed-instance-restore-geo-backup.md)。

### <a name="geo-restore-considerations"></a>异地还原注意事项

不能对异地辅助数据库执行时间点还原。 只能对主数据库执行此操作。 有关使用异地还原在中断后恢复的详细信息，请参阅[在中断后恢复](sql-database-disaster-recovery.md)。

> [!IMPORTANT]
> 异地还原是 SQL 数据库中提供的最基本的灾难恢复解决方案。 它依赖于自动创建的异地复制备份，其恢复点目标 (RPO) 为 1 小时，估计恢复时间最长为 12 小时。 它不保证在发生区域性的服务中断后，目标区域可提供足够的容量来还原数据库，因为此时的需求可能会急剧上升。 如果应用程序使用相对较小的数据库并且不是业务关键型应用程序，则异地还原是适当的灾难恢复解决方案。 对于需要大型数据库且必须确保业务连续性的业务关键型应用程序，请使用[自动故障转移组](sql-database-auto-failover-group.md)。 此功能提供的 RPO 和恢复时间目标要低得多，并且始终可以保证容量。 有关业务连续性选项的详细信息，请参阅[业务连续性概述](sql-database-business-continuity.md)。

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>使用自动备份以编程方式执行恢复

也可以使用 Azure PowerShell 或 REST API 进行恢复。 下表描述了可用的命令集。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

#### <a name="single-azure-sql-database"></a>单个 Azure SQL 数据库

若要还原独立或共用数据库，请参阅[AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)。

  | Cmdlet | 说明 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |获取一个或多个数据库。 |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 获取可以还原的已删除数据库。 |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |获取数据库的异地冗余备份。 |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |还原 SQL 数据库。 |

  > [!TIP]
  > 有关演示如何对数据库执行时间点还原的示例 PowerShell 脚本，请参阅[使用 PowerShell 还原 SQL 数据库](scripts/sql-database-restore-database-powershell.md)。

#### <a name="managed-instance-database"></a>托管实例数据库

若要还原托管实例数据库，请参阅 [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)。

  | Cmdlet | 说明 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |获取一个或多个托管实例。 |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | 获取实例数据库。 |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |还原实例数据库。 |

### <a name="rest-api"></a>REST API

使用 REST API 还原单一数据库或共用数据库：

| API | 说明 |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |还原数据库。 |
| [获取创建或更新数据库状态](https://docs.microsoft.com/rest/api/sql/operations) |在还原操作过程中返回状态。 |

### <a name="azure-cli"></a>Azure CLI

#### <a name="single-azure-sql-database"></a>单个 Azure SQL 数据库

若要使用 Azure CLI 还原单一数据库或共用数据库，请参阅 [az sql db restore](/cli/azure/sql/db#az-sql-db-restore)。

#### <a name="managed-instance-database"></a>托管实例数据库

若要使用 Azure CLI 恢复托管实例数据库，请参阅[az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)。

## <a name="summary"></a>摘要

自动备份可保护数据库，使其免受用户和应用程序错误、意外的数据库删除和长时间中断的影响。 此内置的功能适用于所有服务层级和计算大小。

## <a name="next-steps"></a>后续步骤

- [业务连续性概述](sql-database-business-continuity.md)
- [SQL 数据库自动备份](sql-database-automated-backups.md)
- [长期保留](sql-database-long-term-retention.md)
- 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-active-geo-replication.md)或[自动故障转移组](sql-database-auto-failover-group.md)。
