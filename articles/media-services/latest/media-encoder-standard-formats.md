---
title: 标准编码器格式和编解码器 - Azure
description: 本文包含可与 StandardEncoderPreset 配合使用的最常见的导入和导出文件格式列表。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79251357"
---
# <a name="standard-encoder-formats-and-codecs"></a>标准编码器格式和编解码器

本文包含可与 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 配合使用的最常见的导入和导出文件格式列表。 有关如何使用 **StandardEncoderPreset** 创建自定义预设的信息，请参阅[使用自定义预设创建转换](customize-encoder-presets-how-to.md)。

## <a name="input-containerfile-formats"></a>输入容器/文件格式

| 文件格式（文件扩展名） | 支持 |
| --- | --- |
| FLV（使用 H.264 和 AAC 编解码器）(.flv) |是 |
| MXF    (.mxf) |是 |
| GXF    (.gxf) |是 |
| MPEG2-PS、MPEG2-TS、3GP（.ts、.ps、.3gp、.3gpp、.mpg） |是 |
| Windows Media 视频 (WMV)/ASF（.wmv、.asf） |是 |
| AVI（8 位/10 位未压缩）(.avi) |是 |
| MP4（.mp4、.m4a、.m4v）/ISMV（.isma、.ismv） |是 |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |是 |
| Matroska/WebM (.mkv) |是 |
| WAVE/WAV (.wav) |是 |
| QuickTime (.mov) |是 |

### <a name="audio-formats-in-input-containers"></a>输入容器中的音频格式

标准编码器支持在输入容器中携带以下音频格式：

* MXF、GXF 和 QuickTime 文件，其中的音频曲目具有交错的立体声或 5.1 示例

或

* MXF、GXF 和 QuickTime 文件，其中的音频以独立 PCM 轨道的形式携带，但可以从文件元数据推导频道映射（到立体声或 5.1 的映射）

## <a name="input-video-codecs"></a>输入视频编解码器
| 输入视频编解码器 | 支持 |
| --- | --- |
| AVC 8 位/10 位，最高支持 4:2:2，包括 AVCIntra |8 位 4:2:0 和 4:2:2 |
| Avid DNxHD（MXF 格式） |是 |
| DVCPro/DVCProHD（MXF 格式） |是 |
| 数字视频 (DV)（AVI 文件格式） |是 |
| JPEG 2000 |是 |
| MPEG-2（最高支持 422 Profile 和 High Level；包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs® 和 D10 等变体） |最高支持 422 Profile |
| MPEG-1 |是 |
| VC-1/WMV9 |是 |
| Canopus HQ/HQX |否 |
| Mpeg-4 第 2 部分 |是 |
| [Theora](https://en.wikipedia.org/wiki/Theora) |是 |
| YUV420（未压缩或夹层） |是 |
| Apple ProRes 422 |是 |
| Apple ProRes 422 LT |是 |
| Apple ProRes 422 HQ |是 |
| Apple ProRes Proxy |是 |
| Apple ProRes 4444 |是 |
| Apple ProRes 4444 XQ |是 |
| HEVC/H.265| Main Profile|

## <a name="input-audio-codecs"></a>输入音频编解码器
| 输入音频编解码器 | 支持 |
| --- | --- |
| AAC(AAC-LC、AAC-HE 和 AAC-HEv2；最高支持 5.1） |是 |
| MPEG Layer 2 |是 |
| MP3 (MPEG-1 Audio Layer 3) |是 |
| Windows Media 音频 |是 |
| WAV/PCM |是 |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |是 |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |是 |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |是 |
| AMR（自适应多速率） |是 |
| AES（SMPTE 331M 和 302M、AES3-2003） |否 |
| Dolby® E |否 |
| Dolby® Digital (AC3) |否 |
| Dolby® Digital Plus (E-AC3) |否 |

## <a name="output-formats-and-codecs"></a>输出格式和编解码器
下表列出了导出操作支持的编解码器和文件格式。

| 文件格式 | 视频编解码器 | 音频编解码器 |
| --- | --- | --- |
| MP4 <br/><br/>（包括多码率 MP4 容器） |H.264（High、Main 和 Baseline Profile） |AAC-LC、HE-AAC v1、HE-AAC v2 |
| MPEG2-TS |H.264（High、Main 和 Baseline Profile） |AAC-LC、HE-AAC v1、HE-AAC v2 |

## <a name="next-steps"></a>后续步骤

[使用自定义预设创建转换](customize-encoder-presets-how-to.md)
