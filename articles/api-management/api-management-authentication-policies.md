---
title: Azure API 管理身份验证策略 | Microsoft Docs
description: 了解可在 Azure API 管理中使用的身份验证策略。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79473551"
---
# <a name="api-management-authentication-policies"></a>API 管理身份验证策略
本主题提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](https://go.microsoft.com/fwlink/?LinkID=398186)。

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a>身份验证策略

-   [使用基本方法进行身份验证](api-management-authentication-policies.md#Basic) - 使用基本身份验证方法对后端服务进行身份验证。

-   [使用客户端证书进行身份验证](api-management-authentication-policies.md#ClientCertificate) - 使用客户端证书对后端服务进行身份验证。

-   [用托管标识进行身份](api-management-authentication-policies.md#ManagedIdentity)验证-通过 API 管理服务的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)进行身份验证。

##  <a name="authenticate-with-basic"></a><a name="Basic"></a>使用基本方法进行身份验证
 通过 `authentication-basic` 策略使用基本身份验证方法对后端服务进行身份验证。 此策略有效地将 HTTP 授权标头设置为与策略中提供的凭据对应的值。

### <a name="policy-statement"></a>策略语句

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>示例

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>元素

|名称|说明|必选|
|----------|-----------------|--------------|
|authentication-basic|根元素。|是|

### <a name="attributes"></a>属性

|名称|说明|必选|默认|
|----------|-----------------|--------------|-------------|
|username|指定基本凭据的用户名。|是|空值|
|password|指定基本凭据的密码。|是|空值|

### <a name="usage"></a>使用情况
 此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。

-   **策略段：** 入站

-   **策略范围：** 所有范围

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a>使用客户端证书进行身份验证
 通过 `authentication-certificate` 策略使用客户端证书对后端服务进行身份验证。 需要首先将证书[安装到 API 管理](https://go.microsoft.com/fwlink/?LinkID=511599)，并由其指纹进行标识。

### <a name="policy-statement"></a>策略语句

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>示例

在此示例中，客户端证书是由指纹标识的。
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
在此示例中，客户端证书是由资源名称标识的。
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>元素  
  
|名称|说明|必选|  
|----------|-----------------|--------------|  
|authentication-certificate|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|名称|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|thumbprint|客户端证书的指纹。|必须提供 `thumbprint` 或 `certificate-id`。|空值|  
|certificate-id|证书资源名称。|必须提供 `thumbprint` 或 `certificate-id`。|空值|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
  
-   **策略范围：** 所有范围  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> 使用托管标识进行身份验证  
 使用 `authentication-managed-identity` 策略通过 API 管理服务的托管标识向后端服务进行身份验证。 此策略实质上使用托管标识从 Azure Active Directory 获取访问令牌以访问指定资源。 成功获取令牌后，策略将使用 `Authorization` 方案在 `Bearer` 标头中设置令牌的值。
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>示例  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>使用托管标识向后端服务进行身份验证
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>在发送请求策略中使用托管标识
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>元素  
  
|名称|说明|必选|  
|----------|-----------------|--------------|  
|authentication-managed-identity |根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|名称|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|resource|字符串。 Azure Active Directory 中的目标 web API （受保护资源）的应用 ID。|是|空值|  
|output-token-variable-name|字符串。 上下文变量的名称，它将令牌值接收为对象类型 `string`。 |否|空值|  
|ignore-error|布尔值。 如果设置为 `true`，即使未获得访问令牌，策略管道也将继续执行。|否|false|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
  
-   **策略范围：** 所有范围

## <a name="next-steps"></a>后续步骤
有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)
