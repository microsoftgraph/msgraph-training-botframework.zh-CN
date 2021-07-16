---
ms.openlocfilehash: 51878a3eebbcacb9ea325a3f97453d30006be116
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446776"
---
<!-- markdownlint-disable MD002 MD041 -->

在此部分中，你将创建 Bot Framework 项目。

1. 在要创建项目的 (CLI) 打开命令行接口。 运行以下命令以使用 **Microsoft.Bot.Framework.CSharp.EchoBot** 模板创建新项目。

    ```dotnetcli
    dotnet new echobot -n GraphCalendarBot
    ```

    > [!NOTE]
    > 如果收到错误 `No templates matched the input template name: echobot.` ，请通过以下命令安装模板，然后重新运行上一个命令。
    >
    > ```dotnetcli
    > dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
    > ```

1. 将默认的 **EchoBot** 类重命名为 **CalendarBot**。 打开 **./Bots/EchoBot.cs，** 将 的所有实例 `EchoBot` 替换为 `CalendarBot` 。 将文件重命名为 **CalendarBot.cs**。

1. 将 替换为其余 `EchoBot` `CalendarBot` **.cs 文件的所有实例** 。

1. 在 CLI 中，将当前目录更改为 **GraphCalendarBot** 目录并运行以下命令以确认项目生成。

    ```dotnetcli
    dotnet build
    ```

## <a name="add-nuget-packages"></a>添加 Nuget 程序包

在继续之前，请安装一些NuGet包，你稍后会使用它。

- [AdaptiveCards，](https://www.nuget.org/packages/AdaptiveCards/) 允许机器人在响应中发送自适应卡片。
- [Microsoft.Bot.Builder.Dialogs，](https://www.nuget.org/packages/Microsoft.Bot.Builder.Dialogs/) 用于向自动程序添加对话框支持。
- [Microsoft.Recognizers.Text.DataTypes.TimexExpression](https://www.nuget.org/packages/Microsoft.Recognizers.Text.DataTypes.TimexExpression/) 将自动程序提示返回的 TIMEX 表达式转换为 **DateTime** 对象。
- [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) 用来呼叫 Microsoft Graph。

1. 在 CLI 中运行以下命令以安装依赖项。

    ```Shell
    dotnet add package AdaptiveCards --version 2.7.1
    dotnet add package Microsoft.Bot.Builder.Dialogs --version 4.14.1
    dotnet add package Microsoft.Bot.Builder.Integration.AspNet.Core --version 4.14.1
    dotnet add package Microsoft.Recognizers.Text.DataTypes.TimexExpression --version 1.7.0
    dotnet add package Microsoft.Graph --version 4.0.0
    ```

## <a name="test-the-bot"></a>测试机器人

在添加任何代码之前，请测试自动程序以确保其正常工作，并确保Bot Framework Emulator配置为测试它。

1. 通过运行以下命令启动自动程序。

    ```dotnetcli
    dotnet run
    ```

    > [!TIP]
    > 虽然可以使用任何文本编辑器来编辑项目中的源文件，但我们建议使用[Visual Studio Code](https://code.visualstudio.com/)。 Visual Studio Code调试支持、Intellisense 等。 如果使用 Visual Studio Code，可以使用"运行开始调试"菜单启动  ->  自动程序。

1. 打开浏览器并进入 ，确认机器人正在运行 `http://localhost:3978` 。 你应该会看到你的 **机器人已准备就绪！** 消息。

1. 打开Bot Framework Emulator。 选择"**文件"** 菜单，然后选择"**打开自动程序"。**

1. 在 `http://localhost:3978/api/messages` 自动程序 **URL 中输入**，然后选择"连接"。 

1. 聊天机器人在聊天 `Hello and welcome!` 窗口中进行响应。 向自动程序发送消息并确认它会回显。

    ![连接到自动Bot Framework Emulator的屏幕截图](images/test-emulator.png)
