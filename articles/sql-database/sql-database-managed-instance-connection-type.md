---
title: 托管实例连接类型
description: 了解托管实例连接类型
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "73819460"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL 数据库托管实例连接类型

本文介绍客户端如何根据连接类型连接到 Azure SQL 数据库托管实例。 下面提供了更改连接类型的脚本示例，以及有关更改默认连接设置的注意事项。

## <a name="connection-types"></a>连接类型

Azure SQL 数据库托管实例支持以下两种连接类型：

- **重定向（建议）：** 客户端直接与托管数据库的节点建立连接。 若要启用使用重定向的连接，必须打开防火墙和网络安全组以允许访问端口 1433 和 11000-11999。 数据包将直接发送到数据库，因此使用“通过代理重定向”可以改善延迟和吞吐量性能。
- **代理（默认值）：** 在此模式下，所有连接都使用代理网关组件。 若要启用连接，只需打开专用网络的端口 1433 和公共连接的端口 3342。 选择此模式可能导致延迟增大、吞吐量降低，具体取决于工作负荷的性质。 我们强烈建议使用“重定向”连接策略而不要使用“代理”连接策略，以最大程度地降低延迟和提高吞吐量。

## <a name="redirect-connection-type"></a>重定向连接类型

重定向连接类型意味着，在与 SQL 引擎建立 TCP 会话后，客户端会话从负载均衡器获取虚拟群集节点的目标虚拟 IP。 后续数据包会绕过网关直接流向虚拟群集节点。 下图演示了此流量流。

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> 重定向连接类型当前仅适用于专用终结点。 无论连接类型设置如何，通过公共终结点进行的连接都将通过代理。

## <a name="proxy-connection-type"></a>代理连接类型

代理连接类型意味着，TCP 会话是使用网关建立的，并且所有后续数据包都将流过该网关。 下图演示了此流量流。

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>使用 PowerShell 编写更改连接类型设置的脚本

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下 PowerShell 脚本显示了如何将托管实例的连接类型更改为“重定向”。

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>后续步骤

- [将数据库还原到托管实例](sql-database-managed-instance-get-started-restore.md)
- 了解如何[在托管实例上配置公共终结点](sql-database-managed-instance-public-endpoint-configure.md)
- 了解[托管实例连接体系结构](sql-database-managed-instance-connectivity-architecture.md)