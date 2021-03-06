---
title: 查询文本到语音容器终结点
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81274940"
---
容器提供[基于 REST 的终结点 api](../rest-text-to-speech.md)。 有许多适用于平台、框架和语言变体的[示例源代码项目](https://github.com/Azure-Samples/Cognitive-Speech-TTS)。

对于*标准的文本到语音转换*容器，你应依赖于所下载图像标记的区域设置和语音。 例如，如果下载了`latest`标记，则默认区域设置为`en-US`和`JessaRUS`语音。 然后`{VOICE_NAME}` ，参数将为[`en-US-JessaRUS`](../language-support.md#standard-voices)。 请参阅下面的示例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

但是，对于*自定义的文本到语音转换*，需要从[自定义语音门户](https://aka.ms/custom-voice-portal)获取**语音/模式**。 自定义模型名称与语音名称同义。 导航到**定型**页面，并复制要用作`{VOICE_NAME}`参数的**语音/模型**。
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="自定义语音模型-语音名称":::

请参阅下面的示例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

让我们构造一个 HTTP POST 请求，提供一些标头和数据负载。 将`{VOICE_NAME}`占位符替换为自己的值。

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

此命令：

* 为`speech/synthesize/cognitiveservices/v1`终结点构造 HTTP POST 请求。
* 指定`Accept`标头`audio/*`
* 指定的`Content-Type`标头`application/ssml+xml`，有关详细信息，请参阅[请求正文](../rest-text-to-speech.md#request-body)。
* 指定的`X-Microsoft-OutputFormat`标头`riff-16khz-16bit-mono-pcm`，有关更多选项，请参阅[音频输出](../rest-text-to-speech.md#audio-outputs)。
* 将给定的`{VOICE_NAME}` [语音合成标记语言（SSML）](../speech-synthesis-markup.md)请求发送到终结点。