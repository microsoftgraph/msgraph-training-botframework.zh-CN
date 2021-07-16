---
ms.openlocfilehash: 51878a3eebbcacb9ea325a3f97453d30006be116
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446776"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="52de8-101">在此部分中，你将创建 Bot Framework 项目。</span><span class="sxs-lookup"><span data-stu-id="52de8-101">In this section you'll create a Bot Framework project.</span></span>

1. <span data-ttu-id="52de8-102">在要创建项目的 (CLI) 打开命令行接口。</span><span class="sxs-lookup"><span data-stu-id="52de8-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="52de8-103">运行以下命令以使用 **Microsoft.Bot.Framework.CSharp.EchoBot** 模板创建新项目。</span><span class="sxs-lookup"><span data-stu-id="52de8-103">Run the following command to create new project using the **Microsoft.Bot.Framework.CSharp.EchoBot** template.</span></span>

    ```dotnetcli
    dotnet new echobot -n GraphCalendarBot
    ```

    > [!NOTE]
    > <span data-ttu-id="52de8-104">如果收到错误 `No templates matched the input template name: echobot.` ，请通过以下命令安装模板，然后重新运行上一个命令。</span><span class="sxs-lookup"><span data-stu-id="52de8-104">If you receive an `No templates matched the input template name: echobot.` error, install the template with the following command and re-run the previous command.</span></span>
    >
    > ```dotnetcli
    > dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
    > ```

1. <span data-ttu-id="52de8-105">将默认的 **EchoBot** 类重命名为 **CalendarBot**。</span><span class="sxs-lookup"><span data-stu-id="52de8-105">Rename the default **EchoBot** class to **CalendarBot**.</span></span> <span data-ttu-id="52de8-106">打开 **./Bots/EchoBot.cs，** 将 的所有实例 `EchoBot` 替换为 `CalendarBot` 。</span><span class="sxs-lookup"><span data-stu-id="52de8-106">Open **./Bots/EchoBot.cs** and replace all instances of `EchoBot` with `CalendarBot`.</span></span> <span data-ttu-id="52de8-107">将文件重命名为 **CalendarBot.cs**。</span><span class="sxs-lookup"><span data-stu-id="52de8-107">Rename the file to **CalendarBot.cs**.</span></span>

1. <span data-ttu-id="52de8-108">将 替换为其余 `EchoBot` `CalendarBot` **.cs 文件的所有实例** 。</span><span class="sxs-lookup"><span data-stu-id="52de8-108">Replace all instances of `EchoBot` with `CalendarBot` in the remaining **.cs** files.</span></span>

1. <span data-ttu-id="52de8-109">在 CLI 中，将当前目录更改为 **GraphCalendarBot** 目录并运行以下命令以确认项目生成。</span><span class="sxs-lookup"><span data-stu-id="52de8-109">In your CLI, change the current directory to the **GraphCalendarBot** directory and run the following command to confirm the project builds.</span></span>

    ```dotnetcli
    dotnet build
    ```

## <a name="add-nuget-packages"></a><span data-ttu-id="52de8-110">添加 Nuget 程序包</span><span class="sxs-lookup"><span data-stu-id="52de8-110">Add NuGet packages</span></span>

