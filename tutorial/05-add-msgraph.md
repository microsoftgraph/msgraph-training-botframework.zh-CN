---
ms.openlocfilehash: ab38560fe196ea76bdb1928f218b83aa9c769496
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446783"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="5bd1d-101">在此部分中，你将使用 Microsoft Graph SDK 获取登录用户。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-101">In this section you'll use the Microsoft Graph SDK to get the logged-in user.</span></span>

## <a name="create-a-graph-service"></a><span data-ttu-id="5bd1d-102">创建 Graph 服务</span><span class="sxs-lookup"><span data-stu-id="5bd1d-102">Create a Graph service</span></span>

<span data-ttu-id="5bd1d-103">首先实现机器人可用于从 Microsoft Graph SDK 获取 **GraphServiceClient** 的服务，然后通过依赖关系注入使该服务对机器人可用。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-103">Start by implementing a service that the bot can use to get a **GraphServiceClient** from the Microsoft Graph SDK, then making that service available to the bot via dependency injection.</span></span>

1. <span data-ttu-id="5bd1d-104">在名为 Graph 的项目的 **根目录中创建** 一Graph。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-104">Create a new directory in the root of the project named **Graph**.</span></span> <span data-ttu-id="5bd1d-105">在名为 **IGraphClientService.cs** **的 ./Graph** 目录中创建新文件并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-105">Create a new file in the **./Graph** directory named **IGraphClientService.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. <span data-ttu-id="5bd1d-106">在名为 **GraphClientService.cs** **的 ./Graph** 目录中创建新文件并添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-106">Create a new file in the **./Graph** directory named **GraphClientService.cs** and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/GraphClientService.cs" id="GraphClientServiceSnippet":::

1. <span data-ttu-id="5bd1d-107">打开 **./Startup.cs，** 在文件顶部 `using` 添加以下语句。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-107">Open **./Startup.cs** and add the following `using` statement at the top of the file.</span></span>

    ```csharp
    using CalendarBot.Graph;
    ```

1. <span data-ttu-id="5bd1d-108">将以下代码添加到`ConfigureServices` 函数末尾。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-108">Add the following code to the end of the `ConfigureServices` function.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="AddGraphServiceSnippet":::

