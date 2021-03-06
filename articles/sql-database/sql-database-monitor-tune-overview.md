---
title: 监视和性能优化
description: 概述 Azure SQL 数据库中的监视和性能优化功能与方法。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268725"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Azure SQL 数据库中的监视和性能优化

若要监视 Azure SQL 数据库中某个数据库的性能，首先需要监视工作负荷使用的、相对于在选择特定服务层和性能级别所选的数据库性能级别的 CPU 和 IO 资源。 为了帮助实现此目的，Azure SQL 数据库将发出资源指标，可以在 Azure 门户中或使用以下 SQL 管理工具之一查看这些指标：[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 或 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。

对于单一数据库和共用数据库，Azure SQL 数据库提供多个数据库顾问来提供智能性能优化建议以及用于提高性能的自动优化选项。 此外，Query Performance Insight 会显示有关针对单一数据库和共用数据库运行的、CPU 和 IO 占用量最高的查询的详细信息。

Azure SQL 数据库提供基于人工智能的其他监视和优化功能，以帮助排查数据库和解决方案的性能问题并实现其最高性能。 您可以选择将这些[智能见解](sql-database-intelligent-insights.md)和其他 SQL 数据库资源日志和指标的[流式导出](sql-database-metrics-diag-logging.md)配置为多个目标中的一个，以实现消耗和分析，尤其是使用[SQL Analytics](../azure-monitor/insights/azure-sql.md)）。 Azure SQL Analytics 是一种高级云监视解决方案，用于在单个视图中跨多个订阅大规模监视所有 Azure SQL 数据库的性能。 有关可导出的日志和指标列表，请参阅[可导出的诊断遥测数据](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

最后，SQL 具有自身的监视和诊断功能，以及 [SQL Server 查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)和[动态管理视图 (DMV)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)。 有关用于监视各种性能问题的脚本，请参阅[使用 DMV 进行监视](sql-database-monitoring-with-dmvs.md)。

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure 门户中的监视和优化功能

在 Azure 门户中，Azure SQL 数据库针对所有部署类型提供资源指标监视功能。 此外，对于单一数据库和共用数据库，数据库顾问和 Query Performance Insight 会提供查询优化建议和查询性能分析。 最后，在 Azure 门户中，可为逻辑服务器及其单一数据库和共用数据库启用自动优化。

### <a name="sql-database-resource-monitoring"></a>SQL 数据库资源监视

可以在 Azure 门户上的“指标”视图中快速监视以下资源指标： 

- **DTU 使用率**

  检查数据库或弹性池是否长时间接近 100% 的 DTU 使用率。 较高的 DTU 使用率表示工作负荷可能需要更多的 CPU 或 IO 资源。 它还可能指示查询需要优化。
- **CPU 使用率**

  检查数据库、弹性池或托管实例是否长时间接近 100% 的 CPU 使用率。 较高的 CPU 使用率表示工作负荷可能需要更多的 CPU 或 IO 资源。 它还可能指示查询需要优化。
- **IO 使用率**

  查看数据库、弹性池或管理实例是否达到了基础存储的 IO 限制。 较高的 IO 使用率表示工作负荷可能需要更多的 CPU 或 IO 资源。 它还可能指示查询需要优化。

  ![资源指标](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>数据库顾问
Azure SQL 数据库包含针对单一数据库和共用数据库提供性能优化建议的[数据库顾问](sql-database-advisor.md)。 这些建议会在 Azure 门户中提供，也可以使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor) 来查看。 还可以启用[自动优化](sql-database-automatic-tuning.md)，使 SQL 数据库能够自动实施这些优化建议。

### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) 在 Azure 门户中显示针对单一数据库和共用数据库运行的、资源消耗量最高且运行时间最长的查询的性能。

## <a name="generate-intelligent-assessments-of-performance-issues"></a>生成性能问题的智能评估

Azure SQL 数据库[智能见解](sql-database-intelligent-insights.md)使用内置智能通过人工智能持续监视数据库使用情况，并检测导致性能不佳的干扰性事件。 智能见解可根据查询执行等待时间、错误或超时自动检测 Azure SQL 数据库中数据库的性能问题。 检测到问题后，将执行详细的分析，以生成资源日志（称为 SQLInsights）和[问题的智能评估](sql-database-intelligent-insights-troubleshoot-performance.md)。 此评估包含对数据库性能问题的根本原因分析，以及为性能改进而提供的可行性建议。

Intelligent Insights 是 Azure 内置智能的一项独特功能，提供以下功能价值：

- 主动监视
- 定制的性能见解
- 数据库性能下降的早期检测
- 已检测出问题的根本原因分析
- 性能改进建议
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>启用指标和资源日志的流式导出

可以启用并配置将[诊断遥测数据（包括智能见解资源日志）流式导出](sql-database-metrics-diag-logging.md)到多个目标之一。 使用[SQL 分析](../azure-monitor/insights/azure-sql.md)和其他功能来使用此附加的诊断遥测来识别和解决性能问题。

将诊断设置配置为将单个数据库、共用数据库、弹性池、托管实例和实例数据库的指标和资源日志的类别流式传输到以下 Azure 资源之一。

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure monitor 中的 Log Analytics 工作区

可以将指标和资源日志流式传输到[Azure Monitor 中的 Log Analytics 工作区](../azure-monitor/platform/resource-logs-collect-workspace.md)。 SQL Analytics 可以使用此处流式传输的数据， [SQL Analytics](../azure-monitor/insights/azure-sql.md)是一种仅限云的监视解决方案，它提供对数据库的智能监视，其中包括性能报告、警报和缓解建议。 流式传输到 Log Analytics 工作区的数据可以使用收集的其他监视数据进行分析，还可以利用其他 Azure Monitor 功能，如警报和可视化效果。

### <a name="azure-event-hubs"></a>Azure 事件中心

可以将指标和资源日志流式传输到[Azure 事件中心](../azure-monitor/platform/resource-logs-stream-event-hubs.md)。 将诊断遥测流式传输到事件中心，以提供以下功能：

- **将日志流式传输到第三方日志记录和遥测系统**

  将所有指标和资源日志流式传输到单个事件中心，以便将日志数据传递给第三方 SIEM 或 log analytics 工具。
- **构建自定义遥测和日志记录平台**

  事件中心高度可缩放的发布-订阅特性使你可以灵活地将指标和资源日志引入自定义遥测平台。 有关详细信息，请参阅 [Designing and Sizing a Global Scale Telemetry Platform on Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)（在 Azure 事件中心设计全球规模的遥测平台并设置其大小）。
- **通过将数据流式传输到 Power BI 来查看服务运行状况**

  使用事件中心、流分析和 Power BI 将诊断数据转换为对 Azure 服务的近乎实时见解。 有关此解决方案的详细信息，请参阅[流分析和 Power BI：用于流式处理数据的实时分析仪表板](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)。

### <a name="azure-storage"></a>Azure 存储

将指标和资源日志流式传输到[Azure 存储](../azure-monitor/platform/resource-logs-collect-storage.md)。 使用 Azure 存储将大量诊断遥测数据存档，只需一小部分成本即可实现以前两个流式处理选项。

## <a name="use-extended-events-in-the-sql-database-engine"></a>在 SQL 数据库引擎中使用扩展事件

此外，还可以使用 SQL 中的[扩展事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)进行其他高级监视和故障排除。 扩展事件体系结构使用户能够收集必要的数据，以进行故障排除或确定性能问题所需的数据量。 有关在 SQL 数据库中使用扩展事件的信息，请参阅[Sql 数据库中的扩展事件](sql-database-xevent-db-diff-from-svr.md)。

## <a name="next-steps"></a>后续步骤

- 有关单一数据库和池数据库的智能性能建议的详细信息，请参阅[数据库顾问性能建议](sql-database-advisor.md)。
- 有关借助自动诊断和性能问题的根本原因分析自动监视数据库性能的详细信息，请参阅 [Azure SQL 智能见解](sql-database-intelligent-insights.md)。
'''''''''