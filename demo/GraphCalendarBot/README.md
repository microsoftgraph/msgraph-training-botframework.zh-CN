---
ms.openlocfilehash: c60d1167e52118f06127a858e91aef6bddeb1d14
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446818"
---
# <a name="graphcalendarbot"></a>GraphCalendarBot

Bot Framework v4 回显自动程序示例。

此机器人是使用 [Bot Framework](https://dev.botframework.com)创建的，它显示了如何创建一个简单的机器人，以接受用户输入并返回回显。

## <a name="prerequisites"></a>先决条件

- [.NET Core SDK](https://dotnet.microsoft.com/download) 版本 3.1

  ```bash
  # determine dotnet version
  dotnet --version
  ```

## <a name="to-try-this-sample"></a>试用此示例

- 在终端中，导航到 `GraphCalendarBot`

    ```bash
    # change into project folder
    cd GraphCalendarBot
    ```

- 从终端运行自动程序，或从Visual Studio选择选项 A 或 B。

  来自) 的 A

  ```bash
  # run the bot
  dotnet run
  ```

  B) 或 from Visual Studio

  - 启动Visual Studio
  - 文件 -> Open -> Project/Solution
  - 导航到 `GraphCalendarBot` 文件夹
  - 选择 `GraphCalendarBot.csproj` 文件
  - 按 `F5` 以运行项目

## <a name="testing-the-bot-using-bot-framework-emulator"></a>使用自动程序测试Bot Framework Emulator

[Bot Framework Emulator](https://github.com/microsoft/botframework-emulator)是一个桌面应用程序，可让机器人开发人员在 localhost 上或通过隧道远程运行测试和调试其机器人。

- 从此处Bot Framework Emulator 4.9.0 版或[更大版本](https://github.com/Microsoft/BotFramework-Emulator/releases)

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a>连接自动程序Bot Framework Emulator

- 启动Bot Framework Emulator
- 文件 ->打开自动程序
- 输入的自动程序 URL `http://localhost:3978/api/messages`

## <a name="deploy-the-bot-to-azure"></a>将机器人部署到 Azure

若要了解有关将机器人部署到 Azure 中的信息，请参阅将机器人部署到 [Azure，](https://aka.ms/azuredeployment) 获取部署说明的完整列表。

## <a name="further-reading"></a>进一步阅读

- [Bot Framework 文档](https://docs.botframework.com)
- [Bot 基础知识](https://docs.microsoft.com/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0)
- [活动处理](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-activity-processing?view=azure-bot-service-4.0)
- [Azure Bot 服务简介](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- [Azure Bot 服务文档](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)
- [.NET Core CLI 工具](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
- [Azure 门户](https://portal.azure.com)
- [语言 了解使用](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)
- [频道和自动程序连接器服务](https://docs.microsoft.com/en-us/azure/bot-service/bot-concepts?view=azure-bot-service-4.0)

使用 `dotnet new echobot` v4.13.2 生成
