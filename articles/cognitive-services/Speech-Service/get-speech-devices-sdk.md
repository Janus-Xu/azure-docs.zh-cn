---
title: 获取语音设备 SDK
titleSuffix: Azure Cognitive Services
description: “语音服务”适用于多种设备和音频源。 现在，可以通过匹配的硬件和软件进一步利用语音应用程序。 本文介绍如何访问语音设备 SDK 并开始开发。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: ed3400689272d262ff5c8cda3cc2bd451f2ced4d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82184888"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>获取认知服务语音设备 SDK

语音设备 SDK 是一个预优化库，旨在与特制的开发工具包和各种麦克风阵列配置配合使用。

## <a name="choose-a-development-kit"></a>选择开发工具包

|设备|规格|说明|方案|
|--|--|--|--|
|[URbetter 开发工具包](http://www.urbetter.com/products_56/278.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 麦克风阵列、ARM SOC、WIFI、以太网、HDMI、USB 摄像头。 <br>Linux|行业级语音设备 SDK，适用于 Microsoft 麦克风阵列并支持扩展 I/O，如 HDMI/以太网和更多 USB 外围设备|对话听录、教育、医院、机器人、OTT Box、语音代理、汽车餐厅|
|[Roobo 智能音频开发工具包](https://ddk.roobo.com)<br>[安装](speech-devices-sdk-roobo-v1.md) / [快速入门](speech-devices-sdk-android-quickstart.md)![Roobo 智能音频开发工具包](media/speech-devices-sdk/device-roobo-v1.jpg)|7 麦克风阵列、ARM SOC、WIFI、音频输出、IO。 <br>[Android](speech-devices-sdk-android-quickstart.md)|首个适用于 Microsoft 麦克风阵列和前端处理 SDK 的语音设备 SDK，用于开发高质量的听录和语音方案|对话听录、智能扬声器、语音代理、可穿戴设备|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[设置](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [快速入门](speech-devices-sdk-windows-quickstart.md)![Azure Kinect 深色](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 麦克风阵列 RGB 和深度相机。 <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|具有高级人工智能 (AI) 传感器的开发人员工具包，用于构建复杂的计算机视觉和语音模型。 它将同类最佳的空间麦克风阵列和深度相机与视频相机和方向传感器组合在一起，这一切都在具有多种模式、选项和 Sdk 的一小设备中，可容纳各种计算类型。|对话听录, 机器人, 智能构建|
|Roobo 智能音频开发工具包 2<br>[安装](speech-devices-sdk-roobo-v2.md)<br>![Roobo 智能音频开发工具包 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 麦克风阵列、ARM SOC、WIFI、蓝牙、IO。 <br>Linux|第二代语音设备 SDK，在经济高效的参考设计中提供替代操作系统和更多功能。|对话听录、智能扬声器、语音代理、可穿戴设备|


## <a name="download-the-speech-devices-sdk"></a>下载语音设备 SDK

下载[语音设备 SDK](https://aka.ms/sdsdk-download)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [语音设备 SDK 入门](https://aka.ms/sdsdk-quickstart)
