---
title: Azure Active Directory 中的单页应用程序
description: 介绍什么是单页应用程序 (SPA)，以及有关此应用类型的协议流、注册和令牌到期的基础知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154740"
---
# <a name="single-page-applications"></a>单页应用程序

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

单页应用程序（Spa）通常结构化为在浏览器中运行的 JavaScript 表示层（前端）以及在服务器上运行并实现应用程序业务逻辑的 web API 后端。若要了解有关隐式授权的详细信息，并帮助你确定它是否适合你的应用程序方案，请参阅[了解 Azure Active Directory 中的 OAuth2 隐式授权流](v1-oauth2-implicit-grant-flow.md)。

在此方案中，当用户登录时，JavaScript 前端使用 [JavaScript (ADAL.JS) 的 Active Directory 身份验证库](https://github.com/AzureAD/azure-activedirectory-library-for-js)和隐式授权授予从 Azure AD 获取一个 ID 令牌 (id_token)。 该令牌随后被缓存，当客户端调用使用 OWIN 中间件进行保护的 Web API 后端时，客户端将该令牌作为持有者令牌附加到请求。

## <a name="diagram"></a>图表

![单页应用程序图示](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>协议流

1. 用户导航到 Web 应用程序。
1. 应用程序将 JavaScript 前端（表示层）返回到浏览器。
1. 用户通过单击登录链接等方式启动登录。 浏览器将 GET 发送到 Azure AD 授权终结点来请求一个 ID 令牌。 此请求将应用程序 ID 和答复 URL 包含在查询参数中。
1. Azure AD 根据在 Azure 门户中配置的已注册回复 URL 来验证回复 URL。
1. 用户在登录页面上进行登录。
1. 如果身份验证成功，Azure AD 会创建一个 ID 令牌，并将其作为 URL 片段（#）返回到应用程序的回复 URL。 对于生产应用程序，此回复 URL 应当采用 HTTPS 格式。 返回的令牌包括应用程序对该令牌进行验证所需的关于用户和 Azure AD 的声明。
1. 在浏览器中运行的 JavaScript 客户端代码从响应中提取用于保护对应用程序的 web API 后端的调用的标记。
1. 浏览器将调用应用程序的 web API 后端，并在授权标头中包含 ID 标记。 Azure AD 身份验证服务颁发一个 ID 令牌，如果资源与客户端 ID 相同（在本例中确实如此，因为 Web API 是应用自己的后端），则可以将该令牌用作代理令牌。

## <a name="code-samples"></a>代码示例

请参阅[单页面应用程序方案的代码示例](sample-v1-code.md#single-page-applications)。 请务必经常回来查看 - 我们会经常添加新示例。

## <a name="app-registration"></a>应用注册

* 单租户-如果只为组织生成应用程序，则必须使用 Azure 门户在公司的目录中注册该应用程序。
* 多租户-如果你正在构建可由组织外部用户使用的应用程序，则必须在你公司的目录中注册该应用程序，但必须在将要使用该应用程序的每个组织的目录中注册该应用程序。 要使应用程序在客户的目录中可用，可以提供一个供客户使用的注册流程，让客户许可应用程序的要求。 当他们针对应用程序进行注册时，系统会向他们显示一个对话框，其中显示了应用程序要求的权限，之后是表示许可的选项。 可能会要求其他组织中的管理员表示许可，具体取决于所需的权限。 当用户或管理员表示许可后，会在其目录中注册该应用程序。

注册应用程序之后，必须将其配置为使用 OAuth 2.0 隐式授予协议。 默认情况下，应用程序禁用此协议。 若要为应用程序启用 OAuth2 隐式授予协议，请从 Azure 门户编辑其应用程序清单，并将 "oauth2AllowImplicitFlow" 值设置为 true。 有关详细信息，请参阅[应用程序清单](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

## <a name="token-expiration"></a>令牌过期

使用 ADAL.js 可帮助：

* 刷新过期的令牌
* 请求访问令牌以调用 Web API 资源

成功身份验证后，Azure AD 会在用户的浏览器中写入一个 Cookie 来建立会话。 请注意，此会话存在于用户与 Azure AD 之间（而非存在于用户与 Web 应用程序之间）。 当一个令牌过期时，ADAL.js 将使用此会话以无提示方式获取另一个令牌。 ADAL.js 使用隐藏的 iFrame 来发送和接收使用 OAuth 隐式授予协议的请求。 如果这些资源支持跨域资源共享（CORS），在用户的目录中注册，并且用户在登录过程中指定了任何所需的许可，则 ADAL node.js 还可以使用此相同机制以无提示方式获取应用程序调用的其他 web API 资源的访问令牌。

## <a name="next-steps"></a>后续步骤

* 详细了解其他[应用程序类型和方案](app-types.md)
* 了解 Azure AD [身份验证基础知识](v1-authentication-scenarios.md)
