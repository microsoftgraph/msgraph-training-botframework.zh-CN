---
ms.openlocfilehash: dc95fc9f1ca24e0a1e3cd16e93597057ebbae787
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446735"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="775b5-101">在此部分中，你将使用 Microsoft Graph SDK 将事件添加到用户的日历。</span><span class="sxs-lookup"><span data-stu-id="775b5-101">In this section you'll use the Microsoft Graph SDK to add an event to the user's calendar.</span></span>

## <a name="implement-a-dialog"></a><span data-ttu-id="775b5-102">实现对话框</span><span class="sxs-lookup"><span data-stu-id="775b5-102">Implement a dialog</span></span>

<span data-ttu-id="775b5-103">首先，新建一个自定义对话框，提示用户输入将事件添加到其日历所需的值。</span><span class="sxs-lookup"><span data-stu-id="775b5-103">Start by creating a new custom dialog to prompt the user for the values needed to add an event to their calendar.</span></span> <span data-ttu-id="775b5-104">此对话框将使用 **一个"一流"Dialog** 执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="775b5-104">This dialog will use a **WaterfallDialog** to do the following steps.</span></span>

- <span data-ttu-id="775b5-105">提示主题</span><span class="sxs-lookup"><span data-stu-id="775b5-105">Prompt for a subject</span></span>
- <span data-ttu-id="775b5-106">询问用户是否要邀请人员</span><span class="sxs-lookup"><span data-stu-id="775b5-106">Ask if the user wants to invite people</span></span>
- <span data-ttu-id="775b5-107">如果用户在上一步骤 (是，则提示与会者) </span><span class="sxs-lookup"><span data-stu-id="775b5-107">Prompt for attendees (if user said yes to previous step)</span></span>
- <span data-ttu-id="775b5-108">提示开始日期和时间</span><span class="sxs-lookup"><span data-stu-id="775b5-108">Prompt for a start date and time</span></span>
- <span data-ttu-id="775b5-109">提示结束日期和时间</span><span class="sxs-lookup"><span data-stu-id="775b5-109">Prompt for an end date and time</span></span>
- <span data-ttu-id="775b5-110">显示所有收集的值，并要求用户确认</span><span class="sxs-lookup"><span data-stu-id="775b5-110">Display all of the collected values and ask user to confirm</span></span>
- <span data-ttu-id="775b5-111">如果用户确认，请从 **OAuthPrompt 获取访问令牌**</span><span class="sxs-lookup"><span data-stu-id="775b5-111">If user confirms, get access token from **OAuthPrompt**</span></span>
- <span data-ttu-id="775b5-112">创建事件</span><span class="sxs-lookup"><span data-stu-id="775b5-112">Create the event</span></span>

1. <span data-ttu-id="775b5-113">在 **./Dialogs** 目录中新建一个名为 **NewEventDialog.cs** 的文件并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="775b5-113">Create a new file in the **./Dialogs** directory named **NewEventDialog.cs** and add the following code.</span></span>

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

    <span data-ttu-id="775b5-114">这是新对话框的 shell，它将提示用户输入将事件添加到其日历所需的值。</span><span class="sxs-lookup"><span data-stu-id="775b5-114">This is the shell of a new dialog that will prompt the user for the values needed to add an event to their calendar.</span></span>

1. <span data-ttu-id="775b5-115">将以下函数添加到 **NewEventDialog** 类以提示用户输入主题。</span><span class="sxs-lookup"><span data-stu-id="775b5-115">Add the following function to the **NewEventDialog** class to prompt the user for a subject.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForSubjectSnippet":::

1. <span data-ttu-id="775b5-116">将以下函数添加到 **NewEventDialog** 类以存储用户在上一步中提供的主题，并询问他们是否要添加与会者。</span><span class="sxs-lookup"><span data-stu-id="775b5-116">Add the following function to the **NewEventDialog** class to store the subject the user gave in the previous step, and to ask if they want to add attendees.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAddAttendeesSnippet":::

1. <span data-ttu-id="775b5-117">将以下函数添加到 **NewEventDialog** 类以检查用户上一步的响应，并提示用户输入与会者列表（如果需要）。</span><span class="sxs-lookup"><span data-stu-id="775b5-117">Add the following function to the **NewEventDialog** class to check the user's response from the previous step and prompt the user for a list of attendees if needed.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForAttendeesSnippet":::

