---
title: 教程：注册并扫描本地 SQL Server
description: 本教程介绍如何将本地 SQL Server 注册到 Purview，以及如何使用自承载 IR 扫描服务器。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/27/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0054b41fdf12efa4bbd3f1bf34e66023b7ea2d75
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213077"
---
# <a name="tutorial-register-and-scan-an-on-premises-sql-server"></a>教程：注册并扫描本地 SQL Server

Azure Purview 设计用于连接到数据源，以帮助你管理敏感数据、简化数据发现和确保正确使用。 Purview 可以跨整个环境（包括多云和本地）连接到源。 在本场景中，你将使用自承载集成运行时连接到本地 SQL 服务器上的数据。 然后，你将使用 Azure Purview 来扫描数据并进行分类。

本教程介绍以下操作：

> [!div class="checklist"]
> * 登录 Purview Studio。
> * 在 Azure Purview 中创建集合。
> * 创建自我托管的集成运行时。
> * 在 Azure Key Vault 中存储凭据。
> * 将本地 SQL Server 注册到 Purview。
> * 扫描 SQL Server。
> * 浏览数据目录以查看 SQL Server 中的资产。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 有效的 [Azure Key Vault](../key-vault/certificates/quick-create-portal.md#create-a-vault)。
- Azure Purview 帐户。 如果还没有帐户，可以[按照本快速入门指南中的步骤创建帐户](create-catalog-portal.md)。
- [本地 SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)。

## <a name="sign-in-to-purview-studio"></a>登录 Purview Studio

若要与 Purview 进行交互，需通过 Azure 门户连接到 [Purview Studio](https://web.purview.azure.com/resource/)。 在 [Azure 门户](https://portal.azure.com)中转到 Purview 资源，然后在概述页面选择“打开 Purview Studio”磁贴，即可找到 Purview Studio。

:::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/open-purview-studio.png" alt-text="Azure 门户中 Purview 窗口的屏幕截图，其中突出显示了 Purview Studio 按钮。" border="true":::

## <a name="create-a-collection"></a>创建集合

使用 Azure Purview 中的集合，可以将资产和源整理到自定义层次结构中以实现组织和可发现性。 它们也是用于管理 Purview 中访问权限的工具。 在本教程中，我们将创建一个集合来容纳 SQL Server 源及其所有资产。 本教程不介绍为其他用户分配权限的相关信息，因此，有关详细信息，请参阅 [Purview 权限指南](catalog-permissions.md)。

### <a name="check-permissions"></a>检查权限

若要在 Purview 中创建和管理集合，需成为 Purview 中的“集合管理员”。 可以在 [Purview Studio](use-purview-studio.md) 中检查这些权限。

1. 在左侧窗格中选择“数据映射”>“集合”，打开集合管理页。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/find-collections.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并选择了“集合”选项卡。" border="true":::

1. 选择你的根集合。 根集合是集合列表中的顶级集合，与 Purview 资源同名。 在以下示例中，该集合名为 Purview 帐户。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-root-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了根集合。" border="true":::

1. 在集合窗口中选择“角色分配”。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/role-assignments.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了“角色分配”选项卡。" border="true":::

1. 若要创建集合，你需要位于“角色分配”下的“集合管理员”列表中。 如果已创建 Purview 资源，则应该已将你列为根集合下的集合管理员。 否则，你需要联系集合管理员为你授予权限。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/collection-admins.png" alt-text="Purview Studio 窗口的屏幕截图，其中已打开“数据映射”并突出显示了“集合管理员”部分。" border="true":::

### <a name="create-the-collection"></a>创建集合

1. 选择“+ 添加集合”。 同样，只有[集合管理员](#check-permissions)能够管理集合。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/select-add-a-collection.png" alt-text="Purview Studio 窗口的屏幕截图，显示了新的集合窗口，其中突出显示了“添加集合”按钮。" border="true":::

1. 在右侧面板中，输入集合名称和说明。 如果需要，还可以添加用户作为新集合的集合管理员。
1. 选择“创建”。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/create-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中显示了“新建集合”窗口，选择了显示名称和集合管理员，并突出显示了“创建”按钮。" border="true":::

1. 新集合的信息将反映在页面上。

    :::image type="content" source="./media/tutorial-register-scan-on-premises-sql-server/created-collection.png" alt-text="Purview Studio 窗口的屏幕截图，其中显示了新建的集合的窗口。" border="true":::

## <a name="create-a-self-hosted-integration-runtime"></a>创建自承载 Integration Runtime

自承载集成运行时 (SHIR) 是 Purview 用于连接到本地数据源的计算基础结构。 SHIR 下载并安装在与本地数据源相同的网络中的计算机上。

本教程假定你将在其中安装自承载集成运行时的计算机可以与 Internet 建立网络连接。 此连接允许 SHIR 在源和 Azure Purview 之间进行通信。 如果计算机具有受限的防火墙，或你想要保护防火墙，请查看[自承载集成运行时的网络要求](manage-integration-runtimes.md#networking-requirements)。

1. 在 Purview Studio 的“主页”上，从左侧导航窗格中选择“数据映射”。

1. 在左侧窗格中的“源管理”下，选择“集成运行时”，然后选择“+ 新建”  。

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="选择“集成运行时”按钮。":::

1. 在 **集成运行时设置** 页面，选择 **“自承载”** 以创建自承载 IR，然后选择 **“继续”** 。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-self-hosted-ir.png" alt-text="创建新 SHIR。":::

1. 输入 IR 的名称，然后选择“创建”。

1. 在 " **集成运行时设置** " 页上，按照 " **手动设置** " 部分中的步骤进行操作。 必须从下载站点将集成运行时下载到与本地 SQL Server 位于同一网络中的 VM 或计算机上。 有关所需计算机类型的信息，可以参考[管理集成运行时的指南](manage-integration-runtimes.md#prerequisites)。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/integration-runtime-settings.png" alt-text="获取密钥":::

   - 复制并粘贴身份验证密钥。

   - 从 [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) 将自承载集成运行时下载到本地 Windows 计算机上。 运行安装程序。 支持自承载集成运行时版本（例如，5.4.7803.1 和 5.6.7795.1）。 

   - 在 **“注册集成运行时(自承载)”** 页上粘贴前面保存的两个密钥的其中一个密钥，然后选择 **“注册”** 。

     :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/register-integration-runtime.png" alt-text="输入密钥。":::

   - 在“新建 Integration Runtime (自承载)节点”页上，选择“完成”。 

1. 成功注册自承载集成运行时后，会看到以下窗口：

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/successfully-registered.png" alt-text="已成功注册。":::

## <a name="set-up-sql-authentication"></a>设置 SQL 身份验证

为本地 SQL Server 设置身份验证只有一种方法：

- SQL 身份验证

### <a name="sql-authentication"></a>SQL 身份验证

SQL 帐户必须拥有访问 master 数据库的权限。 这是因为 `sys.databases` 位于数据库中。 要查找服务器上的所有 SQL 数据库，Purview 扫描程序需要枚举 `sys.databases`。

#### <a name="create-a-new-login-and-user"></a>创建新登录名和用户

如果要创建新的登录名和用户以扫描 SQL Server，请执行以下步骤：

> [!Note]
> 以下所有步骤均可使用[此处](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql)提供的代码执行。

1. 导航到 SQL Server Management Studio (SSMS)，连接到服务器，导航到“安全性”，选择并按住（或右键单击）“登录名”并创建新登录名。 请确保选择“SQL 身份验证”。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="创建新登录名和用户。":::

1. 在左侧导航栏中选择“服务器角色”，并确保已分配 public 角色。

1. 在左侧导航栏中选择“用户映射”，在“映射”中选择所有数据库，然后选择数据库角色：db_datareader。

1. 选择“确定”进行保存。

1. 选择并按住（或右键单击）“用户”并选择“属性”，再次导航到所创建的用户。 输入新密码并进行确认。 选择“指定旧密码”，然后输入旧密码。 创建新登录名后需要尽快更改密码。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/change-password.png" alt-text="更改密码。":::

#### <a name="create-a-key-vault-credential"></a>创建 Key Vault 凭据

1. 在 Azure 门户中导航到密钥保管库。 选择“设置”>“机密”。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="从左侧菜单中选择“机密”":::

1. 选择“+ 生成/导入”

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-generate-import.png" alt-text="从顶部菜单中选择“生成/导入”。":::

1. 对于上传选项，选择“手动”并在登录 SQL Server 时输入“名称”和“值”作为密码  。 确认“已启用”设置为“是” 。 如果设置了激活和到期日期，请确保当天日期在两者之间，否则将无法使用凭据。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/create-credential-secret.png" alt-text="向密钥保管库凭据添加值。":::

1. 选择“创建”以完成操作。
1. 在 [Azure Purview Studio](#sign-in-to-purview-studio) 中，导航到左侧菜单中的“管理”页。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-management.png" alt-text="在左侧菜单上选择“管理”页。":::

1. 选择“凭据”页。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-credentials.png" alt-text="“管理”页上的“凭据”按钮处于突出显示状态。":::

1. 在“凭据”页中选择“管理密钥保管库连接”。 

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/manage-key-vault-connections.png" alt-text="管理 Azure Key Vault 连接。":::

1. 在“管理密钥保管库连接”页中选择“+ 新建”。

1. 提供所需的信息，然后选择“创建”。

1. 确认密钥保管库已成功关联到你的 Azure Purview 帐户，如以下示例中所示：

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-kv-connections.png" alt-text="查看 Azure 密钥保管库连接以确认。":::

1. 通过选择“+ 新建”为 SQL Server 创建新凭据。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-new.png" alt-text="选择“+ 新建”以创建凭据。":::

1. 提供所需的信息。 选择“身份验证方法”，以及要从中选择机密的 **密钥保管库连接**。

1. 填写所有详细信息后，选择“创建”。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/new-credential.png" alt-text="新建凭据":::

1. 确认新凭据是否显示在列表视图中并随时可供使用。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/view-credentials.png" alt-text="查看凭据":::

## <a name="register-sql-server"></a>注册 SQL Server

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 Purview 帐户，然后在选择“[Purview Studio](#sign-in-to-purview-studio)”。

1. 在左侧导航栏的“源”和“扫描”下，选择“集成运行时”。 确保设置了自承载集成运行时。 如果未设置，请按照[此处](manage-integration-runtimes.md)所述的步骤创建自承载集成运行时，以扫描可以访问本地网络的本地或 Azure VM。

1. 在左侧导航区域中选择“数据映射”。

1. 选择“注册”

1. 选择“SQL Server”，然后选择“继续”

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="设置 SQL 数据源。":::

1. 提供一个友好名称和服务器终结点，然后选择“完成”以注册数据源。 例如，如果你的 SQL Server FQDN 是 foobar.database.windows.net，则输入 foobar 作为服务器终结点。

若要创建并运行新扫描，请执行以下操作：

1. 在 Purview Studio 的左窗格中选择“数据映射”选项卡。

1. 选择已注册的 SQL Server 源。

1. 选择“新建扫描”

1. 选择要连接到数据源的凭据。

1. 通过在列表中选择适当的项，可以将扫描范围限定到特定的表。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="限定扫描范围":::

1. 然后选择扫描规则集。 可以在系统默认项和现有的自定义规则集之间选择，或者可以以内联方式创建新规则集。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="扫描规则集":::

1. 选择扫描触发器。 可以设置一个计划或运行一次扫描。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. 查看扫描并选择“保存并运行”。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此 Purview 或 SQL 源，则可以按照以下步骤操作，删除集成运行时、SQL 凭据和 Purview 资源。

### <a name="remove-shir-from-purview"></a>从 Purview 中删除 SHIR

1. 在 [Purview Studio](https://web.purview.azure.com/resource/) 的“主页”上，从左侧导航窗格中选择“数据映射”。

1. 在左侧窗格的“源管理”下，选择“集成运行时” 。

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-integration-runtime.png" alt-text="选择“集成运行时”按钮。":::

1. 选中集成运行时旁边的复选框，然后选择“删除”按钮。

      :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/delete-integration-runtime.png" alt-text="突出显示的“集成运行时”和“删除”按钮旁边的复选框。":::

1. 在下一个窗口中选择“确认”以确认删除。

1. 此窗口将自行刷新，你将不会在“集成运行时”下看到你的 SHIR。

### <a name="uninstall-self-hosted-integration-runtime"></a>卸载自承载集成运行时

1. 登录到安装自承载集成运行时的计算机。
1. 打开“控制面板”，然后在“卸载程序”下搜索“Microsoft Integration Runtime”

1. 卸载现有集成运行时。

> [!IMPORTANT] 
> 在下面的过程中，选择“是”。 在卸载过程中请勿保留数据。

:::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-yes.png" alt-text="用于从集成运行时删除所有用户数据的“是”按钮的屏幕截图。":::

### <a name="remove-sql-credentials"></a>删除 SQL 凭据

1. 转到 [Azure 门户](https://portal.azure.com)，导航到存储 Purview 凭据的 Key Vault 资源。

1. 在左侧菜单的“设置”下，选择“机密” 

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-secrets.png" alt-text="从 Azure Key Vault 的左侧菜单中选择“机密”。":::

1. 选择为本教程创建的 SQL Server 凭据机密。
1. 选择“删除”

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete-credential.png" alt-text="从 Azure Key Vault 机密的顶部菜单中删除机密。":::

1. 选择“是”以永久删除资源。

### <a name="delete-purview-account"></a>删除 Purview 帐户

如果想在完成本教程后删除 Purview 帐户，请按照以下步骤操作。

1. 转到 [Azure 门户](https://portal.azure.com)，然后导航到 Purview 帐户。

1. 在页面顶部选择“删除”按钮。

   :::image type="content" source="media/tutorial-register-scan-on-premises-sql-server/select-delete.png" alt-text="Azure 门户中“Purview 帐户”页上的“删除”按钮处于选中状态。":::

1. 完成此过程后，会在 Azure 门户中收到通知。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Purview REST API](tutorial-using-rest-apis.md)
