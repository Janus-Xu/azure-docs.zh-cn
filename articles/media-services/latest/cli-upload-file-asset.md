---
title: Azure CLI 脚本示例 - 将文件上传到容器 | Microsoft Docs
description: 本文介绍如何使用 Azure CLI 脚本将本地文件上传到存储容器。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 5d1dc90832a399aa9ee253b2c7962b8536c1926a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382998"
---
# <a name="azure-cli-example-upload-a-local-file-to-a-container"></a>Azure CLI 示例：将本地文件上传到容器

本文中的 Azure CLI 脚本演示如何将本地文件上传到存储容器。

## <a name="prerequisites"></a>先决条件

* [创建媒体服务帐户](create-account-cli-how-to.md)。
* 查看[管理资产](manage-asset-concept.md)。

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>示例脚本

```azurecli-interactive
#!/bin/bash
# Update the following variables for your own settings:
storageAccountName=build2018storage
assetContainer="asset-4c834446-7e55-4760-9a25-f2d4fb1f4657"
localFile="..\Media\ignite-short.mp4"
blobName="ignite-short.mp4"
sasToken="?sv=2015-07-08&sr=c&sig=u1uy9OIeXnZUEN62hE0bDgg%2FPXYgRDNGnQxE%2BSi51dM%3D&se=2018-04-29T18:42:02Z&sp=rwl"
# Use the az storage modules to upload a local file to the container using the SAS URL from previous step.
# If you are logged in already to the subscription with access to the storage account, you do not need to use the --sas-token at all. Just eliminate it below.
# The container name is in the SAS URL path, and should be set with the -c option.
# Use the -f option to point to a local file on your machine.
# Use the -n option to name the blob in storage.
# Use the --account-name option to point to the storage account name to use 
# Use the --sas-token option to place the SAS token after the query string from previous step. 
# NOTE that the SAS Token is only good for up to 24 hours max. 
#
az storage blob upload \
    -c $assetContainer \
    -f $localFile \
    -n $blobName \
    --account-name $storageAccountName \
    --sas-token $sasToken \
echo "press  [ENTER]  to continue."
read continue
```

## <a name="next-steps"></a>后续步骤

[媒体服务概述](media-services-overview.md)
