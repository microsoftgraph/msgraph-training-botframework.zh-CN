---
ms.openlocfilehash: f8b2a2b4e1c5d42c74398616513204559058a5dc
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446790"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="90237-101">在此练习中，你将使用 Bot Framework 的 **OAuthPrompt** 在机器人中实现身份验证，并获取用于调用 Microsoft Graph API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="90237-101">In this exercise you will use the Bot Framework's **OAuthPrompt** to implement authentication in the bot, and acquire access tokens for calling the Microsoft Graph API.</span></span>

1. <span data-ttu-id="90237-102">打开 **"appsettings.js"，** 然后进行以下更改。</span><span class="sxs-lookup"><span data-stu-id="90237-102">Open **./appsettings.json** and make the following changes.</span></span>

    - <span data-ttu-id="90237-103">将 的值 `MicrosoftAppId` 更改为你的日历自动程序Graph **的应用程序** ID。</span><span class="sxs-lookup"><span data-stu-id="90237-103">Change the value of `MicrosoftAppId` to the application ID of your **Graph Calendar Bot** app registration.</span></span>
    - <span data-ttu-id="90237-104">将 的值更改为 `MicrosoftAppPassword` 您的日历 **Graph自动程序** 客户端密码。</span><span class="sxs-lookup"><span data-stu-id="90237-104">Change the value of `MicrosoftAppPassword` to your **Graph Calendar Bot** client secret.</span></span>
    - <span data-ttu-id="90237-105">添加一个名为 `ConnectionName` 的值，值为 `GraphBotAuth` 。</span><span class="sxs-lookup"><span data-stu-id="90237-105">Add a value named `ConnectionName` with a value of `GraphBotAuth`.</span></span>

    :::code language="json" source="../demo/GraphCalendarBot/appsettings.example.json":::

    > [!NOTE]
    > <span data-ttu-id="90237-106">如果你使用的值不用于 Azure 门户中 `GraphBotAuth` **OAuth Connection** 设置的名称，请对条目使用 `ConnectionName` 该值。</span><span class="sxs-lookup"><span data-stu-id="90237-106">If you used a value other than `GraphBotAuth` for the name of your entry in **OAuth Connection Settings** in the Azure Portal, use that value for the `ConnectionName` entry.</span></span>

## <a name="implement-dialogs"></a><span data-ttu-id="90237-107">实现对话框</span><span class="sxs-lookup"><span data-stu-id="90237-107">Implement dialogs</span></span>

1. <span data-ttu-id="90237-108">在名为 Dialogs 的项目的根目录中 **新建一个目录**。</span><span class="sxs-lookup"><span data-stu-id="90237-108">Create a new directory in the root of the project named **Dialogs**.</span></span> <span data-ttu-id="90237-109">在 **./Dialogs** 目录中创建一个名为 **LogoutDialog.cs** 的新文件并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="90237-109">Create a new file in the **./Dialogs** directory named **LogoutDialog.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/LogoutDialog.cs" id="LogoutDialogSnippet":::

    <span data-ttu-id="90237-110">此对话框为自动程序中派生的所有其他对话框提供基类。</span><span class="sxs-lookup"><span data-stu-id="90237-110">This dialog provides a base class for all of the other dialogs in the bot to derive from.</span></span> <span data-ttu-id="90237-111">这允许用户注销，而不管他们在机器人对话框中的什么位置。</span><span class="sxs-lookup"><span data-stu-id="90237-111">This allows the user to log out no matter where they are in the bot's dialogs.</span></span>