1. <span data-ttu-id="775b5-118">将以下函数添加到 **NewEventDialog** 类以存储上一步骤 (（如果存在）中的与会者列表) 并提示用户输入开始日期和时间。</span><span class="sxs-lookup"><span data-stu-id="775b5-118">Add the following function to the **NewEventDialog** class to store the list of attendees from the previous step (if present) and prompt the user for a start date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForStartSnippet":::

1. <span data-ttu-id="775b5-119">将以下函数添加到 **NewEventDialog** 类以存储上一步中的起始值，并提示用户输入结束日期和时间。</span><span class="sxs-lookup"><span data-stu-id="775b5-119">Add the following function to the **NewEventDialog** class to store the start value from the previous step and prompt the user for an end date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="PromptForEndSnippet":::

1. <span data-ttu-id="775b5-120">将以下函数添加到 **NewEventDialog** 类以存储上一步中的结束值，并要求用户确认所有输入。</span><span class="sxs-lookup"><span data-stu-id="775b5-120">Add the following function to the **NewEventDialog** class to store the end value from the previous step and ask the user to confirm all of the inputs.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConfirmNewEventSnippet":::

1. <span data-ttu-id="775b5-121">将以下函数添加到 **NewEventDialog** 类以检查用户上一步的响应。</span><span class="sxs-lookup"><span data-stu-id="775b5-121">Add the following function to the **NewEventDialog** class to check the user's response from the previous step.</span></span> <span data-ttu-id="775b5-122">如果用户确认输入，请使用 **OAuthPrompt** 类获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="775b5-122">If the user confirms the inputs, use the **OAuthPrompt** class to get an access token.</span></span> <span data-ttu-id="775b5-123">否则，请结束对话框。</span><span class="sxs-lookup"><span data-stu-id="775b5-123">Otherwise, end the dialog.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="GetTokenSnippet":::

1. <span data-ttu-id="775b5-124">将以下函数添加到 **NewEventDialog** 类，以使用 Microsoft Graph SDK 创建新事件。</span><span class="sxs-lookup"><span data-stu-id="775b5-124">Add the following function to the **NewEventDialog** class to use the Microsoft Graph SDK to create the new event.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AddEventSnippet":::

    <span data-ttu-id="775b5-125">考虑此代码执行哪些功能。</span><span class="sxs-lookup"><span data-stu-id="775b5-125">Consider what this code does.</span></span>

    - <span data-ttu-id="775b5-126">它获取用户的 **MailboxSettings** 以确定用户的首选时区。</span><span class="sxs-lookup"><span data-stu-id="775b5-126">It gets the user's **MailboxSettings** to determine the user's preferred time zone.</span></span>
    - <span data-ttu-id="775b5-127">它使用用户提供的值创建 **Event** 对象。</span><span class="sxs-lookup"><span data-stu-id="775b5-127">It creates an **Event** object using the values provided by the user.</span></span> <span data-ttu-id="775b5-128">请注意 **，Start** 和 **End** 属性是使用用户的时区设置的。</span><span class="sxs-lookup"><span data-stu-id="775b5-128">Note that the **Start** and **End** properties are set with the user's time zone.</span></span>
    - <span data-ttu-id="775b5-129">它在用户的日历上创建事件。</span><span class="sxs-lookup"><span data-stu-id="775b5-129">It creates the event on the user's calendar.</span></span>

## <a name="add-validation"></a><span data-ttu-id="775b5-130">添加验证</span><span class="sxs-lookup"><span data-stu-id="775b5-130">Add validation</span></span>

<span data-ttu-id="775b5-131">现在，向用户输入添加验证，以避免在使用 Microsoft Graph 创建事件时出现错误。</span><span class="sxs-lookup"><span data-stu-id="775b5-131">Now add validation to the user's input to avoid errors when creating the event with Microsoft Graph.</span></span> <span data-ttu-id="775b5-132">我们希望确保：</span><span class="sxs-lookup"><span data-stu-id="775b5-132">We want to make sure that:</span></span>

- <span data-ttu-id="775b5-133">如果用户提供了与会者列表，则该列表应是有效电子邮件地址的分号分隔列表。</span><span class="sxs-lookup"><span data-stu-id="775b5-133">If the user gives an attendee list, it should be a semicolon-delimited list of valid email addresses.</span></span>
- <span data-ttu-id="775b5-134">开始日期/时间应为有效的日期和时间。</span><span class="sxs-lookup"><span data-stu-id="775b5-134">The start date/time should be a valid date and time.</span></span>
- <span data-ttu-id="775b5-135">结束日期/时间应为有效的日期和时间，并且应晚于开始时间。</span><span class="sxs-lookup"><span data-stu-id="775b5-135">The end date/time should be a valid date and time, and should be later than the start.</span></span>

