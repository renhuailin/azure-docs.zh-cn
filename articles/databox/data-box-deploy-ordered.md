---
title: 有关订购 Azure Data Box 的教程 | Microsoft Docs
description: 在本教程中，了解 Azure Data Box（让你能够将本地数据导入 Azure 中的混合解决方案）以及如何订购 Azure Data Box。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/22/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dc06c5646f85fda4b4100805be4a9566d0917e2a
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652985"
---
# <a name="tutorial-order-azure-data-box"></a>教程：订购 Azure Data Box

Azure Data Box 是一个混合解决方案，可用于快速、方便、可靠地将本地数据导入 Azure 中。 请先将数据传输到 Microsoft 提供的 80-TB（可用容量）存储设备，然后再设备寄回。 然后，此数据将上传到 Azure。

本教程介绍如何订购 Azure Data Box。 本教程的介绍内容包括：  

> [!div class="checklist"]
>
> * 部署 Data Box 的先决条件
> * 订购 Data Box
> * 跟踪订单
> * 取消订单

> [!NOTE]
> 若要获取有关 Data Box 订单和发货的常见问题的答案，请参阅 [Data Box 常见问题解答](data-box-faq.yml)。

## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[门户](#tab/portal)

请先完成下述适用于 Data Box 服务和设备的配置先决条件，然后部署设备：

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

可以登录到 Azure，然后采用以下两种方式之一运行 Azure CLI 命令：

* 可以安装 CLI 并在本地运行 CLI 命令。
* 可以在 Azure 门户的 Azure Cloud Shell 中运行 CLI 命令。

在教程中，我们通过 Windows PowerShell 使用 Azure CLI，但你可以自由选择其中任意一种方法。

### <a name="for-azure-cli"></a>对于 Azure CLI

在开始之前，请确保：

#### <a name="install-the-cli-locally"></a>在本地安装 CLI