<span data-ttu-id="52de8-111">在继续之前，请安装一些NuGet包，你稍后会使用它。</span><span class="sxs-lookup"><span data-stu-id="52de8-111">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="52de8-112">[AdaptiveCards，](https://www.nuget.org/packages/AdaptiveCards/) 允许机器人在响应中发送自适应卡片。</span><span class="sxs-lookup"><span data-stu-id="52de8-112">[AdaptiveCards](https://www.nuget.org/packages/AdaptiveCards/) to allow the bot to send Adaptive Cards in responses.</span></span>
- <span data-ttu-id="52de8-113">[Microsoft.Bot.Builder.Dialogs，](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) 用于向自动程序添加对话框支持。</span><span class="sxs-lookup"><span data-stu-id="52de8-113">[Microsoft.Bot.Builder.Dialogs](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) to add dialog support to the bot.</span></span>
- <span data-ttu-id="52de8-114">[Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) 将自动程序提示返回的 TIMEX 表达式转换为 **DateTime** 对象。</span><span class="sxs-lookup"><span data-stu-id="52de8-114">[Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) to convert the TIMEX expressions returned from bot prompts into **DateTime** objects.</span></span>
- <span data-ttu-id="52de8-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) 用来呼叫 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="52de8-115">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>

1. <span data-ttu-id="52de8-116">在 CLI 中运行以下命令以安装依赖项。</span><span class="sxs-lookup"><span data-stu-id="52de8-116">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package AdaptiveCards --version 2.7.1
    dotnet add package Microsoft.Bot.Builder.Dialogs --version 4.14.1
    dotnet add package Microsoft.Bot.Builder.Integration.AspNet.Core --version 4.14.1
    dotnet add package Microsoft.Recognizers.Text.DataTypes.TimexExpression --version 1.7.0
    dotnet add package Microsoft.Graph --version 4.0.0
    ```

## <a name="test-the-bot"></a><span data-ttu-id="52de8-117">测试机器人</span><span class="sxs-lookup"><span data-stu-id="52de8-117">Test the bot</span></span>

<span data-ttu-id="52de8-118">在添加任何代码之前，请测试自动程序以确保其正常工作，并确保Bot Framework Emulator配置为测试它。</span><span class="sxs-lookup"><span data-stu-id="52de8-118">Before adding any code, test the bot to make sure that it works correctly, and that the Bot Framework Emulator is configured to test it.</span></span>

1. <span data-ttu-id="52de8-119">通过运行以下命令启动自动程序。</span><span class="sxs-lookup"><span data-stu-id="52de8-119">Start the bot by running the following command.</span></span>

    ```dotnetcli
    dotnet run
    ```

    > [!TIP]
    > <span data-ttu-id="52de8-120">虽然可以使用任何文本编辑器来编辑项目中的源文件，但我们建议使用[Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="52de8-120">While you can use any text editor to edit the source files in the project, we recommend using [Visual Studio Code](https://code.visualstudio.com/).</span></span> <span data-ttu-id="52de8-121">Visual Studio Code调试支持、Intellisense 等。</span><span class="sxs-lookup"><span data-stu-id="52de8-121">Visual Studio Code offers debugging support, Intellisense, and more.</span></span> <span data-ttu-id="52de8-122">如果使用 Visual Studio Code，可以使用"运行开始调试"菜单启动  ->  自动程序。</span><span class="sxs-lookup"><span data-stu-id="52de8-122">If using Visual Studio Code, you can start the bot using the **Run** -> **Start Debugging** menu.</span></span>

1. <span data-ttu-id="52de8-123">打开浏览器并进入 ，确认机器人正在运行 `http://localhost:3978` 。</span><span class="sxs-lookup"><span data-stu-id="52de8-123">Confirm the bot is running by opening your browser and going to `http://localhost:3978`.</span></span> <span data-ttu-id="52de8-124">你应该会看到你的 **机器人已准备就绪！**</span><span class="sxs-lookup"><span data-stu-id="52de8-124">You should see a **Your bot is ready!**</span></span> <span data-ttu-id="52de8-125">消息。</span><span class="sxs-lookup"><span data-stu-id="52de8-125">message.</span></span>

1. <span data-ttu-id="52de8-126">打开Bot Framework Emulator。</span><span class="sxs-lookup"><span data-stu-id="52de8-126">Open the Bot Framework Emulator.</span></span> <span data-ttu-id="52de8-127">选择"**文件"** 菜单，然后选择"**打开自动程序"。**</span><span class="sxs-lookup"><span data-stu-id="52de8-127">Choose the **File** menu, then **Open Bot**.</span></span>

1. <span data-ttu-id="52de8-128">在 `http://localhost:3978/api/messages` 自动程序 **URL 中输入**，然后选择"连接"。 </span><span class="sxs-lookup"><span data-stu-id="52de8-128">Enter `http://localhost:3978/api/messages` in the **Bot URL**, then select **Connect**.</span></span>

1. <span data-ttu-id="52de8-129">聊天机器人在聊天 `Hello and welcome!` 窗口中进行响应。</span><span class="sxs-lookup"><span data-stu-id="52de8-129">The bot responds with `Hello and welcome!` in the chat window.</span></span> <span data-ttu-id="52de8-130">向自动程序发送消息并确认它会回显。</span><span class="sxs-lookup"><span data-stu-id="52de8-130">Send a message to the bot and confirm it echoes it back.</span></span>

    ![连接到自动Bot Framework Emulator的屏幕截图](images/test-emulator.png)
