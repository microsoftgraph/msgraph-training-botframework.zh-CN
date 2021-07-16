---
ms.openlocfilehash: c60d1167e52118f06127a858e91aef6bddeb1d14
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446818"
---
# <a name="graphcalendarbot"></a><span data-ttu-id="5de71-101">GraphCalendarBot</span><span class="sxs-lookup"><span data-stu-id="5de71-101">GraphCalendarBot</span></span>

<span data-ttu-id="5de71-102">Bot Framework v4 回显自动程序示例。</span><span class="sxs-lookup"><span data-stu-id="5de71-102">Bot Framework v4 echo bot sample.</span></span>

<span data-ttu-id="5de71-103">此机器人是使用 [Bot Framework](https://dev.botframework.com)创建的，它显示了如何创建一个简单的机器人，以接受用户输入并返回回显。</span><span class="sxs-lookup"><span data-stu-id="5de71-103">This bot has been created using [Bot Framework](https://dev.botframework.com), it shows how to create a simple bot that accepts input from the user and echoes it back.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5de71-104">先决条件</span><span class="sxs-lookup"><span data-stu-id="5de71-104">Prerequisites</span></span>

- <span data-ttu-id="5de71-105">[.NET Core SDK](https://dotnet.microsoft.com/download) 版本 3.1</span><span class="sxs-lookup"><span data-stu-id="5de71-105">[.NET Core SDK](https://dotnet.microsoft.com/download) version 3.1</span></span>

  ```bash
  # determine dotnet version
  dotnet --version
  ```

## <a name="to-try-this-sample"></a><span data-ttu-id="5de71-106">试用此示例</span><span class="sxs-lookup"><span data-stu-id="5de71-106">To try this sample</span></span>

- <span data-ttu-id="5de71-107">在终端中，导航到 `GraphCalendarBot`</span><span class="sxs-lookup"><span data-stu-id="5de71-107">In a terminal, navigate to `GraphCalendarBot`</span></span>

    ```bash
    # change into project folder
    cd GraphCalendarBot
    ```

- <span data-ttu-id="5de71-108">从终端运行自动程序，或从Visual Studio选择选项 A 或 B。</span><span class="sxs-lookup"><span data-stu-id="5de71-108">Run the bot from a terminal or from Visual Studio, choose option A or B.</span></span>

  <span data-ttu-id="5de71-109">来自) 的 A</span><span class="sxs-lookup"><span data-stu-id="5de71-109">A) From a terminal</span></span>

  ```bash
  # run the bot
  dotnet run
  ```

  <span data-ttu-id="5de71-110">B) 或 from Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5de71-110">B) Or from Visual Studio</span></span>

  - <span data-ttu-id="5de71-111">启动Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5de71-111">Launch Visual Studio</span></span>
  - <span data-ttu-id="5de71-112">文件 -> Open -> Project/Solution</span><span class="sxs-lookup"><span data-stu-id="5de71-112">File -> Open -> Project/Solution</span></span>
  - <span data-ttu-id="5de71-113">导航到 `GraphCalendarBot` 文件夹</span><span class="sxs-lookup"><span data-stu-id="5de71-113">Navigate to `GraphCalendarBot` folder</span></span>
  - <span data-ttu-id="5de71-114">选择 `GraphCalendarBot.csproj` 文件</span><span class="sxs-lookup"><span data-stu-id="5de71-114">Select `GraphCalendarBot.csproj` file</span></span>
  - <span data-ttu-id="5de71-115">按 `F5` 以运行项目</span><span class="sxs-lookup"><span data-stu-id="5de71-115">Press `F5` to run the project</span></span>

## <a name="testing-the-bot-using-bot-framework-emulator"></a><span data-ttu-id="5de71-116">使用自动程序测试Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="5de71-116">Testing the bot using Bot Framework Emulator</span></span>

<span data-ttu-id="5de71-117">[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator)是一个桌面应用程序，可让机器人开发人员在 localhost 上或通过隧道远程运行测试和调试其机器人。</span><span class="sxs-lookup"><span data-stu-id="5de71-117">[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator) is a desktop application that allows bot developers to test and debug their bots on localhost or running remotely through a tunnel.</span></span>

