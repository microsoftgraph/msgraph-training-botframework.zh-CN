---
ms.openlocfilehash: 883eff2860fe2555cdd816cb942a4a87918a5836
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446825"
---
<!-- markdownlint-disable MD002 MD041 -->

在此练习中，你将使用 Azure 门户创建新的自动程序通道注册和 Azure AD Web 应用程序注册。

## <a name="create-a-bot-channels-registration"></a>创建自动程序频道注册

1. 打开浏览器并导航到 [Azure 门户](https://portal.azure.com)。 使用与 Azure 订阅关联的帐户登录。

1. 选择左上角的菜单，然后选择创建 **资源**。

    ![Azure 门户菜单的屏幕截图](images/create-resource.png)

1. 在"**新建"** 页面上，搜索 `Azure Bot` 并选择 **"Azure 自动程序"。**

1. 在 **"Azure 自动程序"** 页面上，选择"创建 **"。**

1. 填写必填字段，将 **消息终结点留空** 。 自动 **程序句柄** 字段必须是唯一的。 请务必查看不同的定价层，并选择适合你的方案的情况。 如果这只是一个学习练习，你可能想要选择免费选项。

1. 对于 **"Microsoft 应用 ID"，****选择"创建新的 Microsoft 应用 ID"。**

1. 然后“**审阅 + 创建**”。 验证完成后， **选择创建**。

1. 部署完成后，选择转到 **资源**。

1. 在 **"设置"** 下，选择"**配置"。** 选择 Microsoft **应用** ID 旁边的 **"管理"链接**。

1. 选择“**新客户端密码**”按钮。 添加说明并选择过期，然后选择"添加 **"。**

1. 离开此页前，先复制客户端密码值。 你将在以下步骤中需要它。

    > [!IMPORTANT]
    > 此客户端密码不会再次显示，所以请务必现在就复制它。 你需要在多个位置输入此值，以便保持安全。

1. 在 **左侧** 菜单中选择"概述"。 复制 Application (客户端) **ID** 并保存它，在以下步骤中将需要该值。

    ![新应用注册的应用程序 ID 的屏幕截图](./images/aad-application-id.png)

1. 返回到浏览器中的自动程序通道注册窗口，然后将应用程序 ID 粘贴到 **Microsoft 应用 ID** 字段中。 将客户端密码粘贴到 **"密码"** 字段中。 选择“**确定**”。

1. 在"**机器人频道注册"页上，** 选择"创建 **"。**

1. 等待创建自动程序频道注册。 创建后，返回到 Azure 门户中的主页，然后选择"**自动程序服务"。** 选择新的自动程序频道注册以查看其属性。

## <a name="create-a-web-app-registration"></a>创建 Web 应用注册

1. 返回到 Azure 门户的主页，然后选择 **"Azure Active Directory"。**

1. 选择 **"应用注册"。**

1. 选择“新注册”。 在“注册应用”页上，按如下方式设置值。

    - 将“名称”设置为“`Graph Calendar Bot Auth`”。
    - 将“受支持的帐户类型”设置为“任何组织目录中的帐户和个人 Microsoft 帐户”。
    - 在“重定向 URI”下，将第一个下拉列表设置为“`Web`”，并将值设置为“`https://token.botframework.com/.auth/web/redirect`”。

1. 选择“**注册**”。 在 **"Graph日历** 自动程序身份验证"页上，复制"应用程序 (**客户端) ID"的值并** 保存它，以下步骤中将需要该值。

1. 选择“管理”下的“证书和密码”。 选择“新客户端密码”按钮。 在“**说明**”中输入值，并选择“**过期**”下的一个选项，再选择“**添加**”。

1. 离开此页前，先复制客户端密码值。 你将在以下步骤中需要它。

1. 选择 **"API 权限"，** 然后选择"**添加权限"。**

1. 选择 **"Microsoft Graph"，** 然后选择"**委派权限"。**

1. 选择以下权限，然后选择"**添加权限"。**

    - **openid**
    - **个人资料**
    - **Calendars.ReadWrite**
    - **MailboxSettings.Read**

    ![已配置权限的屏幕截图](images/configured-permissions.png)

### <a name="about-permissions"></a>关于权限

请考虑每个权限范围允许机器人执行哪些操作，以及机器人将使用这些权限范围执行哪些操作。

- **openid** 和 **个人资料**：允许机器人登录用户，并获取标识令牌中 Azure AD 的基本信息。
- **Calendars.ReadWrite**：允许机器人读取用户的日历，以及向用户的日历添加新事件。
- **MailboxSettings.Read**：允许机器人读取用户的邮箱设置。 机器人将使用此密码获取用户的所选时区。
- **User.Read：** 允许机器人从 Microsoft Graph。 机器人将使用此名称获取用户名。

## <a name="add-oauth-connection-to-the-bot"></a>向自动程序添加 OAuth 连接

1. 导航到 Azure 门户中的自动程序 Azure 自动程序页面。 选择 **"配置**"下的 **"设置"。**

1. 选择 **"添加 OAuth 连接设置"。**

1. 按如下所示填写表单，然后选择"保存 **"。**

    - **名称**： `GraphBotAuth`
    - **提供程序**： **Azure Active Directory v2**
    - **客户端 ID：** 你的日历自动程序 **Graph的应用程序** ID。
    - **客户端密码**：你的日历Graph **身份验证注册的客户端** 密码。
    - **令牌Exchange URL：** 保留为空
    - **租户 ID：**`common`
    - **范围**： `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`

1. 选择 OAuth 连接选项 下的 **GraphBotAuth** **设置。**

1. 选择 **"测试连接"。** 这将打开一个新的浏览器窗口或选项卡以启动 OAuth 流。

1. 如有必要，请登录。 查看请求的权限列表，然后选择接受 **。**

1. 应看到"测试 **到'GraphBotAuth'的连接成功"** 消息。

> [!TIP]
> 可以选择此 **页上的"复制** 令牌"按钮，然后将令牌粘贴到 中以查看 [https://jwt.ms](https://jwt.ms) 令牌中的声明。 当解决身份验证错误时，这非常有用。
