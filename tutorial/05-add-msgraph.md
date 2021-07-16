---
ms.openlocfilehash: ab38560fe196ea76bdb1928f218b83aa9c769496
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446783"
---
<!-- markdownlint-disable MD002 MD041 -->

在此部分中，你将使用 Microsoft Graph SDK 获取登录用户。

## <a name="create-a-graph-service"></a>创建 Graph 服务

首先实现机器人可用于从 Microsoft Graph SDK 获取 **GraphServiceClient** 的服务，然后通过依赖关系注入使该服务对机器人可用。

1. 在名为 Graph 的项目的 **根目录中创建** 一Graph。 在名为 **IGraphClientService.cs** **的 ./Graph** 目录中创建新文件并添加以下代码。

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. 在名为 **GraphClientService.cs** **的 ./Graph** 目录中创建新文件并添加以下代码。

    :::code language="csharp" source="../demo/GraphCalendarBot/Graph/GraphClientService.cs" id="GraphClientServiceSnippet":::

1. 打开 **./Startup.cs，** 在文件顶部 `using` 添加以下语句。

    ```csharp
    using CalendarBot.Graph;
    ```

1. 将以下代码添加到`ConfigureServices` 函数末尾。

    :::code language="csharp" source="../demo/GraphCalendarBot/Startup.cs" id="AddGraphServiceSnippet":::

1. 打开 **./Dialogs/MainDialog.cs**。 将以下 `using` 语句添加到文件顶部。

    ```csharp
    using System;
    using System.IO;
    using AdaptiveCards;
    using CalendarBot.Graph;
    using Microsoft.Graph;
    ```

1. 将以下属性添加到 **MainDialog** 类。

    ```csharp
    private readonly IGraphClientService _graphClientService;
    ```

1. 找到 **MainDialog** 类的构造函数，并更新其签名以使用 **IGraphServiceClient** 参数。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ConstructorSignatureSnippet" highlight="4":::

1. 将以下代码添加到构造函数。

    ```csharp
    _graphClientService = graphClientService;
    ```

## <a name="get-the-logged-on-user"></a>获取登录的用户

在此部分中，你将使用 Microsoft Graph获取用户名、电子邮件地址和照片。 然后，你将创建自适应卡片以显示信息。

1. 在名为 **CardHelper.cs 的项目的根目录下创建一个新文件**。 将以下代码添加到文件中。

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

    此代码使用 **AdaptiveCard** NuGet 包生成自适应卡片来显示用户。

1. 将以下函数添加到 **MainDialog** 类。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="DisplayLoggedInUserSnippet":::

    考虑此代码执行哪些功能。

    - 它使用 **graphClient** [获取登录的用户](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0)。
        - 它使用 `Select` 方法限制返回的字段。
    - 它使用 **graphClient** [](https://docs.microsoft.com/graph/api/profilephoto-get?view=graph-rest-1.0)获取用户的照片，请求受支持的最小大小为 48x48 像素。
    - 它使用 **CardHelper** 类构造自适应卡片，并将该卡片作为附件发送。

1. 将 中的 `else if (command.StartsWith("show me"))` 块内的代码 `ProcessStepAsync` 替换为以下内容。

    :::code language="csharp" source="../demo/GraphCalendarBot/Dialogs/MainDialog.cs" id="ShowMeSnippet" highlight="3":::

1. 保存所有更改并重新启动自动程序。

1. 使用 Bot Framework Emulator连接到自动程序并登录。 选择 **"显示我** "按钮以显示登录的用户。

    ![显示用户的自适应卡片屏幕截图](images/user-card.png)
