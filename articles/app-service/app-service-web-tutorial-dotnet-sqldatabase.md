---
title: 教程：将 ASP.NET 应用与 Azure SQL 数据库配合使用
description: 了解如何将 C# ASP.NET 应用部署到 Azure 和 Azure SQL Server 数据库
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/05/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 818ab11d64468a945d85f5b15f6b39dc1664a424
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536223"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>教程：使用 Azure SQL 数据库将 ASP.NET 应用部署到 Azure

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。 本教程演示如何在应用服务中部署数据驱动的 ASP.NET 应用，以及如何将其连接到 [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)。 完成后，你便拥有了一个在 Azure 中运行并连接到 SQL 数据库的 ASP.NET 应用。

![已在 Azure 应用服务中发布 ASP.NET 应用程序](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 在 Azure SQL 数据库中创建数据库
> * 将 ASP.NET 应用连接到 SQL 数据库
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 将日志从 Azure 流式传输到终端
> * 在 Azure 门户中管理应用

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本教程：

安装带有 ASP.NET 和 Web 开发工作负荷的 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>。

如果已安装 Visual Studio，请单击“工具” > **Get Tools and Features**“获取工具和功能”，以便在 Visual Studio 中添加工作负荷。

## <a name="download-the-sample"></a>下载示例

1. [下载示例项目 ](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip)。

1. 提取（解压缩）*dotnet-sqldb-tutorial-master.zip* 文件。

此示例项目包含一个使用 [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) 的基本 [ASP.NET MVC](https://www.asp.net/mvc) 创建-读取-更新-删除 (CRUD) 应用。

### <a name="run-the-app"></a>运行应用

1. 在 Visual Studio 中打开 *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* 文件。

1. 键入 `Ctrl+F5` 在不调试的情况下运行应用。 该应用将显示在默认浏览器中。

1. 选择“新建”链接，创建多个待办事项。

    ![“新建 ASP.NET 项目”对话框](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. 测试“编辑”、“详细信息”和“删除”链接。

该应用使用数据库上下文与数据库进行连接。 在此示例中，数据库上下文使用名为 `MyDbConnection` 的连接字符串。 此连接字符串在 *Web.config* 文件中设置，在 *Models/MyDatabaseContext.cs* 文件中引用。 在教程后面部分，该连接字符串名称用于将 Azure 应用连接到 Azure SQL 数据库。

## <a name="publish-aspnet-application-to-azure"></a>将 ASP.NET 应用程序发布到 Azure

1. 在“解决方案资源管理器”中，右键单击 “DotNetAppSqlDb”项目，然后选择“发布”。

    ![从解决方案资源管理器发布](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. 选择 **Azure** 作为目标，然后单击 **下一步**。

1. 确保已选择 **Azure 应用服务 (Windows)** ，然后单击 **下一步**。

#### <a name="sign-in-and-add-an-app"></a>登录并添加应用

1. 在“发布”对话框中，单击“登录”。

1. 登录到 Azure 订阅。 如果已登录到 Microsoft 帐户，请确保该帐户包含 Azure 订阅。 如果已登录的 Microsoft 帐户不包含 Azure 订阅，请单击该帐户以添加正确的帐户。

1. 在 **应用服务实例** 窗格中，单击 **+** 。

    ![登录 Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>配置 Web 应用名称

可保留生成的 Web 应用名称，或将其更改为另一个唯一名称（有效字符是 `a-z`、`0-9` 和 `-`）。 Web 应用名称将用作应用默认 URL 的一部分（`<app_name>.azurewebsites.net`，其中 `<app_name>` 是 Web 应用的名称）。 在 Azure 的所有应用中，Web 应用名称必须是唯一的。

> [!NOTE]
> 暂时不要选择 **创建**。

![“创建应用服务”对话框](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. 在“资源组”旁边单击“新建”。

   ![在“资源组”旁边单击“新建”。](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. 将资源组命名为 **myResourceGroup**。

#### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. 在 **托管计划** 旁边，单击 **新建**。

1. 在 **配置应用服务计划** 对话框中，使用以下设置配置新的应用服务计划并单击 **确定**：

   | 设置  | 建议的值 | 更多信息 |
   | ----------------- | ------------ | ----|
   |**应用服务计划**| myAppServicePlan | [应用服务计划](../app-service/overview-hosting-plans.md) |
   |**位置**| 西欧 | [Azure 区域](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**大小**| 免费 | [定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![创建应用服务计划](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. 单击 **创建** 并等待创建 Azure 资源。

1. “发布”对话框显示已配置的资源。 单击“完成”。

   ![已创建的资源](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>创建服务器和数据库

在创建数据库时之前，需要[逻辑 SQL Server](../azure-sql/database/logical-servers.md)。 逻辑 SQL 服务器是一种逻辑构造，其中包含一组作为组管理的数据库。

1. 在 **发布** 对话框中，向下滚动到 **服务依赖项** 部分。 单击 **SQL Server 数据库** 旁边的 **配置**。

    > [!NOTE]
    > 请确保从“发布”页面而不是“连接的服务”页面配置 SQL 数据库。

   ![配置 SQL 数据库依赖项](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. 选择 **Azure SQL 数据库**，然后单击 **下一步**。

1. 在 **配置 SQL 数据库** 对话框中，单击 **+** 。

1. 在 **数据库服务器** 旁，单击 **新建**。

   服务器名称用作服务器 `<server_name>.database.windows.net` 的默认 URL 的一部分。 它在 Azure SQL 的所有服务器中必须是唯一的。 将服务器名称更改为所需的值。

1. 添加管理员用户名和密码。 有关密码复杂性要求，请参阅[密码策略](/sql/relational-databases/security/password-policy)。

   请记住此用户名和密码。 以后需要使用此用户名和密码来管理服务器。

   ![创建服务器](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > 虽然连接字符串中的密码已在 Visual Studio 和应用服务中受到屏蔽，但由于它实际上是保留在某个位置，因此增加了应用的受攻击面。 应用服务可以使用[托管服务标识](overview-managed-identity.md)，因此根本不需要将机密保留在代码或应用配置中，这样就消除了上述风险。 有关详细信息，请参阅[后续步骤](#next-steps)。

1. 单击“确定”。

1. 在 **Azure SQL 数据库** 对话框中，保留默认生成的 **数据库名称**。 选择 **创建** 并等待创建数据库资源。

    ![配置数据库](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>配置数据库连接

1. 向导完成创建数据库资源后，单击 **下一步**。

1. 在 **数据库连接字符串名称** 中，键入 _MyDbConnection_。 此名称必须与 _Models/MyDatabaseContext.cs_ 中引用的连接字符串相匹配。

1. 在 **数据库连接用户名** 和 **数据库连接密码** 中，键入在 [创建服务器](#create-a-server-and-database)时使用的管理员用户名和密码。

1. 确保已选择 **Azure 应用设置**，然后单击 **完成**。

    > [!NOTE]
    > 相反，如果看到的是“本地用户机密文件”，则必定已从“连接的服务”页而非“发布”页配置 SQL 数据库。

    ![配置数据库连接字符串](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. 等待配置向导完成，并单击 **关闭**。

#### <a name="deploy-your-aspnet-app"></a>部署 ASP.NET 应用

1. 在 **发布** 选项卡中，向上滚动到顶部，然后单击 **发布**。 将 ASP.NET 应用部署到 Azure 后。 默认浏览器会使用已部署应用的 URL 启动。

1. 添加多个待办事项。

    ![已在 Azure 应用中发布 ASP.NET 应用程序](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    祝贺你！ 数据驱动 ASP.NET 应用程序当前在 Azure 应用服务中实时运行。

## <a name="access-the-database-locally"></a>本地访问数据库

使用 Visual Studio 可在 **SQL Server 对象资源管理器** 中轻松浏览和管理自己在 Azure 的新数据库。 新数据库已经为您创建的应用服务应用打开了防火墙，但若要从本地计算机访问它（例如从 Visual Studio），必须为本地计算机的公共 IP 地址打开防火墙。 如果您的 internet 服务提供商更改了您的公共 IP 地址，则需要重新配置防火墙以再次访问 Azure 数据库。

#### <a name="create-a-database-connection"></a>创建数据库连接

1. 在“视图”菜单中，选择“SQL Server 对象资源管理器”。

1. 在“SQL Server 对象资源管理器”顶部，单击“添加 SQL Server”按钮。

#### <a name="configure-the-database-connection"></a>配置数据库连接

1. 在“连接”对话框中，展开“Azure”节点。 此处列出了 Azure 中的所有 SQL 数据库实例。

1. 选择先前创建的数据库。 底部将自动填充前面创建的连接。

1. 键入先前创建的数据库管理员密码，然后单击“连接”。

    ![通过 Visual Studio 配置数据库连接](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>允许来自你的计算机的客户端连接

此时会打开“新建防火墙规则”对话框。 默认情况下，服务器仅允许从 Azure 服务（例如 Azure 应用）连接到其数据库。 若要从 Azure 外部连接到数据库，请在服务器级别创建防火墙规则。 防火墙规则允许本地计算机的公共 IP 地址。

对话框中已填充了你的计算机的公共 IP 地址。

1. 请确保选中“添加我的客户端 IP”，然后单击“确定”。

    ![创建防火墙规则](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    Visual Studio 成功为 SQL 数据库实例创建防火墙设置后，连接将立即显示在“SQL Server 对象资源管理器”中。

    可在此处执行最常见的数据库操作，如运行查询、创建视图和存储过程等。

1. 展开连接 >“数据库” >  **&lt;你的数据库 >**  > “表”。 右键单击 `Todoes` 表，然后选择“查看数据”。

    ![探索 SQL 数据库对象](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>使用 Code First 迁移更新应用

可以在 Visual Studio 中使用熟悉的工具，更新 Azure 中的数据库和应用。 此步骤中将使用实体框架中的 Code First 迁移对数据库架构进行更改，并将其发布至 Azure。

有关使用 Entity Framework Code First 迁移的详细信息，请参阅[使用 MVC 5 的 Entity Framework 6 Code First 入门](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)。

#### <a name="update-your-data-model"></a>更新数据模型

在代码编辑器中打开 _Models\Todo.cs_。 将以下属性添加到 `ToDo` 类：

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>本地运行 Code First 迁移

运行几个命令来更新本地数据库。

1. 在“工具”菜单中，单击“NuGet 包管理器” > “包管理器控制台”。

1. 在“程序包管理器控制台”窗口中，启用“Code First 迁移”：

    ```powershell
    Enable-Migrations
    ```
    
1. 添加迁移：

    ```powershell
    Add-Migration AddProperty
    ```
    
1. 更新本地数据库：

    ```powershell
    Update-Database
    ```
    
1. 键入 `Ctrl+F5`，以便运行该应用。 测试“编辑”、“详细信息”和“创建”链接。

如果应用程序加载未出错，则 Code First 迁移成功。 但页面看上去仍没有变化，这是因为应用程序逻辑尚未使用新属性。

#### <a name="use-the-new-property"></a>使用新属性

为了使用 `Done` 属性，请对代码做一些更改。 简单起见，本教程中将仅更改 `Index` 和 `Create` 视图，以便在操作过程中查看属性。

1. 打开 _Controllers\TodosController.cs_。

1. 在第 52 行找到 `Create()` 方法，并将 `Done` 添加到 `Bind` 特性中的属性列表。 完成后，`Create()` 方法签名应如下面的代码所示：

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. 打开 _Views\Todos\Create.cshtml_。

1. 在 Razor 代码中，应依次看见使用 `model.Description` 的 `<div class="form-group">` 元素和使用 `model.CreatedDate` 的 `<div class="form-group">` 元素。 紧跟在这两个元素之后，添加另一个使用 `model.Done` 的 `<div class="form-group">` 元素：

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. 打开 _Views\Todos\Index.cshtml_。

1. 搜索空的 `<th></th>` 元素。 在此元素的正上方，添加下列 Razor 代码：

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. 查找包含 `Html.ActionLink()` 帮助器方法的 `<td>` 元素。 在此 `<td>`_上面_，使用以下 Razor 代码添加另一个 `<td>` 元素：

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    这就是要在 `Index` 和 `Create` 视图中查看更改所需的全部操作。

1. 键入 `Ctrl+F5`，以便运行该应用。

你现在可以添加一个待办事项，并检查''Done''。 然后，它应作为已完成项在主页中显示。 请记住，`Edit`视图不显示`Done`字段，因为没有更改`Edit`视图。

#### <a name="enable-code-first-migrations-in-azure"></a>在 Azure 中启用 Code First 迁移

代码更改生效（包括数据库迁移）后，将其发布至 Azure 应用，并使用 Code First 迁移更新 SQL 数据库。

1. 与先前的操作相同，右键单击项目，然后选择“发布”。

1. 单击 **更多操作** > **编辑** 以打开发布设置。

    ![打开发布设置](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. 在 **MyDatabaseContext** 下拉列表中，为 Azure SQL 数据库选择数据库连接。

1. 选择“执行 Code First 迁移(应用程序启动时运行)”，然后单击“保存”。

    ![在 Azure 应用中启用 Code First 迁移](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>发布更改

现已在 Azure 应用中启用了 Code First 迁移，可发布代码更改信息。

1. 在发布页中单击“发布”。

1. 再次尝试添加待办事项并选择“完成”，然后，它们将作为已完成项显示在主页中。

    ![Code First 迁移后的 Azure 应用](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

所有现有待办事项仍将显示。 重新发布 ASP.NET 应用程序时，SQL 数据库中的现有数据不会丢失。 此外，Code First 迁移仅更改数据架构，而使现有数据保持不变。

## <a name="stream-application-logs"></a>流式传输应用程序日志

可直接通过 Azure 应用将跟踪消息流式传输到 Visual Studio。

打开 _Controllers\TodosController.cs_。

每个操作都以 `Trace.WriteLine()` 方法开头。 添加此代码的目的是演示如何将跟踪消息添加至 Azure 应用。

#### <a name="enable-log-streaming"></a>启用日志流式传输

1. 在“视图”菜单中，选择“云资源管理器”。

1. 在 **Cloud Explorer** 中，展开包含应用的 Azure 订阅，并展开 **应用服务**。

1. 右键单击 Azure 应用，并选择“查看流式处理日志”。

    ![启用日志流式传输](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    现在，日志已流式传输到“输出”窗口。

    ![输出窗口中的日志流式传输](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    但还无法查看任何跟踪消息。 因为当首先选择“查看流式处理日志”时，Azure 应用将跟踪级别设置为 `Error`，此级别只记录错误事件（使用 `Trace.TraceError()` 方法）。

#### <a name="change-trace-levels"></a>更改跟踪级别

1. 若要更改跟踪级别以输出其他跟踪消息，请返回 **Cloud Explorer**。

1. 再次右键单击您的应用，然后选择 **在门户中打开**。

1. 在应用的“门户管理”页上，从左侧菜单中选择 **应用服务日志**。

1. 在 **应用程序日志记录（文件系统）** 下，选择 **详细** **级别**。 单击“ **保存**”。

    ![将跟踪级别更改为“详细”](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > 可试验不同的跟踪级别，以查看每个级别分别显示哪些类型的消息。 例如，“信息”级别包括 `Trace.TraceInformation()`、`Trace.TraceWarning()` 和 `Trace.TraceError()` 创建的所有日志，但不包括 `Trace.WriteLine()` 创建的日志。

1. 在浏览器中再次导航到应用（ http://&lt;应用名称>.azurewebsites.net），然后尝试在 Azure 中的待办事项列表应用程序周围单击。 现在，跟踪消息已流式传输到 Visual Studio 中的“输出”窗口。

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>停止日志流式传输

若要停止日志流式传输服务，请单击“输出”窗口中的“停止监视”按钮。

![停止日志流式传输](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>管理 Azure 应用

转到 [Azure 门户](https://portal.azure.com)管理 Web 应用。 搜索并选择“应用服务”。

![搜索 Azure 应用服务](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

选择 Azure 应用名称。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

你已登录到应用页。

默认情况下，门户将显示“概览”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的“应用服务”页](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
>
> * 在 Azure SQL 数据库中创建数据库
> * 将 ASP.NET 应用连接到 SQL 数据库
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 将日志从 Azure 流式传输到终端
> * 在 Azure 门户中管理应用

转到下一教程，了解如何轻松地提高 Azure SQL 数据库连接的安全性。

> [!div class="nextstepaction"]
> [使用适用于 Azure 资源的托管标识安全地访问 SQL 数据库](app-service-web-tutorial-connect-msi.md)

更多资源：

> [!div class="nextstepaction"]
> [配置 ASP.NET 应用](configure-language-dotnet-framework.md)

希望优化并节省云支出？

> [!div class="nextstepaction"]
> [使用成本管理开始分析成本](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)