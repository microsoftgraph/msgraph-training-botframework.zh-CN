---
ms.openlocfilehash: 883eff2860fe2555cdd816cb942a4a87918a5836
ms.sourcegitcommit: 59d94851101b121dc89c0f6ccf3b923e35d8efe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "53446825"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="444bb-101">在此练习中，你将使用 Azure 门户创建新的自动程序通道注册和 Azure AD Web 应用程序注册。</span><span class="sxs-lookup"><span data-stu-id="444bb-101">In this exercise, you will create a new Bot Channels registration and an Azure AD web application registration using the Azure Portal.</span></span>

## <a name="create-a-bot-channels-registration"></a><span data-ttu-id="444bb-102">创建自动程序频道注册</span><span class="sxs-lookup"><span data-stu-id="444bb-102">Create a Bot Channels registration</span></span>

1. <span data-ttu-id="444bb-103">打开浏览器并导航到 [Azure 门户](https://portal.azure.com)。</span><span class="sxs-lookup"><span data-stu-id="444bb-103">Open a browser and navigate to the [Azure Portal](https://portal.azure.com).</span></span> <span data-ttu-id="444bb-104">使用与 Azure 订阅关联的帐户登录。</span><span class="sxs-lookup"><span data-stu-id="444bb-104">Login using the account associated with your Azure subscription.</span></span>

1. <span data-ttu-id="444bb-105">选择左上角的菜单，然后选择创建 **资源**。</span><span class="sxs-lookup"><span data-stu-id="444bb-105">Select the upper-left menu, then select **Create a resource**.</span></span>

    ![Azure 门户菜单的屏幕截图](images/create-resource.png)

1. <span data-ttu-id="444bb-107">在"**新建"** 页面上，搜索 `Azure Bot` 并选择 **"Azure 自动程序"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-107">On the **New** page, search for `Azure Bot` and select **Azure Bot**.</span></span>

1. <span data-ttu-id="444bb-108">在 **"Azure 自动程序"** 页面上，选择"创建 **"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-108">On the **Azure Bot** page, select **Create**.</span></span>

1. <span data-ttu-id="444bb-109">填写必填字段，将 **消息终结点留空** 。</span><span class="sxs-lookup"><span data-stu-id="444bb-109">Fill in the required fields, and leave **Messaging endpoint** blank.</span></span> <span data-ttu-id="444bb-110">自动 **程序句柄** 字段必须是唯一的。</span><span class="sxs-lookup"><span data-stu-id="444bb-110">The **Bot handle** field must be unique.</span></span> <span data-ttu-id="444bb-111">请务必查看不同的定价层，并选择适合你的方案的情况。</span><span class="sxs-lookup"><span data-stu-id="444bb-111">Be sure to review the different pricing tiers and select what makes sense for your scenario.</span></span> <span data-ttu-id="444bb-112">如果这只是一个学习练习，你可能想要选择免费选项。</span><span class="sxs-lookup"><span data-stu-id="444bb-112">If this is just a learning exercise, you may want to select the free option.</span></span>

1. <span data-ttu-id="444bb-113">对于 **"Microsoft 应用 ID"，\*\*\*\*选择"创建新的 Microsoft 应用 ID"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-113">For **Microsoft App ID**, select **Create new Microsoft App ID**.</span></span>

1. <span data-ttu-id="444bb-114">然后“**审阅 + 创建**”。</span><span class="sxs-lookup"><span data-stu-id="444bb-114">Select **Review + create**.</span></span> <span data-ttu-id="444bb-115">验证完成后， **选择创建**。</span><span class="sxs-lookup"><span data-stu-id="444bb-115">Once validation completes, select **Create**.</span></span>

1. <span data-ttu-id="444bb-116">部署完成后，选择转到 **资源**。</span><span class="sxs-lookup"><span data-stu-id="444bb-116">Once deployment has finished, select **Go to resource**.</span></span>

1. <span data-ttu-id="444bb-117">在 **"设置"** 下，选择"**配置"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-117">Under **Settings**, select **Configuration**.</span></span> <span data-ttu-id="444bb-118">选择 Microsoft **应用** ID 旁边的 **"管理"链接**。</span><span class="sxs-lookup"><span data-stu-id="444bb-118">Select the **Manage** link next to **Microsoft App ID**.</span></span>

1. <span data-ttu-id="444bb-119">选择“**新客户端密码**”按钮。</span><span class="sxs-lookup"><span data-stu-id="444bb-119">Select **New client secret**.</span></span> <span data-ttu-id="444bb-120">添加说明并选择过期，然后选择"添加 **"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-120">Add a description and choose an expiration, then select **Add**.</span></span>

1. <span data-ttu-id="444bb-121">离开此页前，先复制客户端密码值。</span><span class="sxs-lookup"><span data-stu-id="444bb-121">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="444bb-122">你将在以下步骤中需要它。</span><span class="sxs-lookup"><span data-stu-id="444bb-122">You will need it in the following steps.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="444bb-123">此客户端密码不会再次显示，所以请务必现在就复制它。</span><span class="sxs-lookup"><span data-stu-id="444bb-123">This client secret is never shown again, so make sure you copy it now.</span></span> <span data-ttu-id="444bb-124">你需要在多个位置输入此值，以便保持安全。</span><span class="sxs-lookup"><span data-stu-id="444bb-124">You will need to enter this value in multiple places so keep it safe.</span></span>

1. <span data-ttu-id="444bb-125">在 **左侧** 菜单中选择"概述"。</span><span class="sxs-lookup"><span data-stu-id="444bb-125">Select **Overview** in the left-hand menu.</span></span> <span data-ttu-id="444bb-126">复制 Application (客户端) **ID** 并保存它，在以下步骤中将需要该值。</span><span class="sxs-lookup"><span data-stu-id="444bb-126">Copy the value of the **Application (client) ID** and save it, you will need it in the following steps.</span></span>

    ![新应用注册的应用程序 ID 的屏幕截图](./images/aad-application-id.png)

1. <span data-ttu-id="444bb-128">返回到浏览器中的自动程序通道注册窗口，然后将应用程序 ID 粘贴到 **Microsoft 应用 ID** 字段中。</span><span class="sxs-lookup"><span data-stu-id="444bb-128">Return to the Bot Channel Registration window in your browser, and paste the application ID into the **Microsoft App ID** field.</span></span> <span data-ttu-id="444bb-129">将客户端密码粘贴到 **"密码"** 字段中。</span><span class="sxs-lookup"><span data-stu-id="444bb-129">Paste your client secret into the **Password** field.</span></span> <span data-ttu-id="444bb-130">选择“**确定**”。</span><span class="sxs-lookup"><span data-stu-id="444bb-130">Select **OK**.</span></span>

1. <span data-ttu-id="444bb-131">在"**机器人频道注册"页上，** 选择"创建 **"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-131">On the **Bots Channels Registration** page, select **Create**.</span></span>

1. <span data-ttu-id="444bb-132">等待创建自动程序频道注册。</span><span class="sxs-lookup"><span data-stu-id="444bb-132">Wait for the Bot Channels registration to be created.</span></span> <span data-ttu-id="444bb-133">创建后，返回到 Azure 门户中的主页，然后选择"**自动程序服务"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-133">Once created, return to the Home page in the Azure Portal, then select **Bot Services**.</span></span> <span data-ttu-id="444bb-134">选择新的自动程序频道注册以查看其属性。</span><span class="sxs-lookup"><span data-stu-id="444bb-134">Select your new Bots Channel registration to view its properties.</span></span>

## <a name="create-a-web-app-registration"></a><span data-ttu-id="444bb-135">创建 Web 应用注册</span><span class="sxs-lookup"><span data-stu-id="444bb-135">Create a web app registration</span></span>

1. <span data-ttu-id="444bb-136">返回到 Azure 门户的主页，然后选择 **"Azure Active Directory"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-136">Return to the home page of the Azure portal, then select **Azure Active Directory**.</span></span>

1. <span data-ttu-id="444bb-137">选择 **"应用注册"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-137">Select **App registrations**.</span></span>

1. <span data-ttu-id="444bb-138">选择“新注册”。</span><span class="sxs-lookup"><span data-stu-id="444bb-138">Select **New registration**.</span></span> <span data-ttu-id="444bb-139">在“注册应用”页上，按如下方式设置值。</span><span class="sxs-lookup"><span data-stu-id="444bb-139">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="444bb-140">将“名称”设置为“`Graph Calendar Bot Auth`”。</span><span class="sxs-lookup"><span data-stu-id="444bb-140">Set **Name** to `Graph Calendar Bot Auth`.</span></span>
    - <span data-ttu-id="444bb-141">将“受支持的帐户类型”设置为“任何组织目录中的帐户和个人 Microsoft 帐户”。</span><span class="sxs-lookup"><span data-stu-id="444bb-141">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="444bb-142">在“重定向 URI”下，将第一个下拉列表设置为“`Web`”，并将值设置为“`https://token.botframework.com/.auth/web/redirect`”。</span><span class="sxs-lookup"><span data-stu-id="444bb-142">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `https://token.botframework.com/.auth/web/redirect`.</span></span>

1. <span data-ttu-id="444bb-143">选择“**注册**”。</span><span class="sxs-lookup"><span data-stu-id="444bb-143">Select **Register**.</span></span> <span data-ttu-id="444bb-144">在 **"Graph日历** 自动程序身份验证"页上，复制"应用程序 (**客户端) ID"的值并** 保存它，以下步骤中将需要该值。</span><span class="sxs-lookup"><span data-stu-id="444bb-144">On the **Graph Calendar Bot Auth** page, copy the value of the **Application (client) ID** and save it, you will need it in the following steps.</span></span>

1. <span data-ttu-id="444bb-145">选择“管理”下的“证书和密码”。</span><span class="sxs-lookup"><span data-stu-id="444bb-145">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="444bb-146">选择“新客户端密码”按钮。</span><span class="sxs-lookup"><span data-stu-id="444bb-146">Select the **New client secret** button.</span></span> <span data-ttu-id="444bb-147">在“**说明**”中输入值，并选择“**过期**”下的一个选项，再选择“**添加**”。</span><span class="sxs-lookup"><span data-stu-id="444bb-147">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

1. <span data-ttu-id="444bb-148">离开此页前，先复制客户端密码值。</span><span class="sxs-lookup"><span data-stu-id="444bb-148">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="444bb-149">你将在以下步骤中需要它。</span><span class="sxs-lookup"><span data-stu-id="444bb-149">You will need it in the following steps.</span></span>

1. <span data-ttu-id="444bb-150">选择 **"API 权限"，** 然后选择"**添加权限"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-150">Select **API permissions**, then select **Add a permission**.</span></span>

1. <span data-ttu-id="444bb-151">选择 **"Microsoft Graph"，** 然后选择"**委派权限"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-151">Select **Microsoft Graph**, then select **Delegated permissions**.</span></span>

1. <span data-ttu-id="444bb-152">选择以下权限，然后选择"**添加权限"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-152">Select the following permissions, then select **Add permissions**.</span></span>

    - <span data-ttu-id="444bb-153">**openid**</span><span class="sxs-lookup"><span data-stu-id="444bb-153">**openid**</span></span>
    - <span data-ttu-id="444bb-154">**个人资料**</span><span class="sxs-lookup"><span data-stu-id="444bb-154">**profile**</span></span>
    - <span data-ttu-id="444bb-155">**Calendars.ReadWrite**</span><span class="sxs-lookup"><span data-stu-id="444bb-155">**Calendars.ReadWrite**</span></span>
    - <span data-ttu-id="444bb-156">**MailboxSettings.Read**</span><span class="sxs-lookup"><span data-stu-id="444bb-156">**MailboxSettings.Read**</span></span>

    ![已配置权限的屏幕截图](images/configured-permissions.png)

### <a name="about-permissions"></a><span data-ttu-id="444bb-158">关于权限</span><span class="sxs-lookup"><span data-stu-id="444bb-158">About permissions</span></span>

<span data-ttu-id="444bb-159">请考虑每个权限范围允许机器人执行哪些操作，以及机器人将使用这些权限范围执行哪些操作。</span><span class="sxs-lookup"><span data-stu-id="444bb-159">Consider what each of those permission scopes allows the bot to do, and what the bot will use them for.</span></span>

- <span data-ttu-id="444bb-160">**openid** 和 **个人资料**：允许机器人登录用户，并获取标识令牌中 Azure AD 的基本信息。</span><span class="sxs-lookup"><span data-stu-id="444bb-160">**openid** and **profile**: allows the bot to sign users in and get basic information from Azure AD in the identity token.</span></span>
- <span data-ttu-id="444bb-161">**Calendars.ReadWrite**：允许机器人读取用户的日历，以及向用户的日历添加新事件。</span><span class="sxs-lookup"><span data-stu-id="444bb-161">**Calendars.ReadWrite**: allows the bot to read the user's calendar and to add new events to the user's calendar.</span></span>
- <span data-ttu-id="444bb-162">**MailboxSettings.Read**：允许机器人读取用户的邮箱设置。</span><span class="sxs-lookup"><span data-stu-id="444bb-162">**MailboxSettings.Read**: allows the bot to read the user's mailbox settings.</span></span> <span data-ttu-id="444bb-163">机器人将使用此密码获取用户的所选时区。</span><span class="sxs-lookup"><span data-stu-id="444bb-163">The bot will use this to get the user's selected time zone.</span></span>
- <span data-ttu-id="444bb-164">**User.Read：** 允许机器人从 Microsoft Graph。</span><span class="sxs-lookup"><span data-stu-id="444bb-164">**User.Read**: allows the bot to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="444bb-165">机器人将使用此名称获取用户名。</span><span class="sxs-lookup"><span data-stu-id="444bb-165">The bot will use this to get the user's name.</span></span>

## <a name="add-oauth-connection-to-the-bot"></a><span data-ttu-id="444bb-166">向自动程序添加 OAuth 连接</span><span class="sxs-lookup"><span data-stu-id="444bb-166">Add OAuth connection to the bot</span></span>

1. <span data-ttu-id="444bb-167">导航到 Azure 门户中的自动程序 Azure 自动程序页面。</span><span class="sxs-lookup"><span data-stu-id="444bb-167">Navigate to your bot's Azure Bot page in the Azure Portal.</span></span> <span data-ttu-id="444bb-168">选择 **"配置**"下的 **"设置"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-168">Select **Configuration** under **Settings**.</span></span>

1. <span data-ttu-id="444bb-169">选择 **"添加 OAuth 连接设置"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-169">Select **Add OAuth Connection Settings**.</span></span>

1. <span data-ttu-id="444bb-170">按如下所示填写表单，然后选择"保存 **"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-170">Fill in the form as follows, then select **Save**.</span></span>

    - <span data-ttu-id="444bb-171">**名称**： `GraphBotAuth`</span><span class="sxs-lookup"><span data-stu-id="444bb-171">**Name**: `GraphBotAuth`</span></span>
    - <span data-ttu-id="444bb-172">**提供程序**： **Azure Active Directory v2**</span><span class="sxs-lookup"><span data-stu-id="444bb-172">**Provider**: **Azure Active Directory v2**</span></span>
    - <span data-ttu-id="444bb-173">**客户端 ID：** 你的日历自动程序 **Graph的应用程序** ID。</span><span class="sxs-lookup"><span data-stu-id="444bb-173">**Client id**: The application ID of your **Graph Calendar Bot Auth** registration.</span></span>
    - <span data-ttu-id="444bb-174">**客户端密码**：你的日历Graph **身份验证注册的客户端** 密码。</span><span class="sxs-lookup"><span data-stu-id="444bb-174">**Client secret**: The client secret of your **Graph Calendar Bot Auth** registration.</span></span>
    - <span data-ttu-id="444bb-175">**令牌Exchange URL：** 保留为空</span><span class="sxs-lookup"><span data-stu-id="444bb-175">**Token Exchange URL**: Leave blank</span></span>
    - <span data-ttu-id="444bb-176">**租户 ID：**`common`</span><span class="sxs-lookup"><span data-stu-id="444bb-176">**Tenant ID**: `common`</span></span>
    - <span data-ttu-id="444bb-177">**范围**： `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`</span><span class="sxs-lookup"><span data-stu-id="444bb-177">**Scopes**: `openid profile Calendars.ReadWrite MailboxSettings.Read User.Read`</span></span>

1. <span data-ttu-id="444bb-178">选择 OAuth 连接选项 下的 **GraphBotAuth** **设置。**</span><span class="sxs-lookup"><span data-stu-id="444bb-178">Select the **GraphBotAuth** entry under **OAuth Connection Settings**.</span></span>

1. <span data-ttu-id="444bb-179">选择 **"测试连接"。**</span><span class="sxs-lookup"><span data-stu-id="444bb-179">Select **Test Connection**.</span></span> <span data-ttu-id="444bb-180">这将打开一个新的浏览器窗口或选项卡以启动 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="444bb-180">This opens a new browser window or tab to start the OAuth flow.</span></span>

1. <span data-ttu-id="444bb-181">如有必要，请登录。</span><span class="sxs-lookup"><span data-stu-id="444bb-181">If necessary, sign in.</span></span> <span data-ttu-id="444bb-182">查看请求的权限列表，然后选择接受 **。**</span><span class="sxs-lookup"><span data-stu-id="444bb-182">Review the list of requested permissions, then select **Accept**.</span></span>

1. <span data-ttu-id="444bb-183">应看到"测试 **到'GraphBotAuth'的连接成功"** 消息。</span><span class="sxs-lookup"><span data-stu-id="444bb-183">You should see a **Test Connection to 'GraphBotAuth' Succeeded** message.</span></span>

> [!TIP]
> <span data-ttu-id="444bb-184">可以选择此 **页上的"复制** 令牌"按钮，然后将令牌粘贴到 中以查看 [https://jwt.ms](https://jwt.ms) 令牌中的声明。</span><span class="sxs-lookup"><span data-stu-id="444bb-184">You can select the **Copy Token** button on this page, and paste the token into [https://jwt.ms](https://jwt.ms) to see the claims inside the token.</span></span> <span data-ttu-id="444bb-185">当解决身份验证错误时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="444bb-185">This is useful when troubleshooting authentication errors.</span></span>
