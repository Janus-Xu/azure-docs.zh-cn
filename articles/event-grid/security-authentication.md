---
title: Azure 事件网格安全和身份验证
description: 本文介绍了对事件网格资源（WebHook、订阅、自定义主题）的访问者进行身份验证的不同方式
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532387"
---
# <a name="authenticating-access-to-event-grid-resources"></a>对事件网格资源的访问者进行身份验证

Azure 事件网格包含三种类型的身份验证：

- WebHook 事件传送
- 事件订阅
- 自定义主题发布

## <a name="webhook-event-delivery"></a>WebHook 事件传送

Webhook 是从 Azure 事件网格接收事件的多种方式之一。 当新事件准备就绪时，事件网格服务会向已配置的终结点 POST HTTP 请求，并在请求正文中包含该事件。

与众多支持 Webhook 的其他服务一样，事件网格需要你证明对 Webhook 的所有权，然后才能开始向该终结点传送事件。 此要求可防止恶意用户用大量事件淹没你的终结点。 如果使用下面列出的三项 Azure 服务中的任何一项，Azure 基础结构将自动进行此验证：

- 使用[事件网格连接器](https://docs.microsoft.com/connectors/azureeventgrid/)的 Azure 逻辑应用
- 通过 [Webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md) 实现 Azure 自动化
- 使用[事件网格触发器](../azure-functions/functions-bindings-event-grid.md)的 Azure Functions

如果使用其他任何类型的终结点（例如基于 HTTP 触发器的 Azure 函数），终结点代码需要参与事件网格的验证握手。 事件网格支持通过两种方式来验证订阅。

1. **同步握手**：在创建事件订阅时，事件网格会将订阅验证事件发送到终结点。 此事件的架构与任何其他事件网格事件类似。 此事件的数据部分包括一个 `validationCode` 属性。 您的应用程序验证验证请求是否适用于预期的事件订阅，并以同步方式返回响应中的验证代码。 所有事件网格版本都支持此握手机制。

2. **异步握手**：在某些情况下，不能以同步方式返回 ValidationCode。 例如，如果你使用第三方服务（如[`Zapier`](https://zapier.com)或[IFTTT](https://ifttt.com/)），则无法以编程方式使用验证代码做出响应。

   从版本 2018-05-01-preview 开始，事件网格支持手动验证握手。 如果你在创建事件订阅时使用的 SDK 或工具使用了 API 版本 2018-05-01-preview 或更高版本，则事件网格将在订阅验证事件的数据部分中发送 `validationUrl` 属性。 若要完成握手，请在事件数据中查找该 URL，并对其执行 GET 请求。 你可以使用 REST 客户端或 Web 浏览器。

   提供的 URL 有效时间为**5 分钟**。 在该时间内，事件订阅的预配状态为 `AwaitingManualAction`。 如果在 5 分钟内未完成手动验证，则配置状态被设为 `Failed`。 你将必须在开始手动验证之前重新创建事件订阅。

   此身份验证机制还要求 Webhook 终结点返回 HTTP 状态代码 200，这样它就知道验证事件的 POST 已被接受，然后它就可以进入手动验证模式。 换言之，如果终结点返回200，但不同步返回验证响应，则该模式将转换为手动验证模式。 如果在 5 分钟内在验证 URL 上出现 GET，则可以认为验证握手成功。

> [!NOTE]
> 不支持使用自签名证书进行验证。 改用来自证书颁发机构 (CA) 的签名证书。

### <a name="validation-details"></a>验证详细信息

- 在创建/更新事件订阅时，事件网格会将一个订阅验证事件发送到目标终结点。
- 该事件包含标头值“aeg-event-type:SubscriptionValidation”。
- 事件正文具有与其他事件网格事件相同的架构。
- 该事件的 eventType 属性是 `Microsoft.EventGrid.SubscriptionValidationEvent`。
- 该事件的 data 属性包括一个 `validationCode` 属性，其中含有随机生成的字符串。 例如，“validationCode: acb13…”。
- 事件数据还包括 `validationUrl` 属性，其中包含用于手动验证订阅的 URL。
- 该数组仅包含验证事件。 你回显验证代码后，事件网格会以单独的请求发送其他事件。
- EventGrid DataPlane SDK 包含对应订阅验证事件数据和订阅验证响应的类。

以下示例显示了 SubscriptionValidationEvent 示例：

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

为证明终结点所有权，请在 validationResponse 属性中回显 验证代码，如下例所示：

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

你必须返回 HTTP 200 OK 响应状态代码。 HTTP 202 Accepted 未被识别为有效的事件网格订阅验证响应。 http 请求必须在 30 秒内完成。 如果操作未在 30 秒内完成，则该操作将被取消，并可能在 5 秒后重新尝试。 如果所有尝试均失败，系统会将它视为验证握手错误。

另外，还可以通过将 GET 请求发送到验证 URL 来手动验证订阅。 事件订阅将一直处于挂起状态，直到得到验证。 验证 URL 使用端口 553。 如果防火墙规则阻止端口 553，则可能需更新规则才能成功进行手动握手。

有关处理订阅验证握手的示例，请参阅 [C# 示例](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)。

## <a name="troubleshooting-eventsubsciption-validation"></a>EventSubsciption 验证故障排除

创建事件订阅期间，是否看到“尝试验证所提供的终结点 https:\//your-endpoint-here 失败。 有关更多详细信息，请访问 https:\//aka.ms/esvalidation ”等错误消息，它表示验证握手出现故障。 若要解决此错误，请验证以下各方面：

- 使用 Postman 或卷曲或类似的工具，通过[示例 SubscriptionValidationEvent](#validation-details)请求正文执行到 webhook URL 的 HTTP POST。
- 如果 webhook 要实现同步验证握手机制，请验证是否在响应中返回 ValidationCode。
- 如果 webhook 要实现异步验证握手机制，请验证你的 HTTP POST 是否返回200。
- 如果 webhook 在响应中返回403（禁止访问），请检查 webhook 是否在 Azure 应用程序网关或 Web 应用程序防火墙后面。 如果是，则需要禁用这些防火墙规则，并再次执行 HTTP POST：

  920300（请求缺少 Accept 标头，我们可以解决此问题）

  942430（受限制的 SQL 字符异常检测（args）：超出了特殊字符数（12））

  920230（检测到多个 URL 编码）

  942130（SQL 注入攻击：检测到 SQL Tautology。）

  931130（可能的远程文件包含（RFI）攻击 = 域外引用/链接）

### <a name="event-delivery-security"></a>事件传递安全性

#### <a name="azure-ad"></a>Azure AD

可以对 Webhook 终结点进行保护，方法是：使用 Azure Active Directory 对事件网格进行身份验证和授权，以便将事件发布到终结点。 你需要创建一个 Azure Active Directory 应用程序，在应用程序授权事件网格中创建角色和服务主体，并将事件订阅配置为使用 Azure AD 应用程序。 [了解如何为 AAD 配置事件网格](secure-webhook-delivery.md)。

#### <a name="query-parameters"></a>查询参数

在创建事件订阅时，可以通过向 Webhook URL 中添加查询参数来保护 Webhook 终结点。 将这些查询参数之一设置为某个机密，例如[访问令牌](https://en.wikipedia.org/wiki/Access_token)。 Webhook 可以使用该机密来识别事件是否来自具有有效权限的事件网格。 事件网格会在前往 Webhook 的每个事件传递中包括这些查询参数。

编辑事件订阅时，除非在 Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 中使用了 [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) 参数，否则，不会显示或返回查询参数。

最后，请务必注意 Azure 事件网格仅支持 HTTPS Webhook 终结点。

## <a name="event-subscription"></a>事件订阅

若要订阅事件，必须证明你有权访问事件源和处理程序。 上一部分中假定你拥有一个 WebHook。 如果你使用不是 WebHook 的事件处理程序（例如事件中心或队列存储），则需要对该资源具有写入访问权限。 此权限检查可防止未经授权的用户向你的资源发送事件。

你必须在作为事件源的资源上具有 **Microsoft.EventGrid/EventSubscriptions/Write** 权限。 因为要在资源范围内写入新的订阅，所以需要此权限。 所需资源因是订阅系统主题还是订阅自定义主题而异。 本部分介绍了这两种类型。

### <a name="system-topics-azure-service-publishers"></a>系统主题（Azure 服务发布服务器）

对于系统主题，需要在资源范围内写入新事件订阅的权限，才能发布该事件。 该资源的格式为：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

例如，若要订阅存储帐户上名为“myacct”的事件，需要 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct` 的 Microsoft.EventGrid/EventSubscriptions/Write 权限****

### <a name="custom-topics"></a>自定义主题

对于自定义主题，需要在事件网格主题范围内写入新事件订阅的权限。 该资源的格式为：`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

例如，若要订阅名为“mytopic”的自定义主题，需要 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic` 的 Microsoft.EventGrid/EventSubscriptions/Write 权限****

## <a name="custom-topic-publishing"></a>自定义主题发布

自定义主题使用共享访问签名 (SAS) 或密钥身份验证。 建议使用 SAS，但密钥身份验证提供简单的编程，并与多个现有 webhook 发布服务器兼容。

HTTP 标头中包括身份验证值。 对于 SAS，使用 aeg-sas-token 作为标头值****。 对于密钥身份验证，使用 aeg-sas-key 作为标头值****。

### <a name="key-authentication"></a>密钥身份验证

密钥身份验证是最简单的身份验证形式。 使用以下格式：`aeg-sas-key: <your key>`

例如，使用以下项传递密钥：

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS 令牌

事件网格的 SAS 令牌包括资源、过期时间和签名。 SAS 令牌的格式是：`r={resource}&e={expiration}&s={signature}`。

资源是要将事件发送到的事件网格主题的路径。 例如，有效的资源路径为： `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`。 若要查看所有受支持的 API 版本，请参阅[EventGrid 资源类型](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)。 

从密钥生成签名。

例如，有效的 aeg-sas-token 值是****：

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

以下示例会创建用于事件网格的 SAS 令牌：

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>静态加密

事件网格服务写入到磁盘的所有事件或数据都通过 Microsoft 托管的密钥进行加密，确保静态加密。 此外，事件或数据保留的最长时间为24小时，并遵循[事件网格重试策略](delivery-and-retry.md)。 事件网格将在 24 小时或事件生存时间（以两者中较小者为准）过后自动删除所有事件或数据。

## <a name="endpoint-validation-with-cloudevents-v10"></a>使用 CloudEvents v1.0 进行终结点验证
如果熟悉事件网格，你可能会了解事件网格的用于防止滥用的终结点验证握手。 CloudEvents v1.0 使用 HTTP OPTIONS 方法来实现自己的[滥用保护语义](security-authentication.md#webhook-event-delivery)。 可在[此处](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)阅读详细信息。 使用 CloudEvents 架构进行输出时，事件网格可与 CloudEvents v1.0 滥用保护配合使用，取代事件网格验证事件机制。

## <a name="next-steps"></a>后续步骤

- 有关事件网格的介绍，请参阅[关于事件网格](overview.md)
