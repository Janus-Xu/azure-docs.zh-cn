---
title: 将其他 Azure 存储帐户添加到 HDInsight
description: 了解如何将其他 Azure 存储帐户添加到现有 HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: d5dde8c45331cf8c443aba86c96ba12c8277472c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192478"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>将其他存储帐户添加到 HDInsight

了解如何使用脚本操作将其他 Azure 存储*帐户*添加到 HDInsight。 本文档中的步骤会将存储帐户添加到现有 HDInsight 群集。  本文适用于存储*帐户*（而不是默认群集存储帐户），而不是其他存储， [`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-store.md)例如[`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md)和。

> [!IMPORTANT]  
> 本文档中的信息是关于在创建群集后将其他存储帐户添加到群集。 有关如何在创建群集期间添加存储帐户的信息，请参阅[使用 Apache Hadoop、Apache Spark、Apache Kafka 等设置 HDInsight 中的群集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="prerequisites"></a>必备条件

* HDInsight 上的 Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./hadoop/apache-hadoop-linux-tutorial-get-started.md)。
* 存储帐户名和密钥。 请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md)。
* 如果使用 PowerShell，则需 AZ 模块。  请参阅 [Azure PowerShell 概述](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="how-it-works"></a>工作原理

在处理过程中，该脚本执行以下操作：

* 如果存储帐户已存在于群集的 core-site.xml 配置中，则脚本将退出，不执行进一步的操作。

* 验证存储帐户是否存在，以及是否可以使用密钥访问。

* 使用群集凭据对密钥进行加密。

* 将存储帐户添加到 core-site.xml 文件中。

* 停止并重启 Apache Oozie、Apache Hadoop YARN、Apache Hadoop MapReduce2 和 Apache Hadoop HDFS 服务。 通过停止和重启这些服务来使用新的存储帐户。

> [!WARNING]  
> 不支持在 HDInsight 群集之外的其他位置使用存储帐户。

## <a name="add-storage-account"></a>添加存储帐户

使用[脚本操作](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)应用更改时请注意以下事项：

|properties | 值 |
|---|---|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|节点类型|Head|
|parameters|`ACCOUNTNAME` `ACCOUNTKEY` `-p`（可选）|

* `ACCOUNTNAME` 是要添加到 HDInsight 群集的存储帐户的名称。
* `ACCOUNTKEY` 是 `ACCOUNTNAME` 的访问密钥。
* `-p` 是可选项。 如果指定此参数，则密钥不会加密，并以纯文本形式存储在 core-site.xml 文件中。

## <a name="verification"></a>验证

在 Azure 门户中查看 HDInsight 群集时，选择“属性”  下的“存储帐户”  项时不会显示通过此脚本操作添加的存储帐户。 Azure PowerShell 和 Azure CLI 也不显示其他存储帐户。 之所以未显示存储信息是因为该脚本只修改群集的 `core-site.xml` 配置。 使用 Azure 管理 API 检索群集信息时，不会使用此信息。

若要验证其他存储，请使用下面显示的方法之一：

### <a name="powershell"></a>PowerShell

脚本将返回与给定群集关联的存储帐户名称。 将 `CLUSTERNAME` 替换为实际群集名称，然后运行脚本。

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中的 `CLUSTERNAME` 是群集的名称。

1. 导航到“HDFS”   >   “配置” >   “高级” >   “自定义 core-site”。

1. 观察以 `fs.azure.account.key` 开头的密钥。 如此示例图像中所示，帐户名称将成为密钥的一部分：

   ![通过 Apache Ambari 进行验证](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>删除存储帐户

1. 在 Web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是群集的名称。

1. 导航到“HDFS”   >   “配置” >   “高级” >   “自定义 core-site”。

1. 删除以下密钥：
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

删除这些密钥并保存配置后，你需要逐一重启 Oozie、Yarn、MapReduce2、HDFS 和 Hive。

## <a name="known-issues"></a>已知问题

### <a name="storage-firewall"></a>存储防火墙

如果选择使用**所选网络**上的**防火墙和虚拟网络**限制来保护存储帐户，请确保启用 "**允许受信任的 Microsoft 服务**" 例外，以便 HDInsight 可以访问你的存储帐户`.`

### <a name="unable-to-access-storage-after-changing-key"></a>更改密钥后，无法访问存储

如果更改存储帐户的密钥，则 HDInsight 不再能够访问该存储帐户。 HDInsight 将 core-site.xml 中密钥的缓存副本用于群集。 必须更新此缓存的副本，使之匹配新的密钥。

再次运行脚本操作**不会**更新密钥，因为该脚本会检查存储帐户的某个条目是否已存在。 如果某个条目已存在，则不进行任何更改。

若要解决此问题，请执行以下操作：  
1. 删除存储帐户。
1. 添加存储帐户。

> [!IMPORTANT]  
> 对于附加到群集的主存储帐户，不支持轮换使用存储密钥。

### <a name="poor-performance"></a>性能低下

如果存储帐户与 HDInsight 群集位于不同的区域，则可能会遇到性能低下问题。 访问不同区域中的数据会在区域 Azure 数据中心外发送网络流量。 并且跨公共 internet，这可能会导致延迟。

### <a name="additional-charges"></a>额外费用

如果存储帐户与 HDInsight 群集位于不同的区域，则可能会在 Azure 帐单上发现额外的出口费用。 当数据离开区域数据中心时，将收取出口费用。 即使流量发往另一区域中的另一个 Azure 数据中心，也将收取此费用。

## <a name="next-steps"></a>后续步骤

已了解如何将其他存储帐户添加到现有 HDInsight 群集。 有关脚本操作的详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)