1. <span data-ttu-id="90237-112">在名为 **MainDialog.cs** 的 **./Dialogs** 目录中创建新文件并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="90237-112">Create a new file in the **./Dialogs** directory named **MainDialog.cs** and add the following code.</span></span>

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

    <span data-ttu-id="90237-113">花些时间查看此代码。</span><span class="sxs-lookup"><span data-stu-id="90237-113">Take a moment to review this code.</span></span>

    - <span data-ttu-id="90237-114">在构造函数中，它使用一组按顺序排列的步骤设置 [一个"瀑布](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) 图"。</span><span class="sxs-lookup"><span data-stu-id="90237-114">In the constructor, it sets up a [WaterfallDialog](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-waterfall-dialogs?view=azure-bot-service-4.0) with a set of steps that occur in order.</span></span>
        - <span data-ttu-id="90237-115">In `LoginPromptStepAsync` it sends an **OAuthPrompt**.</span><span class="sxs-lookup"><span data-stu-id="90237-115">In `LoginPromptStepAsync` it sends an **OAuthPrompt**.</span></span> <span data-ttu-id="90237-116">如果用户未登录，这会向用户发送 UI 提示。</span><span class="sxs-lookup"><span data-stu-id="90237-116">If the user isn't logged in, this will send a UI prompt to the user.</span></span>
        - <span data-ttu-id="90237-117">In `ProcessLoginStepAsync` it checks if the login was successful and sends a confirmation.</span><span class="sxs-lookup"><span data-stu-id="90237-117">In `ProcessLoginStepAsync` it checks if the login was successful and sends a confirmation.</span></span>
        - <span data-ttu-id="90237-118">In `PromptUserStepAsync` it sends a **ChoicePrompt** with the available commands.</span><span class="sxs-lookup"><span data-stu-id="90237-118">In `PromptUserStepAsync` it sends a **ChoicePrompt** with the available commands.</span></span>
        - <span data-ttu-id="90237-119">In `CommandStepAsync` it saves the user's choice， then resends an **OAuthPrompt**.</span><span class="sxs-lookup"><span data-stu-id="90237-119">In `CommandStepAsync` it saves the user's choice, then resends an **OAuthPrompt**.</span></span>
        - <span data-ttu-id="90237-120">In `ProcessStepAsync` it takes action based on the command received.</span><span class="sxs-lookup"><span data-stu-id="90237-120">In `ProcessStepAsync` it takes action based on the command received.</span></span>
        - <span data-ttu-id="90237-121">In `ReturnToPromptStepAsync` it starts the waterfall over， but passes a flag to skip the initial user login.</span><span class="sxs-lookup"><span data-stu-id="90237-121">In `ReturnToPromptStepAsync` it starts the waterfall over, but passes a flag to skip the initial user login.</span></span>

## <a name="update-calendarbot"></a><span data-ttu-id="90237-122">更新 CalendarBot</span><span class="sxs-lookup"><span data-stu-id="90237-122">Update CalendarBot</span></span>

<span data-ttu-id="90237-123">下一步是更新 **CalendarBot** 以使用这些新对话框。</span><span class="sxs-lookup"><span data-stu-id="90237-123">The next step is to update **CalendarBot** to use these new dialogs.</span></span>

1. <span data-ttu-id="90237-124">打开 **./Bots/CalendarBot.cs，** 并将其全部内容替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="90237-124">Open **./Bots/CalendarBot.cs** and replace its entire contents with the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Bots/CalendarBot.cs" id="CalendarBotSnippet":::

    <span data-ttu-id="90237-125">以下是更改的简短摘要。</span><span class="sxs-lookup"><span data-stu-id="90237-125">Here's a brief summary of the changes.</span></span>

    - <span data-ttu-id="90237-126">将 **CalendarBot** 类更改为模板类，以接收 **Dialog**。</span><span class="sxs-lookup"><span data-stu-id="90237-126">Changed the **CalendarBot** class to be a template class, receiving a **Dialog**.</span></span>
    - <span data-ttu-id="90237-127">更改 **了 CalendarBot** 类以扩展 **TeamsActivityHandler，** 允许其登录 Microsoft Teams。</span><span class="sxs-lookup"><span data-stu-id="90237-127">Changed the **CalendarBot** class to extend **TeamsActivityHandler**, allowing it to sign in in Microsoft Teams.</span></span>
    - <span data-ttu-id="90237-128">添加了其他方法替代以启用身份验证。</span><span class="sxs-lookup"><span data-stu-id="90237-128">Added additional method overrides to enable authentication.</span></span>

## <a name="update-startupcs"></a><span data-ttu-id="90237-129">更新 Startup.cs</span><span class="sxs-lookup"><span data-stu-id="90237-129">Update Startup.cs</span></span>

<span data-ttu-id="90237-130">最后一步是更新 方法， `ConfigureServices` 以添加身份验证所需的服务和新对话框。</span><span class="sxs-lookup"><span data-stu-id="90237-130">The final step is to update the `ConfigureServices` method to add the services needed for authentication and the new dialog.</span></span>

1. <span data-ttu-id="90237-131">打开 **./Startup.cs，** 然后从 `services.AddTransient<IBot, Bots.CalendarBot>();` 方法中删除 `ConfigureServices` 行。</span><span class="sxs-lookup"><span data-stu-id="90237-131">Open **./Startup.cs** and remove the `services.AddTransient<IBot, Bots.CalendarBot>();` line from the `ConfigureServices` method.</span></span>

1. <span data-ttu-id="90237-132">在 方法的末尾插入以下 `ConfigureServices` 代码。</span><span class="sxs-lookup"><span data-stu-id="90237-132">Insert the following code at the end of the `ConfigureServices` method.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="ConfigureServiceSnippet":::

## <a name="test-authentication"></a><span data-ttu-id="90237-133">测试身份验证</span><span class="sxs-lookup"><span data-stu-id="90237-133">Test authentication</span></span>

1. <span data-ttu-id="90237-134">保存所有更改，然后使用 启动自动程序 `dotnet run` 。</span><span class="sxs-lookup"><span data-stu-id="90237-134">Save all of your changes and start the bot with `dotnet run`.</span></span>

1. <span data-ttu-id="90237-135">打开Bot Framework Emulator。</span><span class="sxs-lookup"><span data-stu-id="90237-135">Open the Bot Framework Emulator.</span></span> <span data-ttu-id="90237-136">选择左&#9881;齿轮图标。</span><span class="sxs-lookup"><span data-stu-id="90237-136">Select the gear icon &#9881; on the bottom left.</span></span>

1. <span data-ttu-id="90237-137">输入 ngrok 安装的本地路径，并启用本地地址旁路 **ngrok** 和运行 **ngrok** Emulator启动选项。</span><span class="sxs-lookup"><span data-stu-id="90237-137">Enter the local path to your installation of ngrok, and enable the **Bypass ngrok for local addresses** and **Run ngrok when the Emulator starts up** options.</span></span> <span data-ttu-id="90237-138">选择“**保存**”。</span><span class="sxs-lookup"><span data-stu-id="90237-138">Select **Save**.</span></span>

1. <span data-ttu-id="90237-139">选择"**文件"** 菜单，然后选择"**新建自动程序配置..."。**</span><span class="sxs-lookup"><span data-stu-id="90237-139">Select the **File** menu, then **New Bot Configuration...**.</span></span>

1. <span data-ttu-id="90237-140">填写字段，如下所示。</span><span class="sxs-lookup"><span data-stu-id="90237-140">Fill in the fields as follows.</span></span>

    - <span data-ttu-id="90237-141">**自动程序名称：**`CalendarBot`</span><span class="sxs-lookup"><span data-stu-id="90237-141">**Bot name:** `CalendarBot`</span></span>
    - <span data-ttu-id="90237-142">**终结点 URL：**`https://localhost:3978/api/messages`</span><span class="sxs-lookup"><span data-stu-id="90237-142">**Endpoint URL:** `https://localhost:3978/api/messages`</span></span>
    - <span data-ttu-id="90237-143">**Microsoft 应用 ID：** 日历自动程序Graph **的应用程序** ID</span><span class="sxs-lookup"><span data-stu-id="90237-143">**Microsoft App ID:** the application ID of your **Graph Calendar Bot** app registration</span></span>
    - <span data-ttu-id="90237-144">**Microsoft 应用密码：Graph\*\*\*\*日历自动程序** 客户端密码</span><span class="sxs-lookup"><span data-stu-id="90237-144">**Microsoft App password:** your **Graph Calendar Bot** client secret</span></span>
    - <span data-ttu-id="90237-145">**对存储在自动程序配置中的密钥进行加密：** 已启用</span><span class="sxs-lookup"><span data-stu-id="90237-145">**Encrypt keys stored in your bot configuration:** Enabled</span></span>

    !["新建自动程序配置"对话框的屏幕截图](images/new-bot-config.png)

1. <span data-ttu-id="90237-147">选择 **"保存并连接"。**</span><span class="sxs-lookup"><span data-stu-id="90237-147">Select **Save and connect**.</span></span> <span data-ttu-id="90237-148">在仿真器连接后，你应该会看到 `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`</span><span class="sxs-lookup"><span data-stu-id="90237-148">After the emulator connects, you should see `Welcome to Microsoft Graph CalendarBot. Type anything to get started.`</span></span>

1. <span data-ttu-id="90237-149">键入一些文本并将其发送到机器人。</span><span class="sxs-lookup"><span data-stu-id="90237-149">Type some text and send it to the bot.</span></span> <span data-ttu-id="90237-150">机器人使用登录提示进行响应。</span><span class="sxs-lookup"><span data-stu-id="90237-150">The bot responds with a login prompt.</span></span>

1. <span data-ttu-id="90237-151">选择" **登录"** 按钮。</span><span class="sxs-lookup"><span data-stu-id="90237-151">Select the **Login** button.</span></span> <span data-ttu-id="90237-152">仿真器会提示你确认以 开头的 `oauthlink://https://token.botframeworkcom` URL。</span><span class="sxs-lookup"><span data-stu-id="90237-152">The emulator prompts you to confirm the URL that starts with `oauthlink://https://token.botframeworkcom`.</span></span> <span data-ttu-id="90237-153">选择 **"确认** "继续。</span><span class="sxs-lookup"><span data-stu-id="90237-153">Select **Confirm** to continue.</span></span>

1. <span data-ttu-id="90237-154">在弹出窗口中，使用你的 Microsoft 365 帐户登录。</span><span class="sxs-lookup"><span data-stu-id="90237-154">In the pop-up window, login with your Microsoft 365 account.</span></span> <span data-ttu-id="90237-155">查看请求的权限并接受。</span><span class="sxs-lookup"><span data-stu-id="90237-155">Review the requested permissions and accept.</span></span>

1. <span data-ttu-id="90237-156">身份验证和同意完成后，弹出窗口将提供验证代码。</span><span class="sxs-lookup"><span data-stu-id="90237-156">Once authentication and consent are complete, the pop-up window provides a validation code.</span></span> <span data-ttu-id="90237-157">复制代码并关闭窗口。</span><span class="sxs-lookup"><span data-stu-id="90237-157">Copy the code and close the window.</span></span>

    ![验证代码Bot Framework Emulator屏幕截图](images/validation-code.png)

1. <span data-ttu-id="90237-159">在聊天窗口中输入验证代码以完成登录。</span><span class="sxs-lookup"><span data-stu-id="90237-159">Enter the validation code in the chat window to complete the login.</span></span>

    ![与示例自动程序进行登录对话的屏幕截图](images/bot-login.png)

1. <span data-ttu-id="90237-161">如果选择 **"显示令牌** "按钮 (或键入) ，则自动 `show token` 程序将显示访问令牌。</span><span class="sxs-lookup"><span data-stu-id="90237-161">If you select the **Show token** button (or type `show token`), the bot displays the access token.</span></span> <span data-ttu-id="90237-162">" **注销"** 按钮 (键入) `log out` 将注销。</span><span class="sxs-lookup"><span data-stu-id="90237-162">The **Log out** button (or typing `log out`) will log you out.</span></span>

> [!TIP]
> <span data-ttu-id="90237-163">与机器人开始对话时，Bot Framework Emulator消息中出现以下错误消息。</span><span class="sxs-lookup"><span data-stu-id="90237-163">You may receive the following error message in the Bot Framework Emulator when starting a conversation with the bot.</span></span>
>
> ```text
> Failed to generate an actual sign-in link: Error: Failed to connect to ngrok instance for OAuth postback URL:
> FetchError: request to http://127.0.0.1:4041/api/tunnels failed, reason: connect ECONNREFUSED 127.0.0.1:4041
> ```
>
> <span data-ttu-id="90237-164">如果发生这种情况，请确保在仿真器设置中启用"Emulator启动时运行 **ngrok"** 选项，然后重新启动仿真器。</span><span class="sxs-lookup"><span data-stu-id="90237-164">If this happens, be sure to enable the **Run ngrok when the Emulator starts up** option in the emulator settings and restart the emulator.</span></span>