1. <span data-ttu-id="775b5-136">将以下函数添加到 **NewEventDialog** 类以验证用户的与会者条目。</span><span class="sxs-lookup"><span data-stu-id="775b5-136">Add the following function to the **NewEventDialog** class to validate the user's entry for attendees.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="AttendeesValidatorSnippet":::

1. <span data-ttu-id="775b5-137">将以下函数添加到 **NewEventDialog** 类以验证用户条目的开始日期和时间。</span><span class="sxs-lookup"><span data-stu-id="775b5-137">Add the following functions to the **NewEventDialog** class to validate the user's entry for start date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="StartValidatorSnippet":::

1. <span data-ttu-id="775b5-138">将以下函数添加到 **NewEventDialog** 类以验证用户的结束日期和时间条目。</span><span class="sxs-lookup"><span data-stu-id="775b5-138">Add the following function to the **NewEventDialog** class to validate the user's entry for end date and time.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="EndValidatorSnippet":::

## <a name="add-steps-to-waterfalldialog"></a><span data-ttu-id="775b5-139">向"瀑布""Dialog"添加步骤</span><span class="sxs-lookup"><span data-stu-id="775b5-139">Add steps to WaterfallDialog</span></span>

<span data-ttu-id="775b5-140">现在，你已拥有对话框的所有"步骤"，最后一步是将它们添加到构造函数中的 **一个"瀑布"Dialog** 中，然后将 **NewEventDialog** 添加到 **MainDialog** 中。</span><span class="sxs-lookup"><span data-stu-id="775b5-140">Now that you have all of the "steps" for the dialog, the last step is to add them to a **WaterfallDialog** in the constructor, then add the **NewEventDialog** to the **MainDialog**.</span></span>

1. <span data-ttu-id="775b5-141">找到 **NewEventDialog** 构造函数，并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="775b5-141">Locate the **NewEventDialog** constructor and add the following code to it.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/NewEventDialog.cs" id="ConstructorSnippet":::

    <span data-ttu-id="775b5-142">这将添加所有使用的对话框，并将你实现的所有函数添加为 **在"瀑布"Dialog 中的步骤**。</span><span class="sxs-lookup"><span data-stu-id="775b5-142">This adds all of the dialogs that are used, and adds all of the functions you implemented as steps in the **WaterfallDialog**.</span></span>

1. <span data-ttu-id="775b5-143">打开 **./Dialogs/MainDialog.cs，** 然后向构造函数中添加以下行。</span><span class="sxs-lookup"><span data-stu-id="775b5-143">Open **./Dialogs/MainDialog.cs** and add the following line to the constructor.</span></span>

    ```csharp
    AddDialog(new NewEventDialog(configuration, graphClientService));
    ```

1. <span data-ttu-id="775b5-144">将 中的 `else if (command.StartsWith("add event"))` 块内的代码 `ProcessStepAsync` 替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="775b5-144">Replace the code inside the `else if (command.StartsWith("add event"))` block in `ProcessStepAsync` with the following.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="AddEventSnippet" highlight="3":::

1. <span data-ttu-id="775b5-145">保存所有更改并重新启动自动程序。</span><span class="sxs-lookup"><span data-stu-id="775b5-145">Save all of your changes and restart the bot.</span></span>

1. <span data-ttu-id="775b5-146">使用 Bot Framework Emulator连接到自动程序并登录。</span><span class="sxs-lookup"><span data-stu-id="775b5-146">Use the Bot Framework Emulator to connect to the bot and log in.</span></span> <span data-ttu-id="775b5-147">选择" **添加事件"** 按钮。</span><span class="sxs-lookup"><span data-stu-id="775b5-147">Select the **Add event** button.</span></span>

1. <span data-ttu-id="775b5-148">响应提示以创建新事件。</span><span class="sxs-lookup"><span data-stu-id="775b5-148">Respond to the prompts to create a new event.</span></span> <span data-ttu-id="775b5-149">请注意，当提示您输入 start 和 end 值时，您可以使用类似"today at 3PM"或"now"的短语。</span><span class="sxs-lookup"><span data-stu-id="775b5-149">Note that when prompted for start and end values, you can use phrases like "today at 3PM", or "now".</span></span>

    ![加载项中的"添加事件"对话框Bot Framework Emulator](images/add-event.png)
