---
title: 多设备对话（预览）-语音服务
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7c30ee2ef4a6ab0cd4241cac921a59eeadf5ce17
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81401048"
---
# <a name="what-is-multi-device-conversation-preview"></a>什么是多设备对话（预览版）？

**多设备对话**使你可以轻松地在多个客户端之间创建语音或文本对话，并协调它们之间发送的消息。

通过**多设备会话**，你可以：

- 将多个客户端连接到相同的会话中，并管理它们之间的消息发送和接收。
- 轻松地转录每个客户端的音频，并通过可选的翻译向其他客户端发送脚本。
- 在客户端之间轻松发送文本消息，并提供可选的翻译。

可以构建跨设备阵列工作的功能或解决方案。 每个设备可以独立地将消息（转录音频或即时消息）发送到所有其他设备。

虽然[**会话**](conversation-transcription.md)脚本在具有多通道麦克风阵列的单个设备上工作，但**多设备会话**适用于具有多个设备的方案，每个设备都有一个麦克风。

>[!IMPORTANT]
> 多设备会话**不**支持在客户端之间发送音频文件：仅限脚本和/或翻译。

## <a name="key-features"></a>主要功能

- **实时**脚本–每个人都将收到会话的脚本，因此，他们可以实时跟踪文本或保存以供稍后使用。
- **实时翻译**–对于超过60个支持的文本翻译[语言](language-support.md#text-languages)，用户可以将会话转换为其首选语言。
- **可读的脚本**-通过标点和句尾，脚本和转换易于理解。
- **语音或文本输入**–每个用户都可以在自己的设备上讲话或键入，具体取决于为参与者选择的语言启用的语言支持功能。 请参阅[语言支持](language-support.md#speech-to-text)。
- **消息中继**-多设备会话服务将使用其所选的语言将一台客户端发送的消息分发给其他客户端。
- **消息标识**–用户在会话中收到的每条消息都将用发送它的用户的昵称进行标记。

## <a name="use-cases"></a>用例

### <a name="lightweight-conversations"></a>轻型对话

创建和加入会话非常简单。 一个用户将充当 "主机" 并创建一个会话，该会话将生成一个随机的五个字母的对话代码和一个 QR 码。 所有其他用户都可以通过键入对话代码或扫描 QR 码来加入会话。 

由于用户通过会话代码加入，而不需要共享联系信息，因此可以轻松地创建快速的即时对话。

### <a name="inclusive-meetings"></a>非独占会议

实时的脚本和翻译可帮助说出不同语言和/或失聪或听力有障碍的用户访问对话。 每个用户还可以通过讲述其首选语言或发送即时消息，积极参与对话。

### <a name="presentations"></a>演示文稿

你还可以在屏幕上以及观众成员自己的设备上提供演示文稿和讲座的标题。 在受众加入会话代码后，他们可以在自己的设备上以其首选语言查看脚本。

> [!NOTE]
> 若要查看示例，请查看[演示文稿转换器](https://www.microsoft.com/translator/apps/presentation-translator/)，其中使用了多设备对话服务的 PowerPoint 外接程序。 可以在[此处](https://www.microsoft.com/download/details.aspx?id=55024)下载。

## <a name="how-it-works"></a>工作原理

所有客户端都将使用语音 SDK 来创建或加入会话。 语音 SDK 与多设备会话服务进行交互，该服务管理会话的生存期，包括参与者列表、每个客户端选择的语言以及发送的消息。  

每个客户端都可以发送音频或即时消息。 服务将使用语音识别将音频转换为文本，并按原样发送即时消息。 如果客户端选择不同的语言，则该服务会将所有消息都转换为每个客户端的指定语言。

![多设备对话概述关系图](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>会话、主机和参与者概述

会话**是指**一个用户为要加入的其他参与用户启动的会话。 所有客户端都使用五行**会话代码**连接到会话。

每个会话创建的元数据包括：
-    会话的开始和结束时间的时间戳
-    会话中所有参与者的列表，其中包括每个用户选择的昵称以及语音或文本输入的主要语言。


会话中有两种类型的用户：**主机**和**参与者**。

**主机**是启动会话的用户，以及作为该对话的管理员的用户。
- 每个会话只能有一个主机
- 在会话期间，主机必须连接到会话。 如果主机离开会话，则会话将为所有其他参与者结束。
- 主机具有几个额外的控制来管理会话： 
    - 锁定会话-阻止其他参与者加入
    - 静音所有参与者-阻止其他参与者向对话发送任何邮件，无论是从语音邮件还是即时消息转录
    - 静音单个参与者
    - 取消所有参与者的静音
    - 取消单个参与者的静音

**参与者**是加入对话的用户。
- 参与者可以随时离开和重新加入同一会话，而无需结束其他参与者的会话。
- 参与者不能锁定会话，也不能取消锁定/取消静音

> [!NOTE]
> 每个会话最多可以有100个参与者，在任何给定时间都可以同时说10个参与者。

## <a name="language-support"></a>语言支持

创建或加入会话时，每个用户都必须选择一种**主要语言**：他们将使用的语言，并在中发送即时消息，还必须选择他们将看到其他用户的消息的语言。

有两种类型的语言：**语音转换文本**和纯**文本**：
- 如果用户选择一种**语音到文本**语言作为其主要语言，则他们将能够在会话中使用语音和文本输入。

- 如果用户选择**纯文本**语言，则他们将只能使用文本输入并在会话中发送即时消息。 纯文本语言是文本翻译支持的语言，但不是语音到文本语言。 可在[语言支持](supported-languages.md)页上查看可用语言。

除了其主要语言以外，每个参与者还可以指定用于翻译会话的其他语言。

下面总结了用户在多设备对话中的功能，它基于其选择的主要语言。


| 用户可以在对话中执行的操作 | 语音转文本 | 纯文本 |
|-----------------------------------|----------------|------|
| 使用语音输入 | ✔️ | ❌ |
| 发送即时消息 | ✔️ | ✔️ |
| 转换会话 | ✔️ | ✔️ |

> [!NOTE]
> 有关可用的语音到文本和文本翻译语言的列表，请参阅[支持的语言](supported-languages.md)。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实时转换会话](quickstarts/multi-device-conversation.md)