1. <span data-ttu-id="5bd1d-109">打开 **./Dialogs/MainDialog.cs**。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-109">Open **./Dialogs/MainDialog.cs**.</span></span> <span data-ttu-id="5bd1d-110">将以下 `using` 语句添加到文件顶部。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-110">Add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System;
    using System.IO;
    using AdaptiveCards;
    using CalendarBot.Graph;
    using Microsoft.Graph;
    ```

1. <span data-ttu-id="5bd1d-111">将以下属性添加到 **MainDialog** 类。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-111">Add the following property to the **MainDialog** class.</span></span>

    ```csharp
    private readonly IGraphClientService _graphClientService;
    ```

1. <span data-ttu-id="5bd1d-112">找到 **MainDialog** 类的构造函数，并更新其签名以使用 **IGraphServiceClient** 参数。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-112">Locate the constructor for the **MainDialog** class and update its signature to take an **IGraphServiceClient** parameter.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ConstructorSignatureSnippet" highlight="4":::

1. <span data-ttu-id="5bd1d-113">将以下代码添加到构造函数。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-113">Add the following code to the constructor.</span></span>

    ```csharp
    _graphClientService = graphClientService;
    ```

## <a name="get-the-logged-on-user"></a><span data-ttu-id="5bd1d-114">获取登录的用户</span><span class="sxs-lookup"><span data-stu-id="5bd1d-114">Get the logged on user</span></span>

<span data-ttu-id="5bd1d-115">在此部分中，你将使用 Microsoft Graph获取用户名、电子邮件地址和照片。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-115">In this section you'll use the Microsoft Graph to get the user's name, email address, and photo.</span></span> <span data-ttu-id="5bd1d-116">然后，你将创建自适应卡片以显示信息。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-116">Then you'll create an Adaptive Card to show the information.</span></span>

1. <span data-ttu-id="5bd1d-117">在名为 **CardHelper.cs 的项目的根目录下创建一个新文件**。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-117">Create a new file in the root of the project named **CardHelper.cs**.</span></span> <span data-ttu-id="5bd1d-118">将以下代码添加到文件中。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-118">Add the following code to the file.</span></span>

    ```csharp
    using AdaptiveCards;
    using Microsoft.Graph;
    using System;
    using System.IO;

    namespace CalendarBot
    {
        public class CardHelper
        {
            public static AdaptiveCard GetUserCard(User user, Stream photo)
            {
              // Create an Adaptive Card to display the user
                // See https://adaptivecards.io/designer/ for possibilities
                var userCard = new AdaptiveCard("1.2");

                var columns = new AdaptiveColumnSet();
                userCard.Body.Add(columns);

                var userPhotoColumn = new AdaptiveColumn { Width = AdaptiveColumnWidth.Auto };
                columns.Columns.Add(userPhotoColumn);

                userPhotoColumn.Items.Add(new AdaptiveImage {
                    Style = AdaptiveImageStyle.Person,
                    Size = AdaptiveImageSize.Small,
                    Url = GetDataUriFromPhoto(photo)
                });

                var userInfoColumn = new AdaptiveColumn {Width = AdaptiveColumnWidth.Stretch };
                columns.Columns.Add(userInfoColumn);

                userInfoColumn.Items.Add(new AdaptiveTextBlock {
                    Weight = AdaptiveTextWeight.Bolder,
                    Wrap = true,
                    Text = user.DisplayName
                });

                userInfoColumn.Items.Add(new AdaptiveTextBlock {
                    Spacing = AdaptiveSpacing.None,
                    IsSubtle = true,
                    Wrap = true,
                    Text = user.Mail ?? user.UserPrincipalName
                });

                return userCard;
            }

            private static Uri GetDataUriFromPhoto(Stream photo)
            {
                // Copy to a MemoryStream to get access to bytes
                var photoStream = new MemoryStream();
                photo.CopyTo(photoStream);

                var photoBytes = photoStream.ToArray();

                return new Uri($"data:image/png;base64,{Convert.ToBase64String(photoBytes)}");
            }
        }
    }
    ```

    <span data-ttu-id="5bd1d-119">此代码使用 **AdaptiveCard** NuGet 包生成自适应卡片来显示用户。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-119">This code uses the **AdaptiveCard** NuGet package to build an Adaptive Card to display the user.</span></span>

1. <span data-ttu-id="5bd1d-120">将以下函数添加到 **MainDialog** 类。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-120">Add the following function to the **MainDialog** class.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="DisplayLoggedInUserSnippet":::

    <span data-ttu-id="5bd1d-121">考虑此代码执行哪些功能。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-121">Consider what this code does.</span></span>

    - <span data-ttu-id="5bd1d-122">它使用 **graphClient** [获取登录的用户](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0)。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-122">It uses the **graphClient** to [get the logged-in user](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0).</span></span>
        - <span data-ttu-id="5bd1d-123">它使用 `Select` 方法限制返回的字段。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-123">It uses the `Select` method to limit which fields are returned.</span></span>
    - <span data-ttu-id="5bd1d-124">它使用 **graphClient** [](https://docs.microsoft.com/graph/api/profilephoto-get?view=graph-rest-1.0)获取用户的照片，请求受支持的最小大小为 48x48 像素。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-124">It uses the **graphClient** to [get the user's photo](https://docs.microsoft.com/graph/api/profilephoto-get?view=graph-rest-1.0), requesting the smallest supported size of 48x48 pixels.</span></span>
    - <span data-ttu-id="5bd1d-125">它使用 **CardHelper** 类构造自适应卡片，并将该卡片作为附件发送。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-125">It uses the **CardHelper** class to construct an Adaptive Card and sends the card as an attachment.</span></span>

1. <span data-ttu-id="5bd1d-126">将 中的 `else if (command.StartsWith("show me"))` 块内的代码 `ProcessStepAsync` 替换为以下内容。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-126">Replace the code inside the `else if (command.StartsWith("show me"))` block in `ProcessStepAsync` with the following.</span></span>

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ShowMeSnippet" highlight="3":::

1. <span data-ttu-id="5bd1d-127">保存所有更改并重新启动自动程序。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-127">Save all of your changes and restart the bot.</span></span>

1. <span data-ttu-id="5bd1d-128">使用 Bot Framework Emulator连接到自动程序并登录。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-128">Use the Bot Framework Emulator to connect to the bot and log in.</span></span> <span data-ttu-id="5bd1d-129">选择 **"显示我** "按钮以显示登录的用户。</span><span class="sxs-lookup"><span data-stu-id="5bd1d-129">Select the **Show me** button to display the logged-on user.</span></span>

    ![显示用户的自适应卡片屏幕截图](images/user-card.png)
