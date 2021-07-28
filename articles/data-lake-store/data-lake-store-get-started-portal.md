---
title: Azure Data Lake Storage Gen1 入门 - 门户
description: 使用 Azure 门户创建 Data Lake Store Gen1 帐户，并在该账户中执行基本操作。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 428ed96c3223e644b0c78712723231a5fabbdc77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578574"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>通过 Azure 门户开始使用 Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

了解如何使用 Azure 门户创建 Data Lake Storage Gen1 帐户并执行基本操作，如创建文件夹、上传和下载数据文件、删除帐户等。有关详细信息，请参阅 [Azure Data Lake Storage Gen1 概述](data-lake-store-overview.md)。

## <a name="prerequisites"></a>先决条件

要阅读本教程，必须具备以下项：

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-a-data-lake-storage-gen1-account"></a>创建 Data Lake Storage Gen1 帐户

1. 登录到新的 [Azure 门户](https://portal.azure.com)。
2. 单击“创建资源”>“存储”>“Data Lake Storage Gen1”。
3. 在“新建 Data Lake Storage Gen1”边栏选项卡中，提供以下屏幕截图中所示的值：

    ![创建新的 Data Lake Storage Gen1 帐户](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "创建新的 Data Lake Storage Gen1 帐户")

   * **名称**。 输入 Data Lake Storage Gen1 帐户的唯一名称。
   * **订阅**。 选择要在其下创建新的 Data Lake Storage Gen1 帐户的订阅。
   * 资源组。 选择现有资源组，或选择“新建”选项创建一个资源组。 资源组是一个容器，包含应用程序的相关资源。 有关详细信息，请参阅 [Azure 中的资源组](../azure-resource-manager/management/overview.md#resource-groups)。
   * **位置**：选择要创建 Data Lake Storage Gen1 帐户的位置。
   * **加密设置**。 有三个选项：

     * 不启用加密。
     * 如果希望 Data Lake Storage Gen1 管理加密密钥，请使用 Data Lake Storage Gen1 管理的密钥。
     * **使用自己的专用密钥保管库的密钥**。 可以选择现有的 Azure Key Vault，也可以创建新的 Key Vault。 若要使用密钥保管库中的密钥，必须为 Data Lake Storage Gen1 帐户分配 Azure Key Vault 访问权限。 有关说明，请参阅[分配对 Azure Key Vault 的权限](#assign-permissions-to-azure-key-vault)。

        ![“新建 Data Lake Storage Gen1”边栏选项卡和“加密设置”边栏选项卡的屏幕截图。](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Storage Gen1 加密")

        在“加密设置”边栏选项卡中单击“确定”。

        有关详细信息，请参阅 [Azure Data Lake Storage Gen1 中的数据加密](./data-lake-store-encryption.md)。

4. 单击“创建”。 如果选择将帐户固定到仪表板，将返回仪表板，在其中可以查看预配 Data Lake Storage Gen1 帐户的进度。 预配 Data Lake Storage Gen1 帐户后，会显示帐户边栏选项卡。

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>分配对 Azure Key Vault 的权限

如果使用 Azure Key Vault 中的密钥为 Data Lake Storage Gen1 帐户配置加密，必须配置 Data Lake Storage Gen1 帐户与 Azure Key Vault 帐户之间的访问权限。 为此，请执行以下步骤。

1. 如果使用了 Azure Key Vault 中的密钥，Data Lake Storage Gen1 帐户的边栏选项卡顶部会显示一条警告。 单击警告打开“加密”。

    ![显示“需要 Key Vault 权限配置”警告的 Data Lake Storage Gen1 帐户边栏选项卡的屏幕截图。单击此处设置。](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Storage Gen1 加密")
2. 该边栏选项卡显示两个用于配置访问权限的选项。

    ![“加密”边栏选项卡的屏幕截图。](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Storage Gen1 加密")

   * 在第一个选项中，单击“授予权限”来配置访问权限。 仅当创建 Data Lake Storage Gen1 帐户的用户也是 Azure Key Vault 的管理员时，才启用第一个选项。
   * 另一个选项用于运行边栏选项卡中显示的 PowerShell cmdlet。 必须是 Azure 密钥保管库的所有者，或者能够授予对 Azure 密钥保管库的权限。 运行该 cmdlet 后，请返回上述边栏选项卡，并单击“启用”配置访问权限。

> [!NOTE]
> 也可以使用 Azure 资源管理器模板创建 Data Lake Storage Gen1 帐户。 可以从 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/?term=data+lake+store)访问这些模板：
> * 不进行数据加密：[Deploy Azure Data Lake Storage Gen1 account with no data encryption](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)（在不进行数据加密的情况下部署 Azure Data Lake Storage Gen1 帐户）。
> * 使用 Data Lake Storage Gen1 进行数据加密：[Deploy Data Lake Storage Gen1 account with encryption(Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)（在进行加密 (Data Lake) 的情况下部署 Data Lake Storage Gen1 帐户）。
> * 使用 Azure Key Vault 进行数据加密：[Deploy Data Lake Storage Gen1 account with encryption(Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)（在进行加密 (Key Vault) 的情况下部署 Data Lake Storage Gen1 帐户）。
>
>

## <a name="create-folders"></a><a name="createfolder"></a>创建文件夹

可以在 Data Lake Storage Gen1 帐户下创建文件夹，用于管理和存储数据。

1. 打开已创建的 Data Lake Storage Gen1 帐户。 在左窗格中单击“所有资源”，然后在“所有资源”边栏选项卡中单击要在其下创建文件夹的帐户名。  如果将帐户固定到了启动板，请单击该帐户磁贴。
2. 在 Data Lake Storage Gen1 帐户边栏选项卡中，单击“数据资源管理器”。

    ![标出“数据资源管理器”选项的 Data Lake Storage Gen1 帐户边栏选项卡的屏幕截图。](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "在 Data Lake Storage Gen1 帐户中创建文件夹")
3. 在“数据资源管理器”边栏选项卡中，单击“新建文件夹”，输入新文件夹的名称，然后单击“确定”。

    ![“数据资源管理器”边栏选项卡的屏幕截图，其中标出了“新建文件夹”选项和“创建新的文件夹”文本框。](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "在 Data Lake Storage Gen1 帐户中创建文件夹")

    新创建的文件夹在“数据资源管理器”边栏选项卡中列出。 可以创建任何级别的嵌套文件夹。

    ![在 Data Lake 帐户中创建文件夹](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "在 Data Lake 帐户中创建文件夹")

## <a name="upload-data"></a><a name="uploaddata"></a>上传数据

可以直接将数据上传到 Data Lake Storage Gen1 帐户的根级别，也可以上传到在帐户中创建的文件夹。

1. 在“数据资源管理器”边栏选项卡中，单击“上传”。
2. 在“上传文件”边栏选项卡中，导航到要上传的文件，然后单击“添加所选文件”。 也可选择多个要上传的文件。

    ![上传数据](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "上传数据")

如果正在查找一些示例数据进行上传，可以从 **Azure Data Lake Git 存储库** 获取 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)文件夹。

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>适用于已存储数据的操作

单击与文件相对应的省略号图标，然后在弹出菜单中单击要对数据执行的操作。

![数据的属性](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "数据的属性")

## <a name="secure-your-data"></a>保护数据

可以通过 Azure Active Directory 和访问控制 (ACL) 来保护 Data Lake Storage Gen1 帐户中存储的数据。 有关如何实现保护的说明，请参阅[保护 Azure Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)。

## <a name="delete-your-account"></a>删除你的帐户

若要删除 Data Lake Storage Gen1 帐户，单击 Data Lake Storage Gen1 边栏选项卡中的“删除”。 系统会提示输入要删除的帐户的名称，确认该操作。 输入帐户的名称，单击“删除” 。

![删除 Data Lake Storage Gen1 帐户](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "删除 Data Lake 帐户")

## <a name="next-steps"></a>后续步骤

* [使用 Azure Data Lake Storage Gen1 满足大数据要求](data-lake-store-data-scenarios.md)
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
* [将 Azure Data Lake Analytics 与 Data Lake Storage Gen1 配合使用](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)