- <span data-ttu-id="5de71-118">从此处Bot Framework Emulator 4.9.0 版或[更大版本](https://github.com/Microsoft/BotFramework-Emulator/releases)</span><span class="sxs-lookup"><span data-stu-id="5de71-118">Install the Bot Framework Emulator version 4.9.0 or greater from [here](https://github.com/Microsoft/BotFramework-Emulator/releases)</span></span>

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a><span data-ttu-id="5de71-119">连接自动程序Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="5de71-119">Connect to the bot using Bot Framework Emulator</span></span>

- <span data-ttu-id="5de71-120">启动Bot Framework Emulator</span><span class="sxs-lookup"><span data-stu-id="5de71-120">Launch Bot Framework Emulator</span></span>
- <span data-ttu-id="5de71-121">文件 ->打开自动程序</span><span class="sxs-lookup"><span data-stu-id="5de71-121">File -> Open Bot</span></span>
- <span data-ttu-id="5de71-122">输入的自动程序 URL `http://localhost:3978/api/messages`</span><span class="sxs-lookup"><span data-stu-id="5de71-122">Enter a Bot URL of `http://localhost:3978/api/messages`</span></span>

## <a name="deploy-the-bot-to-azure"></a><span data-ttu-id="5de71-123">将机器人部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="5de71-123">Deploy the bot to Azure</span></span>

<span data-ttu-id="5de71-124">若要了解有关将机器人部署到 Azure 中的信息，请参阅将机器人部署到 [Azure，](https://aka.ms/azuredeployment) 获取部署说明的完整列表。</span><span class="sxs-lookup"><span data-stu-id="5de71-124">To learn more about deploying a bot to Azure, see [Deploy your bot to Azure](https://aka.ms/azuredeployment) for a complete list of deployment instructions.</span></span>

## <a name="further-reading"></a><span data-ttu-id="5de71-125">进一步阅读</span><span class="sxs-lookup"><span data-stu-id="5de71-125">Further reading</span></span>

- [<span data-ttu-id="5de71-126">Bot Framework 文档</span><span class="sxs-lookup"><span data-stu-id="5de71-126">Bot Framework Documentation</span></span>](https://docs.botframework.com)
- [<span data-ttu-id="5de71-127">Bot 基础知识</span><span class="sxs-lookup"><span data-stu-id="5de71-127">Bot Basics</span></span>](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [<span data-ttu-id="5de71-128">活动处理</span><span class="sxs-lookup"><span data-stu-id="5de71-128">Activity processing</span></span>](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [<span data-ttu-id="5de71-129">Azure Bot 服务简介</span><span class="sxs-lookup"><span data-stu-id="5de71-129">Azure Bot Service Introduction</span></span>](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [<span data-ttu-id="5de71-130">Azure Bot 服务文档</span><span class="sxs-lookup"><span data-stu-id="5de71-130">Azure Bot Service Documentation</span></span>](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [<span data-ttu-id="5de71-131">.NET Core CLI 工具</span><span class="sxs-lookup"><span data-stu-id="5de71-131">.NET Core CLI tools</span></span>](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)
- [<span data-ttu-id="5de71-132">Azure CLI</span><span class="sxs-lookup"><span data-stu-id="5de71-132">Azure CLI</span></span>](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [<span data-ttu-id="5de71-133">Azure 门户</span><span class="sxs-lookup"><span data-stu-id="5de71-133">Azure Portal</span></span>](https://portal.azure.com)
- [<span data-ttu-id="5de71-134">语言 了解使用</span><span class="sxs-lookup"><span data-stu-id="5de71-134">Language Understanding using LUIS</span></span>](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)
- [<span data-ttu-id="5de71-135">频道和自动程序连接器服务</span><span class="sxs-lookup"><span data-stu-id="5de71-135">Channels and Bot Connector Service</span></span>](https://docs.microsoft.com/en-us/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)

<span data-ttu-id="5de71-136">使用 `dotnet new echobot` v4.13.2 生成</span><span class="sxs-lookup"><span data-stu-id="5de71-136">Generated using `dotnet new echobot` v4.13.2</span></span>
