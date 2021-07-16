---
ms.openlocfilehash: f8b2a2b4e1c5d42c74398616513204559058a5dc
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446790"
---
<!-- markdownlint-disable MD002 MD041 -->

在此练习中，你将使用 Bot Framework 的 **OAuthPrompt** 在机器人中实现身份验证，并获取用于调用 Microsoft Graph API 的访问令牌。

1. 打开 **"appsettings.js"，** 然后进行以下更改。

    - 将 的值 `MicrosoftAppId` 更改为你的日历自动程序Graph **的应用程序** ID。
    - 将 的值更改为 `MicrosoftAppPassword` 您的日历 **Graph自动程序** 客户端密码。
    - 添加一个名为 `ConnectionName` 的值，值为 `GraphBotAuth` 。

    :::code language="json" source="../demo/GraphCalendarBot/appsettings.example.json":::

    > [!NOTE]
    > 如果你使用的值不用于 Azure 门户中 `GraphBotAuth` **OAuth Connection** 设置的名称，请对条目使用 `ConnectionName` 该值。

## <a name="implement-dialogs"></a>实现对话框

1. 在名为 Dialogs 的项目的根目录中 **新建一个目录**。 在 **./Dialogs** 目录中创建一个名为 **LogoutDialog.cs** 的新文件并添加以下代码。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/LogoutDialog.cs" id="LogoutDialogSnippet":::

    此对话框为自动程序中派生的所有其他对话框提供基类。 这允许用户注销，而不管他们在机器人对话框中的什么位置。

