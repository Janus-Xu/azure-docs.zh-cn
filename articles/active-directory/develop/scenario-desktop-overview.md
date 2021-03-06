---
title: 构建调用 Web API 的桌面应用 - Microsoft 标识平台 | Azure
description: 了解如何构建调用 Web API 的桌面应用（概述）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 09cc43dec2eff48754f5a6e693badd6bb1907cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882994"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>方案：用于调用 Web API 的 桌面应用

了解构建调用 Web API 的桌面应用所需的一切。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>入门

如果尚未创建你的第一个应用程序，请按照 .NET 桌面快速入门、通用 Windows 平台 (UWP) 快速入门或 macOS 本机应用快速入门进行创建：

> [!div class="nextstepaction"]
> [快速入门：获取令牌并从 Windows 桌面应用中调用 Microsoft Graph API](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [快速入门：获取令牌并从 UWP 应用中调用 Microsoft Graph API](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [快速入门：获取令牌并从 macOS 本机应用调用 Microsoft Graph API](./quickstart-v2-ios.md)

## <a name="overview"></a>概述

你编写桌面应用程序，需要将用户登录到应用程序并调用 Web API，例如 Microsoft Graph、其他 Microsoft API 或你自己的 Web API。 有几种可能性：

- 在以下情况下，可以使用交互式令牌获取：

  - 如果桌面应用程序支持图形控制，例如，它是 Windows.Form 应用程序、WPF 应用程序或 macOS 本机应用程序。
  - 或者，如果它是 .NET Core 应用程序，并且你同意在系统浏览器中与 Azure Active Directory (Azure AD) 进行身份验证交互。

- 对于 Windows 托管的应用程序，也可让加入到 Windows 域或已加入 Azure AD 的计算机上运行的应用程序使用集成 Windows 身份验证以无提示方式获取令牌。
- 最后，可以在公共客户端应用程序中使用用户名和密码，虽然不建议使用这种方式。 在某些情况下，它仍是必需的，如 DevOps。 使用此方式会对应用程序施加约束。 例如，它不能登录需要执行多重身份验证（条件访问）的用户。 另外，应用程序无法利用单一登录 (SSO)。

  它也违反新式身份验证的原则，提供它只是出于历史原因。

  ![桌面应用程序](media/scenarios/desktop-app.svg)

- 如果要编写可移植的命令行工具（可能是在 Linux 或 Mac 上运行的 .NET Core 应用程序），并且如果你接受将身份验证委托给系统浏览器，则可以使用交互式身份验证。 .NET Core 未提供 [Web 浏览器](https://aka.ms/msal-net-uses-web-browser)，因此将在系统浏览器中进行身份验证。 否则，在这种情况下，最佳选择是使用设备代码流。 此流也用于无浏览器的应用程序，例如 IoT 应用程序。

  ![无浏览器应用程序](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>详情

桌面应用程序有许多特异性。 这主要取决于你的应用程序是否使用交互式身份验证。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [桌面应用：应用注册](scenario-desktop-app-registration.md)