* 安装 [Azure CLI](/cli/azure/install-azure-cli) 2.0.67 或更高版本。 或者，可以[使用 MSI 安装](https://aka.ms/installazurecliwindows)。

**登录到 Azure**

打开 Windows PowerShell 命令窗口，并使用 [az login](/cli/azure/reference-index#az_login) 命令登录到 Azure：

```azurecli
PS C:\Windows> az login
```

下面是成功登录时的输出：

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**安装 Azure Data Box CLI 扩展**

在使用 Azure Data Box CLI 命令之前，需要安装扩展。 借助 Azure CLI 扩展，可访问尚未在核心 CLI 中提供的试验性和预发布的命令。 有关扩展详细信息，请参阅[使用 Azure CLI 的扩展](/cli/azure/azure-cli-extensions-overview)。

若要安装 Azure Data Box 的扩展，请运行以下命令：`az extension add --name databox`：

```azurecli

    PS C:\Windows> az extension add --name databox
```

如果扩展安装成功，你将看到以下输出：

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

可以通过浏览器使用 [Azure Cloud Shell](https://shell.azure.com/)（一个 Azure 托管的交互式 Shell 环境）来运行 CLI 命令。 Azure Cloud Shell 通过 Azure 服务支持 Bash 或 Windows PowerShell。 已预安装 Azure CLI 并将其配置为与帐户一起使用。 选择 Azure 门户右上角部分菜单上的“Cloud Shell”按钮：

![Cloud Shell 菜单选择](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

该按钮会启动交互式 shell，可以使用它来运行本文中概述的步骤。

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>对于 Azure PowerShell

在开始之前，请确保：

* 安装 Windows PowerShell 6.2.4 或更高版本。
* 安装 Azure PowerShell (AZ) 模块。
* 安装 Azure Data Box (Az.DataBox) 模块。
* 登录 Azure。

#### <a name="install-azure-powershell-and-modules-locally"></a>本地安装 Azure PowerShell 和模块

**安装或升级 Windows PowerShell**

需要安装 Windows PowerShell 6.2.4 或更高版本。 若要查看已安装的 PowerShell 版本，请运行：`$PSVersionTable`。

将显示以下输出：

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

如果你的版本低于 6.2.4，则需要升级 Windows PowerShell 版本。 要安装最新版本的 Windows PowerShell，请参阅[安装 Azure PowerShell](/powershell/scripting/install/installing-powershell)。

**安装 Azure PowerShell 和 Data Box 模块**

需要安装 Azure PowerShell 模块才能使用 Azure PowerShell 订购 Azure Data Box。 安装 Azure PowerShell 模块：

1. 安装 [Azure PowerShell Az 模块](/powershell/azure/new-azureps-module-az)。
2. 然后使用命令 `Install-Module -Name Az.DataBox` 安装 Az.DataBox。

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>登录 Azure

打开 Windows PowerShell 命令窗口，并使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) 命令登录到 Azure：

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

下面是成功登录时的输出：

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

有关如何使用 Windows PowerShell 登录到 Azure 的详细信息，请参阅[使用 Azure PowerShell 进行登录](/powershell/azure/authenticate-azureps)。

---

## <a name="order-data-box"></a>订购 Data Box

# <a name="portal"></a>[门户](#tab/portal)

在 Azure 门户中执行以下步骤以订购设备。

1. 使用 Microsoft Azure 凭据在以下 URL 登录：[https://portal.azure.com](https://portal.azure.com)。
2. 选择“+ 创建资源”并搜索 *Azure Data Box*。 选择“Azure Data Box”。

   ![“新建”部分的屏幕截图，其中搜索字段中有“Azure Data Box”字样](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. 选择“创建”。

   ![Azure Data Box 部分的屏幕截图，其中已标注“创建”选项](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. 检查 Data Box 服务是否在你的区域可用。 输入或选择以下信息，然后选择“应用”。

    |设置  |值  |
    |---------|---------|
    |传输类型     | 选择“导入到 Azure”。        |
    |订阅     | 对于 Data Box 服务，选择一个 EA、CSP 或 Azure 赞助订阅。 <br> 该订阅将链接到计费帐户。       |
    |资源组 | 选择现有资源组。 资源组是可以统一管理或部署的资源的逻辑容器。 |
    |源国家/地区    |    选择数据当前所在的国家/地区。         |
    |目标 Azure 区域     |     选择要在其中传输数据的 Azure 区域。 <br> 有关详细信息，请转到[适用区域](data-box-overview.md#region-availability)。            |

    [ ![启动 Azure Data Box 导入订单](media/data-box-deploy-ordered/select-data-box-import-04-b.png) ](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. 选择“Data Box”。 单次订购的可用最大容量为 80 TB。 可以创建多个订单，以增加数据大小。

    ![可用数据大小：Data Box Disk：40 TB；Data Box：100 TB；Data Box Heavy：1000 TB；寄送自己的磁盘：1 TB](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. 在“订单”中，转到“基本”选项卡 。输入或选择以下信息，然后选择“下一步:数据目标>”。

    |设置  |值  |
    |---------|---------|
    |订阅      | 系统会根据前面所做的选择自动填充此订阅。|
    |资源组    | 之前选择的资源组。 |
    |导入订单名称 | 提供友好名称用于跟踪订单。 <br> 名称可以为 3 到 24 个字符，包括字母、数字和连字符。 <br> 名称必须以字母或数字开头和结尾。    |

    ![Data Box 导入订单向导的“基本信息”屏幕，其中已填写正确信息](media/data-box-deploy-ordered/select-data-box-import-06.png)

7. 在“数据目标”屏幕上，选择“数据目标”（存储帐户或托管磁盘） 。

    如果使用“存储帐户”作为存储目标，则会看到以下屏幕：

    ![Data Box 导入订单向导的“数据目标”屏幕，其中已选择存储帐户](media/data-box-deploy-ordered/select-data-box-import-07.png)

    根据指定的 Azure 区域，从现有存储帐户的筛选列表中选择一个或多个存储帐户。 Data Box 可以与最多 10 个存储帐户链接。 也可以创建新的 **常规用途 v1**、**常规用途 v2** 或 **Blob 存储帐户**。

   > [!NOTE]
   > - 如果选择 Azure Premium FileStorage 帐户，则存储帐户共享上的预配配额将增加到要复制到文件共享的数据的大小。 配额增加后，就不会再进行调整，例如，如果出于某种原因 Data Box 无法复制你的数据。
   > - 此配额用于计费。 将数据上传到数据中心后，应调整配额以满足需求。 有关详细信息，请参阅[了解计费](../../articles/storage/files/understanding-billing.md)。

    支持使用虚拟网络的存储帐户。 若要允许 Data Box 服务使用受保护的存储帐户，请在存储帐户网络防火墙设置中启用受信任的服务。 有关详细信息，请了解如何[将 Azure Data Box 添加为受信任的服务](../storage/common/storage-network-security.md#exceptions)。

    如果使用 Data Box 从本地虚拟硬盘 (VHD) 创建“托管磁盘”，则还需提供以下信息：

    |设置  |值  |
    |---------|---------|
    |资源组     | 若要从本地 VHD 创建托管磁盘，请创建新的资源组。 使用现有资源组的前提是，资源组是在以前由 Data Box 服务为托管磁盘创建 Data Box 订单时创建的。 <br> 指定多个用分号分隔的资源组。 最多支持 10 个资源组。|

    ![Data Box 导入订单向导的“数据目标”屏幕，其中已选择“托管磁盘”](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    为托管磁盘指定的存储帐户用作临时存储帐户。 Data Box 服务将 VHD 作为页 Blob 上传到临时存储帐户，然后将其转换为托管磁盘并移到资源组。 有关详细信息，请参阅[验证 Azure 中的数据上传](data-box-deploy-picked-up.md#verify-data-upload-to-azure)。

   > [!NOTE]
   > 如果页 blob 未成功转换为托管磁盘，它将保留在存储帐户中，你需为存储付费。

8. 在完成时选择“下一步:安全性”以继续操作。

    通过“安全性”屏幕，可使用自己的加密密钥和设备并共享密码，还可选择使用双重加密。

    “安全性”屏幕上的所有设置都是可选的。 如果不更改任何设置，则将应用默认设置。

    ![Data Box 导入订单向导的“安全性”屏幕](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. 如果要使用自己的客户管理的密钥来保护新资源的解锁密钥，请展开“加密类型”。

    可选择性地为 Azure Data Box 配置客户管理的密钥。 默认情况下，Data Box 使用 Microsoft 管理的密钥来保护解锁密钥。

    客户管理的密钥不影响设备上数据的加密方式。 该密钥仅用于加密设备解锁密钥。

    如果不想使用客户管理的密钥，请跳到步骤 15。

   ![显示加密类型设置的“安全性”屏幕](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. 选择“客户管理的密钥”作为密钥类型。 然后，选中“选择密钥保管库和密钥”。
   
    ![安全屏幕，客户管理的密钥的设置](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. 在“从 Azure Key Vault 中选择密钥”边栏选项卡中，订阅会自动填充。

    - 对于“密钥保管库”，可以从下拉列表中选择现有的密钥保管库。

      ![从 Azure Key Vault 屏幕上选择密钥](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - 还可以选择“新建”来创建新的密钥保管库。 在“创建密钥保管库”屏幕上，输入资源组和密钥保管库名称。 确保已启用软删除和清除保护 。 接受其他所有默认值，然后选择“查看 + 创建”。

      ![创建新的 Azure Key Vault 设置](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      查看密钥保管库的信息，然后选择“创建”。 等待几分钟，直到密钥保管库创建完成。

      ![新的 Azure Key Vault 查看屏幕](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. 在“从 Azure Key Vault 选择密钥”中，可选择密钥保管库中的现有密钥。

    ![从 Azure Key Vault 中选择现有密钥](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    如果要创建新的密钥，请选择“新建”。 必须使用 RSA 密钥。 大小可以是 2048 或更大。 输入新密钥的名称，接受其他默认值，然后选择“创建”。

      ![创建新的密钥选项](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      在密钥保管库中创建密钥后，你将收到通知。

13. 选择要使用的密钥的版本，然后选中“选择” 。

      ![在密钥保管库中创建的新密钥](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    如果要创建新的密钥版本，请选择“新建”。

    ![打开用于创建新密钥版本的对话框](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    选择新密钥版本的设置，然后选择“创建”。

    ![创建新的密钥版本](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    “安全性”屏幕上的“加密类型”设置将显示密钥保管库和密钥 。

    ![用于客户管理的密钥的密钥和密钥保管库](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. 选择将用于管理对此资源的访问权限的用户标识。 选中“选择用户标识”。 在右侧面板中，选择要使用的订阅和托管标识。 然后选取“选择”  。

    用户分配的托管标识是一个可用于管理多个资源的独立 Azure 资源。 有关详细信息，请参阅[托管标识类型](../active-directory/managed-identities-azure-resources/overview.md)。  

    如果需要创建新的托管标识，请按照[使用 Azure 门户创建、列出和删除用户分配的托管标识以及如何为其分配角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)中的指南操作。
    
    ![选择用户标识](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    用户标识显示在“加密类型”设置中。

    ![加密类型设置中显示的所选用户标识](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. 如果不想采用 Azure Data Box 默认使用的系统生成的密码，请在“安全性”屏幕上展开“创建自己的密码” 。

    系统生成的密码很安全，建议使用该密码，除非贵组织另有要求。

    ![展开 Data Box 导入订单的“创建自己的密码”选项](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - 要对新设备使用自己的密码，请在“设置设备密码的首选项”中选择“使用自己的密码”，然后键入符合安全要求的密码 。
     
     密码必须是字母和数字，且包含 12 到 15 个字符，至少有一个大写字母、一个小写字母、一个特殊字符和一个数字。 

     - 允许使用的特殊字符：@ # - $ % ^ ! + = ; : _ ( )
     - 不允许使用的字符：I i L o O 0
   
     ![Data Box 导入订单“安全性”屏幕上的“使用自己的设备密码”选项](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - 使用自己的共享密码：

   1. 在“设置共享密码的首选项”中，依次选择“使用自己的密码”和“选择共享密码”  。
     
       ![Data Box 导入订单“安全性”屏幕上的“使用自己的共享密码”选项](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    1. 为订单中的每个存储帐户键入一个密码。 该密码将用于存储帐户的所有共享。
    
       密码必须是字母和数字，且包含 12 到 64 个字符，至少有一个大写字母、一个小写字母、一个特殊字符和一个数字。

       - 允许使用的特殊字符：@ # - $ % ^ ! + = ; : _ ( )
       - 不允许使用的字符：I i L o O 0
     
    1. 要对所有存储帐户使用同一密码，请选择“复制到全部”。 

    1. 完成后，选择“保存”。
     
       ![为 Data Box 导入订单输入共享密码的屏幕](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

    在“安全性”屏幕上，可以使用“查看或更改密码”来更改密码 。

16. 在“安全性”中，如果要启用基于软件的双重加密，请展开“双重加密(适用于高度安全的环境)”，然后选择“为订单启用双重加密”  。

    ![Data Box 导入的安全性屏幕，为 Data Box 订单启用了基于软件的加密](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    除了对 Data Box 上的数据进行 AES-256 位加密，还可执行基于软件的加密。

    > [!NOTE]
    > 启用此选项可能会导致订单处理和数据复制耗时较长。 创建订单后，不能更改此选项。

    在完成时选择“下一步:联系人详细信息”以继续。

17. 在“联系人详细信息”中，选择“+ 添加送货地址” 。

    ![在“联系人详细信息”屏幕中，将寄送地址添加到你的 Azure Data Box 导入订单](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. 在“寄送地址”中，提供你的姓名、公司的名称和邮政地址，以及有效的电话号码。 选择“验证地址”。 服务将验证寄送地址，以确定服务是否在该区域可用。 如果服务在指定的寄送地址可用，则会将结果通知给你。

    ![“添加寄送地址”对话框的屏幕截图，其中标注了“寄送方式”选项和“添加寄送地址”选项。](media/data-box-deploy-ordered/select-data-box-import-10.png)

    如果你选择了自我托管交付，则在成功下单后，你将收到一封电子邮件通知。 有关自托管寄送的详细信息，请参阅[使用自托管寄送](data-box-portal-customer-managed-shipping.md)。

19. 成功验证寄送详细信息后，选择“添加送货地址”。 你将返回“联系人详细信息”选项卡。

20. 返回“联系人详细信息”后，添加一个或多个电子邮件地址。 服务会将有关任何订单状态更新的电子邮件通知发送到指定的电子邮件地址。

    我们建议使用组电子邮件，以便在组中的管理员离任后，可以持续收到通知。

    ![订单向导中“联系人详细信息”的电子邮件部分](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. 查看“查看 + 订购”中与订单、联系人、通知和隐私条款相关的信息。 选中对应于同意隐私条款的复选框。

22. 选择“订单”。 创建订单需要几分钟时间。

    ![订单向导的“审查和订购”屏幕](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 执行以下步骤以订购设备：

1. 记下 Data Box 订单的设置。 这些设置包括你的个人/企业信息、订阅名称、设备信息和寄送信息。 在运行 CLI 命令以创建 Data Box 订单时，需要将这些设置用作参数。 下表显示了用于 `az databox job create` 的参数设置：

   | 设置（参数） | 说明 |  示例值 |
   |---|---|---|
   |resource-group| 使用现有资源组，或创建新的资源组。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |name| 要创建的订单的名称。 | "mydataboxorder"|
   |contact-name| 与寄送地址关联的名称。 | "Gus Poland"|
   |phone| 接收订单的个人或企业的电话号码。| "14255551234"
   |location| 要向你寄送设备的、距你最近的 Azure 区域。| "US West"|
   |sku| 要订购的特定 Data Box 设备。 有效值是："DataBox", "DataBoxDisk", and "DataBoxHeavy"| "DataBox" |
   |email-list| 与订单关联的电子邮件地址。| "gusp@contoso.com" |
   |street-address1| 订单将寄送到的街道地址。 | "15700 NE 39th St" |
   |street-address2| 辅助性地址信息，如公寓号或楼号。 | “123 号建筑” |
   |city| 设备将寄送到的城市。 | "Redmond" |
   |state-or-province| 设备将寄送到的省/市/自治区。| "WA" |
   |country| 设备将寄送到的国家/地区。 | "United States" |
   |postal-code| 与寄送地址相关联的邮政编码。| "98052"|
   |company-name| 你工作的公司的名称。| "Contoso, LTD" |
   |存储帐户| 要从中导入数据的 Azure 存储帐户。| "mystorageaccount"|
   |debug| 在详细日志记录中包含调试信息  | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

2. 在所选的命令提示符或终端中运行 [az databox job create](/cli/azure/databox/job#az_databox_job_create)，以创建 Azure Data Box 订单。

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   以下是命令使用情况的示例：

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   以下是运行命令后得到的输出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. 所有 Azure CLI 命令默认使用 json 作为输出格式，除非进行更改。 可以使用全局参数 `--output <output-format>` 更改输出格式。 将格式更改为“table”可提高输出的可读性。

   下面是刚运行的那个命令，只是稍微更改了格式：

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   以下是运行命令后得到的输出：

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

使用 Azure PowerShell 执行以下步骤以订购设备：

1. 在创建导入订单之前，需要获取存储帐户并将存储帐户对象保存在变量中。

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. 记下 Data Box 订单的设置。 这些设置包括你的个人/企业信息、订阅名称、设备信息和寄送信息。 在运行 PowerShell 命令以创建 Data Box 订单时，需要将这些设置用作参数。 下表显示了用于 [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) 的参数设置。

    | 设置（参数） | 说明 |  示例值 |
    |---|---|---|
    |ResourceGroupName [必填]| 使用现有资源组。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
    |名称[必填]| 要创建的订单的名称。 | "mydataboxorder"|
    |ContactName [必填]| 与寄送地址关联的名称。 | "Gus Poland"|
    |PhoneNumber [必填]| 接收订单的个人或企业的电话号码。| "14255551234"
    |位置[必填]| 要向你寄送设备的、距你最近的 Azure 区域。| “WestUS”|
    |DataBoxType [必填]| 要订购的特定 Data Box 设备。 有效值是："DataBox", "DataBoxDisk", and "DataBoxHeavy"| "DataBox" |
    |EmailId [必填]| 与订单关联的电子邮件地址。| "gusp@contoso.com" |
    |StreetAddress1 [必填]| 订单将寄送到的街道地址。 | "15700 NE 39th St" |
    |StreetAddress2| 辅助性地址信息，如公寓号或楼号。 | “123 号建筑” |
    |StreetAddress3| 三级地址信息。 | |
    |城市[必填]| 设备将寄送到的城市。 | "Redmond" |
    |StateOrProvinceCode [必填]| 设备将寄送到的省/市/自治区。| "WA" |
    |CountryCode [必填]| 设备将寄送到的国家/地区。 | "United States" |
    |PostalCode [必填]| 与寄送地址相关联的邮政编码。| "98052"|
    |CompanyName| 你工作的公司的名称。| "Contoso, LTD" |
    |StorageAccountResourceId [必填]| 要从中导入数据的 Azure 存储帐户 ID。| <AzStorageAccount>.id |

3. 在所选的命令提示符或终端中，使用 [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) 创建 Azure Data Box 订单。

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   以下是运行命令后得到的输出：

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>跟踪订单

# <a name="portal"></a>[门户](#tab/portal)

下单后，可以从 Azure 门户跟踪订单状态。 转到你的 Data Box 订单，然后转到“概述”来查看状态。 门户中会显示订单处于“已订购”状态。

如果设备缺货，你会收到通知。 如果设备有货，Microsoft 会确定要发货的设备，并准备发货。 在设备准备期间，会执行以下操作：

* 将为与设备关联的每个存储帐户创建 SMB 共享。
* 将为每个共享生成访问凭据（例如用户名和密码）。
* 也会生成用于解锁设备的设备密码。
* 锁定 Data Box 的目的是在任何时候防止对设备进行未经授权的访问。

设备准备完成后，门户会显示订单处于“已处理”状态。

![已处理的 Data Box 订单](media/data-box-overview/data-box-order-status-processed.png)

然后，Microsoft 会安排区域承运人发运设备。 设备发货后，你会收到跟踪号。 门户会显示订单处于“已发运”状态。

![已发运的 Data Box 订单](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>跟踪单个订单

若要获取单个现有 Azure Data Box 订单的跟踪信息，请运行 [`az databox job show`](/cli/azure/databox/job#az_databox_job_show)。 该命令显示有关订单的信息，例如（但不限于）：名称、资源组、跟踪信息、订阅 ID、联系人信息、寄送类型和设备 sku。

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   下表显示 `az databox job show` 的参数信息：

   | 参数 | 说明 |  示例值 |
   |---|---|---|
   |resource-group [必填]| 与订单关联的资源组的名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |名称[必填]| 要显示的订单的名称。 | "mydataboxorder"|
   |debug| 在详细日志记录中包含调试信息 | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

   以下是输出格式设置为“table”的命令示例：

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   以下是运行命令后得到的输出：

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> 可以在订阅级别支持列出订单，这使资源组成为可选参数（而不是必需参数）。

### <a name="list-all-orders"></a>列出所有订单

如果订购了多台设备，可以运行 [`az databox job list`](/cli/azure/databox/job#az_databox_job_list) 查看所有 Azure Data Box 订单。 该命令列出属于特定资源组的所有订单。 输出中还显示：订单名称、寄送状态、Azure 区域、交付类型、订单状态。 已取消的订单也包含在列表中。
此命令还显示每个订单的时间戳。

```azurecli
az databox job list --resource-group <resource-group>
```

下表显示 `az databox job list` 的参数信息：

   | 参数 | 说明 |  示例值 |
   |---|---|---|
   |resource-group [必填]| 包含该订单的资源组名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |debug| 在详细日志记录中包含调试信息 | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

   以下是输出格式设置为“table”的命令示例：

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   以下是运行命令后得到的输出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>跟踪单个订单

若要获取单个现有 Azure Data Box 订单的跟踪信息，请运行 [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob)。 该命令显示有关订单的信息，例如（但不限于）：名称、资源组、跟踪信息、订阅 ID、联系人信息、寄送类型和设备 sku。

> [!NOTE]
> `Get-AzDataBoxJob` 用于显示单个和多个订单。 不同之处在于，为单个订单指定了订单名称。

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   下表显示 `Get-AzDataBoxJob` 的参数信息：

   | 参数 | 说明 |  示例值 |
   |---|---|---|
   |ResourceGroup [必填]| 与订单关联的资源组的名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |名称[必填]| 要获取其信息的订单名称。 | "mydataboxorder"|
   |ResourceId| 与订单关联的资源的 ID。 |  |

   下面是包含输出的命令示例：

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   以下是运行命令后得到的输出：

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>列出所有订单

如果订购了多台设备，可以运行 [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) 查看所有 Azure Data Box 订单。 该命令列出属于特定资源组的所有订单。 输出中还显示：订单名称、寄送状态、Azure 区域、交付类型、订单状态。 已取消的订单也包含在列表中。
此命令还显示每个订单的时间戳。

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

下面是该命令的示例：

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

以下是运行命令后得到的输出：

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>取消订单

# <a name="portal"></a>[门户](#tab/portal)

若要取消此订单，请在 Azure 门户中转到“概览”，然后在命令栏中选择“取消” 。

下单后，只要订单状态尚未标记为“已处理”，就可以随时取消订单。

若要删除已取消的订单，请转到“概况”，然后在命令栏中选择“删除” 。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>取消订单

若要取消 Azure Data Box 订单，请运行 [`az databox job cancel`](/cli/azure/databox/job#az_databox_job_cancel)。 你需要指定取消订单的原因。

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   下表显示 `az databox job cancel` 的参数信息：

   | 参数 | 说明 |  示例值 |
   |---|---|---|
   |resource-group [必填]| 与要删除的订单关联的资源组的名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |名称[必填]| 要删除的订单的名称。 | "mydataboxorder"|
   |原因[必填]| 取消订单的原因。 | “我输入了错误信息，需要取消订单。” |
   |是| 不提示确认。 | --yes (-y)| 
   |debug| 在详细日志记录中包含调试信息 | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

   下面是包含输出的命令示例：

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   以下是运行命令后得到的输出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>删除订单

如果取消了 Azure Data Box 订单，可以运行 [`az databox job delete`](/cli/azure/databox/job#az_databox_job_delete) 删除订单。

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   下表显示 `az databox job delete` 的参数信息：

   | 参数 | 说明 |  示例值 |
   |---|---|---|
   |resource-group [必填]| 与要删除的订单关联的资源组的名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |名称[必填]| 要删除的订单的名称。 | "mydataboxorder"|
   |订阅| Azure 订阅的名称或 ID (GUID)。 | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |是| 不提示确认。 | --yes (-y)|
   |debug| 在详细日志记录中包含调试信息 | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

下面是包含输出的命令示例：

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   以下是运行命令后得到的输出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>取消订单

若要取消 Azure Data Box 订单，请运行 [Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob)。 你需要指定取消订单的原因。

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

下表显示 `Stop-AzDataBoxJob` 的参数信息：

| 参数 | 说明 |  示例值 |
|---|---|---|
|ResourceGroup [必填]| 与要取消的订单关联的资源组的名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
|名称[必填]| 要删除的订单的名称。 | "mydataboxorder"|
|原因[必填]| 取消订单的原因。 | “我输入了错误信息，需要取消订单。” |
|Force | 强制 cmdlet 在未经用户确认的情况下运行。 | -Force |

下面是包含输出的命令示例：

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

以下是运行命令后得到的输出：

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>删除订单

如果取消了 Azure Data Box 订单，可以运行 [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) 删除订单。

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

下表显示 `Remove-AzDataBoxJob` 的参数信息：

| 参数 | 说明 |  示例值 |
|---|---|---|
|ResourceGroup [必填]| 与要删除的订单关联的资源组的名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
|名称[必填]| 要删除的订单的名称。 | "mydataboxorder"|
|Force | 强制 cmdlet 在未经用户确认的情况下运行。 | -Force |

下面是包含输出的命令示例：

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

以下是运行命令后得到的输出：

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的文章，例如：

> [!div class="checklist"]
>
> * 部署 Data Box 的先决条件
> * 订购 Data Box
> * 跟踪订单
> * 取消订单

请继续学习下一篇教程，了解如何设置 Data Box。

> [!div class="nextstepaction"]
> [设置 Azure Data Box](./data-box-deploy-set-up.md)
