---
ms.openlocfilehash: dc95fc9f1ca24e0a1e3cd16e93597057ebbae787
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446735"
---
<!-- markdownlint-disable MD002 MD041 -->

在此部分中，你将使用 Microsoft Graph SDK 将事件添加到用户的日历。

## <a name="implement-a-dialog"></a>实现对话框

首先，新建一个自定义对话框，提示用户输入将事件添加到其日历所需的值。 此对话框将使用 **一个"一流"Dialog** 执行以下步骤。

- 提示主题
- 询问用户是否要邀请人员
- 如果用户在上一步骤 (是，则提示与会者) 
- 提示开始日期和时间
- 提示结束日期和时间
- 显示所有收集的值，并要求用户确认
- 如果用户确认，请从 **OAuthPrompt 获取访问令牌**
- 创建事件

1. 在 **./Dialogs** 目录中新建一个名为 **NewEventDialog.cs** 的文件并添加以下代码。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using CalendarBot.Graph;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.Graph;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    using TimexTypes = Microsoft.Recognizers.Text.DataTypes.TimexExpression.Constants.TimexTypes;

    namespace CalendarBot.Dialogs
    {
        public class NewEventDialog : LogoutDialog
        {
            protected readonly ILogger _logger;
            private readonly IGraphClientService _graphClientService;

            public NewEventDialog(
                IConfiguration configuration,
                IGraphClientService graphClientService)
                : base(nameof(NewEventDialog), configuration["ConnectionName"])
            {

            }

            // Generate a DateTime from the list of
            // DateTimeResolutions provided by the DateTimePrompt
            private static DateTime GetDateTimeFromResolutions(IList<DateTimeResolution> resolutions)
            {
                var timex = new TimexProperty(resolutions[0].Timex);

                // Handle the "now" case
                if (timex.Now ?? false)
                {
                    return DateTime.Now;
                }

                // Otherwise generate a DateTime
                return TimexHelpers.DateFromTimex(timex);
            }
        }
    }
    ```

    这是新对话框的 shell，它将提示用户输入将事件添加到其日历所需的值。

1. 将以下函数添加到 **NewEventDialog** 类以提示用户输入主题。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForSubjectSnippet":::

1. 将以下函数添加到 **NewEventDialog** 类以存储用户在上一步中提供的主题，并询问他们是否要添加与会者。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAddAttendeesSnippet":::

1. 将以下函数添加到 **NewEventDialog** 类以检查用户上一步的响应，并提示用户输入与会者列表（如果需要）。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAttendeesSnippet":::

1. 将以下函数添加到 **NewEventDialog** 类以存储上一步骤 (（如果存在）中的与会者列表) 并提示用户输入开始日期和时间。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForStartSnippet":::

1. 将以下函数添加到 **NewEventDialog** 类以存储上一步中的起始值，并提示用户输入结束日期和时间。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForEndSnippet":::

1. 将以下函数添加到 **NewEventDialog** 类以存储上一步中的结束值，并要求用户确认所有输入。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConfirmNewEventSnippet":::

1. 将以下函数添加到 **NewEventDialog** 类以检查用户上一步的响应。 如果用户确认输入，请使用 **OAuthPrompt** 类获取访问令牌。 否则，请结束对话框。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="GetTokenSnippet":::

1. 将以下函数添加到 **NewEventDialog** 类，以使用 Microsoft Graph SDK 创建新事件。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AddEventSnippet":::

    考虑此代码执行哪些功能。

    - 它获取用户的 **MailboxSettings** 以确定用户的首选时区。
    - 它使用用户提供的值创建 **Event** 对象。 请注意 **，Start** 和 **End** 属性是使用用户的时区设置的。
    - 它在用户的日历上创建事件。

## <a name="add-validation"></a>添加验证

现在，向用户输入添加验证，以避免在使用 Microsoft Graph 创建事件时出现错误。 我们希望确保：

- 如果用户提供了与会者列表，则该列表应是有效电子邮件地址的分号分隔列表。
- 开始日期/时间应为有效的日期和时间。
- 结束日期/时间应为有效的日期和时间，并且应晚于开始时间。

1. 将以下函数添加到 **NewEventDialog** 类以验证用户的与会者条目。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AttendeesValidatorSnippet":::

1. 将以下函数添加到 **NewEventDialog** 类以验证用户条目的开始日期和时间。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="StartValidatorSnippet":::

1. 将以下函数添加到 **NewEventDialog** 类以验证用户的结束日期和时间条目。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="EndValidatorSnippet":::

## <a name="add-steps-to-waterfalldialog"></a>向"瀑布""Dialog"添加步骤

现在，你已拥有对话框的所有"步骤"，最后一步是将它们添加到构造函数中的 **一个"瀑布"Dialog** 中，然后将 **NewEventDialog** 添加到 **MainDialog** 中。

1. 找到 **NewEventDialog** 构造函数，并添加以下代码。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConstructorSnippet":::

    这将添加所有使用的对话框，并将你实现的所有函数添加为 **在"瀑布"Dialog 中的步骤**。

1. 打开 **./Dialogs/MainDialog.cs，** 然后向构造函数中添加以下行。

    ```csharp
    AddDialog(new NewEventDialog(configuration, graphClientService));
    ```

1. 将 中的 `else if (command.StartsWith("add event"))` 块内的代码 `ProcessStepAsync` 替换为以下内容。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="AddEventSnippet" highlight="3":::

1. 保存所有更改并重新启动自动程序。

1. 使用 Bot Framework Emulator连接到自动程序并登录。 选择" **添加事件"** 按钮。

1. 响应提示以创建新事件。 请注意，当提示您输入 start 和 end 值时，您可以使用类似"today at 3PM"或"now"的短语。

    ![加载项中的"添加事件"对话框Bot Framework Emulator](images/add-event.png)
