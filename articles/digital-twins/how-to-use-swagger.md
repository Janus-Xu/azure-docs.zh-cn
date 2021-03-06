---
title: 如何使用 reference Swagger 文档-Azure 数字孪生 |Microsoft Docs
description: 了解如何使用 Azure 数字孪生 Swagger 参考文档。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77023287"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Azure 数字孪生 Swagger 参考文档

每个预配的 Azure 数字孪生实例都包含自己生成的 Swagger 参考文档。

[Swagger](https://swagger.io/) 或 [OpenAPI](https://www.openapis.org/) 将复杂的 API 信息整合到一个交互式和语言无关的参考资源中。 Swagger 提供有关要使用哪些 JSON 有效负载、HTTP 方法和特定终结点对 API 执行操作的关键参考资料。

## <a name="swagger-summary"></a>Swagger 摘要

Swagger 提供 API 的交互式摘要，这包括：

* API 和对象模型信息。
* REST API 终结点，用于指定必需的请求有效负载、标头、参数、上下文路径和 HTTP 方法。
* API 功能测试。
* 示例响应信息，用于验证和确认 HTTP 响应。
* 错误代码信息。

Swagger 是一种非常便利的工具，可用于帮助执行对 Azure 数字孪生管理 API 作出的开发和测试调用。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>参考资料

自动生成的 Swagger 参考资料提供了重要概念的快速概述、可用的管理 API 终结点和每个对象模型的描述，用于帮助开发和测试。

精简摘要对 API 进行了描述。

[![Swagger 摘要和 API 概述信息](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

还列出了管理 API 对象模型。

[![Swagger UI 底部列出的 swagger 模型](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

可以选择每个列出的对象模型来获取关键属性的更详细摘要：

[![扩展了 Swagger 模型以读取模型的内容](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

生成的 Swagger 对象模型可方便阅读所有可用的 Azure 数字孪生[对象和 api](./concepts-objectmodel-spatialgraph.md)。 开发人员在 Azure 数字孪生上生成解决方案时可以使用此资源。

## <a name="endpoint-summary"></a>终结点摘要

Swagger 还对编写管理 API 的所有终结点提供了全面概述。

每个列出的终结点还包括所需的请求信息，例如：

* 必需的参数。
* 必需的参数数据类型。
* 用于访问资源的 HTTP 方法。

[![Swagger UI 中显示的 swagger 终结点](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

选择每个资源以显示其其他内容，以获取更详细的概述。

## <a name="use-swagger-to-test-endpoints"></a>使用 Swagger 测试终结点

Swagger 提供的一个强大功能是能够直接通过文档 UI 测试 API 终结点。

选择特定的终结点后，将显示 "**试用**" 按钮。

[![Swagger 尝试此按钮](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

展开该部分会显示每个必需字段和可选参数的输入字段。 输入正确的值，并选择“执行”****。

[![Swagger 试用结果示例](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

执行测试后，可以验证响应数据。

## <a name="swagger-response-data"></a>Swagger 响应数据

每个列出的终结点还包括用以验证开发和测试的响应正文数据。 这些示例包括成功的 HTTP 请求的状态代码和 JSON。

[![Swagger JSON 响应示例](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

这些示例还包括用以帮助调试或改进失败测试的错误代码。

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 授权

> [!NOTE]
> * 创建 Azure 数字孪生资源的用户主体将具有空间管理员角色分配，并将能够为其他用户创建其他角色分配。 可以授权这些用户及其角色调用 Api。

1. 按照[快速入门](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app)中的步骤创建和配置 Azure Active Directory 应用程序。 或者，你可以重复使用现有的应用注册。

1. 将以下**重定向 URI**添加到 Azure Active Directory 应用注册：

    [![在 AAD 中注册 Swagger 重定向 url](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | 名称  | 替换为 | 示例 |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | 你的管理 REST API 在门户中找到的文档 URL  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. 选中 "**隐式授予** > **访问令牌**" 复选框可允许使用 OAuth 2.0 隐式授权流。 选择 "**配置**"，然后单击 "**保存**"。

1. 复制 Azure Active Directory 应用的**客户端 ID** 。

完成 Azure Active Directory 注册后：

1. 在 swagger 页面上选择 "**授权**" 按钮。

    [![选择 "Swagger 授权" 按钮](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. 将应用程序 ID 粘贴到**client_id**字段。

    [![Swagger client_id 字段](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. 然后，将重定向到以下成功模式。

    [![Swagger 重定向模式](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

若要详细了解 OAuth 2.0 保护的交互式测试请求，请阅读[官方文档](https://swagger.io/docs/specification/authentication/oauth2/)。

## <a name="next-steps"></a>后续步骤

- 若要阅读有关 Azure 数字孪生对象模型和空间智能图的详细信息，请阅读[了解 Azure 数字孪生对象模型](./concepts-objectmodel-spatialgraph.md)。

- 若要了解如何使用管理 API 进行身份验证，请阅读[使用 API 进行身份验证](./security-authenticating-apis.md)。
