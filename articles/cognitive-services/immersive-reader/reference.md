---
title: 沉浸式读者 SDK 参考
titleSuffix: Azure Cognitive Services
description: 沉浸式读者 SDK 包含一个 JavaScript 库，使你能够将沉浸式读者集成到你的应用程序中。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: cb88fb24ceed943d4104da6914959e4b79c35571
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231911"
---
# <a name="immersive-reader-sdk-reference-guide"></a>沉浸式读者 SDK 参考指南

沉浸式读者 SDK 包含一个 JavaScript 库，使你能够将沉浸式读者集成到你的应用程序中。

## <a name="functions"></a>函数

SDK 公开函数：

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

`iframe`在 web 应用程序中的内启动沉浸式读取器。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>参数

| 名称 | 类型 | 说明 |
| ---- | ---- |------------ |
| `token` | 字符串 | Azure AD 身份验证令牌。 |
| `subdomain` | 字符串 | Azure 中沉浸式读者资源的自定义子域。 |
| `content` | [内容](#content) | 一个对象，该对象包含要在沉浸式读取器中显示的内容。 |
| `options` | [选项](#options) | 用于配置沉浸式读者的某些行为的选项。 可选。 |

### <a name="returns"></a>返回

返回`Promise<LaunchResponse>`，它可以解析沉浸式读取器的加载时间。 `Promise`解析为[`LaunchResponse`](#launchresponse)对象。

### <a name="exceptions"></a>例外

如果沉浸`Promise`式读取器加载失败[`Error`](#error) ，将使用对象拒绝返回的。 有关详细信息，请参阅[错误代码](#error-codes)。

## <a name="close"></a>关闭

关闭沉浸式阅读器。

此函数的一个示例用例是通过设置```hideExitButton: true``` "[选项](#options)" 中的 "退出" 按钮是否处于隐藏状态。 然后，在单击该按钮时，另一个按钮（例如移动标头的后退```close```箭头）可以调用此函数。

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

此函数将样式和更新文档的沉浸式读者按钮元素。 如果```options.elements```提供了，则此函数将呈现中```options.elements```的按钮。 否则，这些按钮将在具有类```immersive-reader-button```的文档元素中呈现。

当窗口加载时，SDK 会自动调用此函数。

有关更多呈现选项，请参阅[可选属性](#optional-attributes)。

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>参数

| 名称 | 类型 | 说明 |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | 用于配置 renderButtons 函数的某些行为的选项。 可选。 |

## <a name="types"></a>类型

### <a name="content"></a>内容

包含要在沉浸式阅读器中显示的内容。

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>区块

单个数据块，将传入沉浸式读取器的内容。

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

包含对的调用的响应`ImmersiveReader.launchAsync`。

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy 枚举

用于设置沉浸式读者 cookie 使用情况的策略的枚举。 请参阅[选项](#options)。

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>支持的 MIME 类型

| MIME 类型 | 描述 |
| --------- | ----------- |
| text/plain | 纯文本。 |
| text/html | HTML 内容。 [了解详细信息](#html-support)|
| application/mathml + xml | 数学标记语言（MathML）。 [了解详细信息](./how-to/display-math.md)。
| application/vnd.apple.mpegurl. vnd.openxmlformats-officedocument.spreadsheetml.sheet. wordprocessingml | Microsoft Word .docx 格式的文档。

### <a name="html-support"></a>HTML 支持

| HTML | 支持的内容 |
| --------- | ----------- |
| 字体样式 | 粗体、斜体、下划线、代码、删除线、上标、下标 |
| 无序列表 | 光盘、圆形、方形 |
| 排序列表 | Decimal，大写字母，小写字母，上罗马，小写罗马字 |

不支持的标记将呈现为同等的。 当前不支持映像和表。

### <a name="options"></a>选项

包含配置沉浸式读者的某些行为的属性。

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

用于呈现沉浸式读者按钮的选项。

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>错误

包含有关错误的信息。

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>错误代码

| 代码 | 描述 |
| ---- | ----------- |
| BadArgument | 提供的参数无效，有关`message`详细信息，请参阅。 |
| 超时 | 沉浸式读取器无法在指定的超时内加载。 |
| TokenExpired | 提供的令牌已过期。 |
| 已中止 | 超出了调用速率限制。 |

## <a name="launching-the-immersive-reader"></a>启动沉浸式阅读器

SDK 为启动沉浸式阅读器的按钮提供默认样式。 使用`immersive-reader-button` class 特性启用此样式设置。 有关更多详细信息，请参阅[此文](./how-to-customize-launch-button.md)。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>可选属性

使用以下属性来配置按钮的外观。

| 特性 | 描述 |
| --------- | ----------- |
| `data-button-style` | 设置按钮的样式。 可以是 `icon`、`text` 或 `iconAndText`。 默认为 `icon`。 |
| `data-locale` | 设置区域设置。 例如，`en-US` 或 `fr-FR`。 默认为英语`en`。 |
| `data-icon-px-size` | 设置图标的大小（以像素为单位）。 默认值为20px。 |

## <a name="browser-support"></a>浏览器支持

使用以下浏览器的最新版本，以获取沉浸式读者的最佳体验。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>后续步骤

* 探索 [GitHub 上的沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [快速入门：创建用于启动沉浸式读取器的 web 应用程序（c #）](./quickstart.md)
