---
ms.openlocfilehash: 959ea0ceffc601baa5d87a5cd303f7d02919826f
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446811"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a3032-101">本教程指导你如何生成使用 Microsoft Graph API 检索用户的日历信息的 Bot Framework 自动程序。</span><span class="sxs-lookup"><span data-stu-id="a3032-101">This tutorial teaches you how to build a Bot Framework bot that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="a3032-102">如果只想下载已完成的教程，可以下载或克隆GitHub[存储库](https://github.com/microsoftgraph/msgraph-training-botframework)。</span><span class="sxs-lookup"><span data-stu-id="a3032-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).</span></span> <span data-ttu-id="a3032-103">有关使用应用 ID 和密码配置应用的说明，请参阅演示文件夹中的自述文件。</span><span class="sxs-lookup"><span data-stu-id="a3032-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a3032-104">先决条件</span><span class="sxs-lookup"><span data-stu-id="a3032-104">Prerequisites</span></span>

<span data-ttu-id="a3032-105">在开始本教程之前，应在开发计算机上安装以下内容。</span><span class="sxs-lookup"><span data-stu-id="a3032-105">Before you start this tutorial, you should have the following installed on your development machine.</span></span>

- [<span data-ttu-id="a3032-106">.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="a3032-106">.NET Core SDK</span></span>](https://dotnet.microsoft.com/download)
- [<span data-ttu-id="a3032-107">Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="a3032-107">Bot Framework Emulator</span></span>](https://github.com/microsoft/BotFramework-Emulator/blob/master/README.md)
- [<span data-ttu-id="a3032-108">ngrok</span><span class="sxs-lookup"><span data-stu-id="a3032-108">ngrok</span></span>](https://ngrok.com/)

<span data-ttu-id="a3032-109">您还应该有一个在 Outlook.com 上拥有邮箱的个人 Microsoft 帐户，或者一个 Microsoft 工作或学校帐户。</span><span class="sxs-lookup"><span data-stu-id="a3032-109">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="a3032-110">如果你没有 Microsoft 帐户，则有几个选项可以获取免费帐户：</span><span class="sxs-lookup"><span data-stu-id="a3032-110">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="a3032-111">你可以 [注册新的个人 Microsoft 帐户](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。</span><span class="sxs-lookup"><span data-stu-id="a3032-111">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="a3032-112">你可以[注册开发人员计划Office 365](https://developer.microsoft.com/office/dev-program)免费订阅Office 365订阅。</span><span class="sxs-lookup"><span data-stu-id="a3032-112">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>
- <span data-ttu-id="a3032-113">Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="a3032-113">An Azure subscription.</span></span> <span data-ttu-id="a3032-114">如果没有，请创建一个 [免费](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 帐户，然后再开始。</span><span class="sxs-lookup"><span data-stu-id="a3032-114">If you do not have one, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

> [!NOTE]
> <span data-ttu-id="a3032-115">本教程是使用以下版本编写的。</span><span class="sxs-lookup"><span data-stu-id="a3032-115">This tutorial was written with the following versions.</span></span> <span data-ttu-id="a3032-116">本指南中的步骤可能与其他版本一起运行，但尚未经过测试。</span><span class="sxs-lookup"><span data-stu-id="a3032-116">The steps in this guide may work with other versions, but that has not been tested.</span></span>
>
> - <span data-ttu-id="a3032-117">.NET Core SDK 版本 5.0.302</span><span class="sxs-lookup"><span data-stu-id="a3032-117">.NET Core SDK version 5.0.302</span></span>
> - <span data-ttu-id="a3032-118">Bot Framework Emulator 4.1.3</span><span class="sxs-lookup"><span data-stu-id="a3032-118">Bot Framework Emulator 4.1.3</span></span>
> - <span data-ttu-id="a3032-119">ngrok 2.3.40</span><span class="sxs-lookup"><span data-stu-id="a3032-119">ngrok 2.3.40</span></span>

## <a name="feedback"></a><span data-ttu-id="a3032-120">反馈</span><span class="sxs-lookup"><span data-stu-id="a3032-120">Feedback</span></span>

<span data-ttu-id="a3032-121">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).</span><span class="sxs-lookup"><span data-stu-id="a3032-121">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-botframework).</span></span>
