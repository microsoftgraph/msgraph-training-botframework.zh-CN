---
ms.openlocfilehash: 959ea0ceffc601baa5d87a5cd303f7d02919826f
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446811"
---
<!-- markdownlint-disable MD002 MD041 -->

本教程指导你如何生成使用 Microsoft Graph API 检索用户的日历信息的 Bot Framework 自动程序。

> [!TIP]
> 如果只想下载已完成的教程，可以下载或克隆GitHub[存储库](https://github.com/microsoftgraph/msgraph-training-botframework)。 有关使用应用 ID 和密码配置应用的说明，请参阅演示文件夹中的自述文件。

## <a name="prerequisites"></a>先决条件

在开始本教程之前，应在开发计算机上安装以下内容。

- [.NET Core SDK](https://dotnet.microsoft.com/download)
- [Bot Framework Emulator](https://github.com/microsoft/BotFramework-Emulator/blob/master/README.md)
- [ngrok](https://ngrok.com/)

您还应该有一个在 Outlook.com 上拥有邮箱的个人 Microsoft 帐户，或者一个 Microsoft 工作或学校帐户。 如果你没有 Microsoft 帐户，则有几个选项可以获取免费帐户：

- 你可以 [注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- 你可以[注册开发人员计划Office 365](https://developer.microsoft.com/office/dev-program)免费订阅Office 365订阅。
- Azure 订阅。 如果没有，请创建一个 [免费](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 帐户，然后再开始。

> [!NOTE]
> 本教程是使用以下版本编写的。 本指南中的步骤可能与其他版本一起运行，但尚未经过测试。
>
> - .NET Core SDK 版本 5.0.302
> - Bot Framework Emulator 4.1.3
> - ngrok 2.3.40

## <a name="feedback"></a>反馈

Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).
