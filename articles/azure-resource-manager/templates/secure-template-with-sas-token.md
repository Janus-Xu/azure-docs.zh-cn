---
title: 使用 SAS 令牌安全部署模板
description: 使用受 SAS 令牌保护的 Azure 资源管理器模板将资源部署到 Azure。 显示 Azure PowerShell 和 Azure CLI。
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80156389"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>部署具有 SAS 令牌的专用 ARM 模板

当 Azure 资源管理器（ARM）模板位于存储帐户中时，你可以限制对该模板的访问，以避免公开公开。 访问受保护模板的方法是：为模板创建一个共享访问签名 (SAS) 令牌，在部署时提供该令牌。 本文介绍如何使用 Azure PowerShell 或 Azure CLI 通过 SAS 令牌来部署模板。

## <a name="create-storage-account-with-secured-container"></a>使用受保护的容器创建存储帐户

以下脚本创建一个存储帐户和容器，其中的公共访问权限已禁用。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>将模板上传到存储帐户

现在可以将模板上传到存储帐户了。 提供要使用的模板的路径。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>在部署期间提供 SAS 令牌

要在存储帐户中部署专用模板，请生成 SAS 令牌，并将其包括在模板的 URI 中。 设置到期时间以允许足够的时间来完成部署。

> [!IMPORTANT]
> 只有帐户所有者才能访问包含该模板的 Blob。 但是，如果为 blob 创建 SAS 令牌，则拥有该 URI 的任何人都可以访问 blob。 如果其他用户截获了该 URI，则此用户可以访问该模板。 使用 SAS 令牌是限制对模板的访问的好办法，但不应直接在模板中包括密码等敏感数据。
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

有关将 SAS 令牌与链接模板配合使用的示例，请参阅[将已链接的模版与 Azure 资源管理器配合使用](linked-templates.md)。


## <a name="next-steps"></a>后续步骤
* 有关部署模板的简介，请参阅[使用 ARM 模板部署资源和 Azure PowerShell](deploy-powershell.md)。
* 若要在模板中定义参数，请参阅[创作模板](template-syntax.md#parameters)。
