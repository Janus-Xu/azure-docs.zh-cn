---
title: Azure Service Fabric 中心机密存储
description: 本文介绍如何使用 Azure Service Fabric 中的中心机密存储。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770415"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure Service Fabric 中的中心机密存储 
本文介绍如何使用 Azure Service Fabric 中的中心机密存储 (CSS) 在 Service Fabric 应用程序中创建机密。 CSS 是一个本地机密存储缓存，用于保存敏感数据，例如，已在内存中加密的密码、令牌和密钥。

## <a name="enable-central-secrets-store"></a>启用中心机密存储
将以下脚本添加到群集配置中的 `fabricSettings` 下即可启用 CSS。 对于 CSS，我们建议使用除群集证书以外的证书。 确保在所有节点上安装加密证书，并且 `NetworkService` 对证书的私钥拥有读取权限。
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>声明机密资源
您可以使用 REST API 创建机密资源。
  > [!NOTE] 
  > 如果群集使用 windows 身份验证，则会通过不安全的 HTTP 通道发送 REST 请求。 建议使用具有安全终结点的基于 X509 的群集。

若要使用`supersecret` REST API 创建机密资源，请向`https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`发出 PUT 请求。 需要使用群集证书或管理员客户端证书来创建密钥资源。

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>设置机密值

使用以下脚本来使用 REST API 设置机密值。
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>检查机密值
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>在应用程序中使用机密

请按照以下步骤在 Service Fabric 应用程序中使用该机密。

1. 使用以下代码片段在**settings .xml**文件中添加一个节。 请注意，此处的值为 {`secretname:version`} 格式。

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. 导入**applicationmanifest.xml**中的部分。
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   环境变量`SecretPath`将指向存储所有机密的目录。 节下面列出的`testsecrets`每个参数都存储在单独的文件中。 应用程序现在可以使用机密，如下所示：
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 将机密装载到容器中。 使密钥在容器内可用所需的唯一更改是中`specify` `<ConfigPackage>`的装入点。
以下代码片段是修改后的**applicationmanifest.xml**。  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   密码在容器中的装入点下可用。

1. 可以通过指定`Type='SecretsStoreRef`将机密绑定到进程环境变量。 以下代码片段举例说明了如何`supersecret`将版本`ver1`绑定到**servicemanifest.xml**中的环境变量`MySuperSecret` 。

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>后续步骤
详细了解[应用程序和服务安全性](service-fabric-application-and-service-security.md)。