1. 在名为 **MainDialog.cs** 的 **./Dialogs** 目录中创建新文件并添加以下代码。

    ```csharp
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Dialogs.Choices;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;

    namespace CalendarBot.Dialogs
    {
        public class MainDialog : LogoutDialog
        {
            const string NO_PROMPT = "no-prompt";
            protected readonly ILogger _logger;

            public MainDialog(IConfiguration configuration, ILogger<MainDialog> logger)
                : base(nameof(MainDialog), configuration["ConnectionName"])
            {
                _logger = logger;

                // OAuthPrompt dialog handles the authentication and token
                // acquisition
                AddDialog(new OAuthPrompt(
                    nameof(OAuthPrompt),
                    new OAuthPromptSettings
                    {
                        ConnectionName = ConnectionName,
                        Text = "Please login",
                        Title = "Login",
                        Timeout = 300000, // User has 5 minutes to login
                    }));

                AddDialog(new ChoicePrompt(nameof(ChoicePrompt)));

                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    LoginPromptStepAsync,
                    ProcessLoginStepAsync,
                    PromptUserStepAsync,
                    CommandStepAsync,
                    ProcessStepAsync,
                    ReturnToPromptStepAsync
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> LoginPromptStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // If we're going through the waterfall a second time, don't do an extra OAuthPrompt
                var options = stepContext.Options?.ToString();
                if (options == NO_PROMPT)
                {
                    return await stepContext.NextAsync(cancellationToken: cancellationToken);
                }

                return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
            }

            private async Task<DialogTurnResult> ProcessLoginStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // If we're going through the waterfall a second time, don't do an extra OAuthPrompt
                var options = stepContext.Options?.ToString();
                if (options == NO_PROMPT)
                {
                    return await stepContext.NextAsync(cancellationToken: cancellationToken);
                }

                // Get the token from the previous step. If it's there, login was successful
                if (stepContext.Result != null)
                {
                    var tokenResponse = stepContext.Result as TokenResponse;
                    if (!string.IsNullOrEmpty(tokenResponse?.Token))
                    {
                        await stepContext.Context.SendActivityAsync(
                            MessageFactory.Text("You are now logged in."), cancellationToken);
                        return await stepContext.NextAsync(null, cancellationToken);
                    }
                }

                await stepContext.Context.SendActivityAsync(
                    MessageFactory.Text("Login was not successful please try again."), cancellationToken);
                return await stepContext.EndDialogAsync();
            }

            private async Task<DialogTurnResult> PromptUserStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                var options = new PromptOptions
                {
                    Prompt = MessageFactory.Text("Please choose an option below"),
                    Choices = new List<Choice> {
                        new Choice { Value = "Show token" },
                        new Choice { Value = "Show me" },
                        new Choice { Value = "Show calendar" },
                        new Choice { Value = "Add event" },
                        new Choice { Value = "Log out" },
                    }
                };

                return await stepContext.PromptAsync(
                    nameof(ChoicePrompt),
                    options,
                    cancellationToken);
            }

            private async Task<DialogTurnResult> CommandStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // Save the command the user entered so we can get it back after
                // the OAuthPrompt completes
                var foundChoice = stepContext.Result as FoundChoice;
                // Result could be a FoundChoice (if user selected a choice button)
                // or a string (if user just typed something)
                stepContext.Values["command"] = foundChoice?.Value ?? stepContext.Result;

                // There is no reason to store the token locally in the bot because we can always just call
                // the OAuth prompt to get the token or get a new token if needed. The prompt completes silently
                // if the user is already signed in.
                return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
            }

            private async Task<DialogTurnResult> ProcessStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                if (stepContext.Result != null)
                {
                    var tokenResponse = stepContext.Result as TokenResponse;

                    // If we have the token use the user is authenticated so we may use it to make API calls.
                    if (tokenResponse?.Token != null)
                    {
                        var command = ((string)stepContext.Values["command"] ?? string.Empty).ToLowerInvariant();

                        if (command.StartsWith("show token"))
                        {
                            // Show the user's token - for testing and troubleshooting
                            // Generally production apps should not display access tokens
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text($"Your token is: {tokenResponse.Token}"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("show me"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("show calendar"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else if (command.StartsWith("add event"))
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I don't know how to do this yet!"),
                                cancellationToken);
                        }
                        else
                        {
                            await stepContext.Context.SendActivityAsync(
                                MessageFactory.Text("I'm sorry, I didn't understand. Please try again."),
                                cancellationToken);
                        }
                    }
                }
                else
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("We couldn't log you in. Please try again later."),
                        cancellationToken);
                    return await stepContext.EndDialogAsync(cancellationToken: cancellationToken);
                }

                // Go to the next step
                return await stepContext.NextAsync(cancellationToken: cancellationToken);
            }

            private async Task<DialogTurnResult> ReturnToPromptStepAsync(
                WaterfallStepContext stepContext,
                CancellationToken cancellationToken)
            {
                // Restart the dialog, but skip the initial login prompt
                return await stepContext.ReplaceDialogAsync(InitialDialogId, NO_PROMPT, cancellationToken);
            }
        }
    }
    ```

    花些时间查看此代码。

    - 在构造函数中，它使用一组按顺序排列的步骤设置 [一个"瀑布](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) 图"。
        - In `LoginPromptStepAsync` it sends an **OAuthPrompt**. 如果用户未登录，这会向用户发送 UI 提示。
        - In `ProcessLoginStepAsync` it checks if the login was successful and sends a confirmation.
        - In `PromptUserStepAsync` it sends a **ChoicePrompt** with the available commands.
        - In `CommandStepAsync` it saves the user's choice， then resends an **OAuthPrompt**.
        - In `ProcessStepAsync` it takes action based on the command received.
        - In `ReturnToPromptStepAsync` it starts the waterfall over， but passes a flag to skip the initial user login.

## <a name="update-calendarbot"></a>更新 CalendarBot

下一步是更新 **CalendarBot** 以使用这些新对话框。

1. 打开 **./Bots/CalendarBot.cs，** 并将其全部内容替换为以下代码。

    :::code language="csharp" source="../demo/GraphCalendarBot/Bots/CalendarBot.cs" id="CalendarBotSnippet":::

    以下是更改的简短摘要。

    - 将 **CalendarBot** 类更改为模板类，以接收 **Dialog**。
    - 更改 **了 CalendarBot** 类以扩展 **TeamsActivityHandler，** 允许其登录 Microsoft Teams。
    - 添加了其他方法替代以启用身份验证。

## <a name="update-startupcs"></a>更新 Startup.cs

最后一步是更新 方法， `ConfigureServices` 以添加身份验证所需的服务和新对话框。

1. 打开 **./Startup.cs，** 然后从 `services.AddTransient<IBot, Bots.CalendarBot>();` 方法中删除 `ConfigureServices` 行。

1. 在 方法的末尾插入以下 `ConfigureServices` 代码。

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="ConfigureServiceSnippet":::

## <a name="test-authentication"></a>测试身份验证

1. 保存所有更改，然后使用 启动自动程序 `dotnet run` 。

1. 打开Bot Framework Emulator。 选择左&#9881;齿轮图标。

1. 输入 ngrok 安装的本地路径，并启用本地地址旁路 **ngrok** 和运行 **ngrok** Emulator启动选项。 选择“**保存**”。

1. 选择"**文件"** 菜单，然后选择"**新建自动程序配置..."。**

1. 填写字段，如下所示。

    - **自动程序名称：**`CalendarBot`
    - **终结点 URL：**`https://localhost:3978/api/messages`
    - **Microsoft 应用 ID：** 日历自动程序Graph **的应用程序** ID
    - **Microsoft 应用密码：Graph****日历自动程序** 客户端密码
    - **对存储在自动程序配置中的密钥进行加密：** 已启用

    !["新建自动程序配置"对话框的屏幕截图](images/new-bot-config.png)

1. 选择 **"保存并连接"。** 在仿真器连接后，你应该会看到 `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`

1. 键入一些文本并将其发送到机器人。 机器人使用登录提示进行响应。

1. 选择" **登录"** 按钮。 仿真器会提示你确认以 开头的 `oauthlink://https://token.botframeworkcom` URL。 选择 **"确认** "继续。

1. 在弹出窗口中，使用你的 Microsoft 365 帐户登录。 查看请求的权限并接受。

1. 身份验证和同意完成后，弹出窗口将提供验证代码。 复制代码并关闭窗口。

    ![验证代码Bot Framework Emulator屏幕截图](images/validation-code.png)

1. 在聊天窗口中输入验证代码以完成登录。

    ![与示例自动程序进行登录对话的屏幕截图](images/bot-login.png)

1. 如果选择 **"显示令牌** "按钮 (或键入) ，则自动 `show token` 程序将显示访问令牌。 " **注销"** 按钮 (键入) `log out` 将注销。

> [!TIP]
> 与机器人开始对话时，Bot Framework Emulator消息中出现以下错误消息。
>
> ```text
> Failed to generate an actual sign-in link: Error: Failed to connect to ngrok instance for OAuth postback URL:
> FetchError: request to http://127.0.0.1:4041/api/tunnels failed, reason: connect ECONNREFUSED 127.0.0.1:4041
> ```
>
> 如果发生这种情况，请确保在仿真器设置中启用"Emulator启动时运行 **ngrok"** 选项，然后重新启动仿真器。
