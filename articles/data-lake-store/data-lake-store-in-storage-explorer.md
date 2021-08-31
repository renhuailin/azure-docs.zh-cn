---
title: 管理 Data Lake Storage Gen1 资源 - Azure 存储资源管理器
description: 了解如何在 Azure 存储资源管理器中访问和管理 Azure Data Lake Storage Gen1 数据和资源
author: jejiang
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/04/2021
ms.author: jejiang
ms.openlocfilehash: 9ef06e1b13141e3b5c342842a63e8e520e3e03e9
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2021
ms.locfileid: "111589825"
---
# <a name="manage-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>使用存储资源管理器管理 Data Lake Storage Gen1 资源

[Azure Data Lake Storage Gen1](./data-lake-store-overview.md) 是一项用于存储大量的非结构化数据（例如文本数据或二进制数据）的服务。 可以通过 HTTP 或 HTTPS 从任何位置访问这些数据。 Azure 存储资源管理器中的 Data Lake Storage Gen1 允许访问和管理 Data Lake Storage Gen1 数据和资源，以及 Blob 和队列之类的其他 Azure 实体。 现在可以使用相同工具在一个位置管理不同 Azure 实体。

另一优势是，不需拥有订阅权限即可管理 Data Lake Storage Gen1 数据。 在存储资源管理器中，可以将 Data Lake Storage Gen1 路径附加到“本地和附加”节点，只要有人授予权限即可。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要符合以下先决条件：

* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial)。
* Data Lake Storage Gen1 帐户。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](./data-lake-store-get-started-portal.md)。

## <a name="install-storage-explorer"></a>安装存储资源管理器

从[产品网页](https://azure.microsoft.com/features/storage-explorer/)安装最新的 Azure 存储资源管理器版本。 此安装支持 Windows、Linux 和 Mac 版本。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

1. 在存储资源管理器中，选择插件图标。

   ![显示插件图标在用户界面中的位置的屏幕截图](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

   此时会打开“连接到 Azure 存储”对话框。
1. 在“选择资源”页上，选择“订阅”。
1. 在“选择 Azure 环境”页上，选择要登录到的 Azure 环境，然后选择“下一步”。
1. 在“登录”对话框中，输入 Azure 凭据，然后选择“下一步”。

1. 在存储资源管理器的“帐户管理”窗格中，选择包含要管理的 Data Lake Storage Gen1 帐户的订阅，然后选择“打开资源管理器”。
1. 在“资源管理器”窗格中，展开订阅。 窗格将更新，并显示所选订阅中的帐户。 这包括任何 Data Lake Storage Gen1 帐户，例如：

     ![显示 Data Lake Storage Gen1 节点中的示例帐户的屏幕截图](./media/data-lake-store-in-storage-explorer/account-list.png)

## <a name="connect-to-data-lake-storage-gen1"></a>连接到 Data Lake Storage Gen1

可以访问订阅中不存在的资源，但前提是有人提供这些资源的 URI。 然后就可以在登录后使用该 URI 连接到 Data Lake Storage Gen1。

1. 打开存储资源管理器。
1. 展开“本地和附加”。
1. 右键单击“Data Lake Storage Gen1 (预览版)”，然后选择“连接到 Data Lake Storage Gen1”。
1. 输入 URI，例如：

      ![显示“连接到 Data Lake Store”对话框的屏幕截图，其中包含用于输入 URI 的文本框](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

   此工具会浏览到刚输入的 URL 的位置。

      ![显示在 UI 的“Data Lake Storage Gen1 (预览版)”节点下列出的 Data Lake Storage Gen1 帐户](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-the-contents-of-a-data-lake-storage-gen1-account"></a>查看 Data Lake Storage Gen1 帐户的内容

Data Lake Storage Gen1 帐户的资源包含文件夹和文件。 以下步骤演示了如何在存储资源管理器中查看 Data Lake Storage Gen1 帐户的内容。

1. 打开存储资源管理器。
1. 展开包含要查看的 Data Lake Storage Gen1 帐户的订阅。
1. 展开“Data Lake Storage Gen1 (预览版)”。
1. 选择要查看的 Data Lake Storage Gen1 帐户。

   主窗格会显示 Data Lake Storage Gen1 帐户的内容。

   ![显示主窗格，其中选择了 Data Lake Storage Gen1 帐户，并显示了帐户中的文件夹列表](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>管理 Data Lake Storage Gen1 中的资源

可通过以下操作管理 Data Lake Storage Gen1 资源：

* 浏览多个 Data Lake Storage Gen1 帐户中的 Data Lake Storage Gen1 资源。  
* 使用连接字符串直接连接到 Data Lake Storage Gen1 并对其进行管理。
* 通过“本地和附加”项下的 ACL 查看他人共享的 Data Lake Storage Gen1 资源。
* 执行文件和文件夹 CRUD 操作：支持递归文件夹和多选文件。
* 将文件夹拖放并添加到进行快速访问的最新位置， 此操作镜像桌面文件资源管理器体验。
* 在存储资源管理器中复制并打开 Data Lake Storage Gen1 超链接，只需单击一下即可。
* 在下方窗格中显示“活动日志”，以便查看活动状态。
* 显示文件夹统计信息和文件属性。

## <a name="manage-resources-in-azure-storage-explorer"></a>在 Azure 存储资源管理器中管理资源

创建 Data Lake Storage Gen1 帐户后，即可：

* 在本地计算机上上传和下载文件夹和文件，以及打开资源。
* 固定到“快速访问”、新建文件夹、复制 URL、全选。
* 复制和粘贴、重命名、删除、获取文件夹统计信息、刷新。

以下项目演示了如何管理 Data Lake Storage Gen1 帐户中的资源。 按照以下步骤执行所需任务。

### <a name="upload-files"></a>上传文件

1. 在主窗格的工具栏上选择“上传”，然后选择“上传文件”。
1. 在“选择要上传的文件”对话框中，选择要上传的文件。
1. 选择“打开”，开始上传。

> [!NOTE]
> 你也可以直接在本地计算机上拖动文件以开始上传。

### <a name="upload-a-folder"></a>上传文件夹

1. 在主窗格的工具栏上选择“上传”，然后选择“上传文件夹”。
1. 在“选择要上传的文件夹”对话框中，选择要上传的文件夹。
1. 选择“选择文件夹”，开始上传。

> [!NOTE]
> 你也可以直接在本地计算机上拖动文件夹以开始上传。

### <a name="download-folders-or-files-to-your-local-computer"></a>将文件夹或文件下载到本地计算机

1. 选择要下载的文件夹或文件。
1. 在主窗格的工具栏上，选择“下载”。 
1. 在“选择用于保存已下载文件的文件夹”对话框中，指定位置和名称。
1. 选择“保存”。

### <a name="open-a-folder-or-file-from-your-local-computer"></a>从本地计算机打开文件夹或文件

1. 选择要打开的文件夹或文件。
1. 在主窗格的工具栏上，选择“打开”。  或者右键单击所选文件夹或文件，然后在快捷菜单中选择“打开”。

可以在文件下载后通过与基础文件类型相关联的应用程序将其打开。 也可以在主窗格中打开文件夹。

### <a name="copy-folders-or-files-to-the-clipboard"></a>将文件夹或文件复制到剪贴板

可以复制 Data Lake Storage Gen1 文件夹或文件，然后将其粘贴到另一 Data Lake Storage Gen1 帐户中。 不支持跨存储类型进行的复制和粘贴操作。 例如，不能将 Data Lake Storage Gen1 文件夹或文件复制粘贴到 Azure Blob 存储中，反之亦然。

1. 选择要复制的文件夹或文件。
1. 在主窗格的工具栏上，选择“复制”。  或者右键单击所选文件夹或文件，然后在快捷菜单中选择“复制”。
1. 在导航窗格中，浏览到另一 Data Lake Storage Gen1 帐户，然后选中以在主窗格中查看它。
1. 在主窗格的工具栏中选择“粘贴”，创建一个副本。 也可以在目标的快捷菜单中选择“粘贴”。

> [!NOTE]
> 复制/粘贴操作的工作原理是，先将文件夹或文件下载到本地计算机，然后再将其上传到目标。 此工具不在后端执行该操作。 大型文件的复制/粘贴操作速度较慢。

### <a name="delete-folders-or-files"></a>删除文件夹或文件

1. 选择要删除的文件夹或文件。
1. 在主窗格的工具栏上，选择“删除”。  或者右键单击所选文件夹或文件，然后在快捷菜单中选择“删除”。
1. 在确认对话框中选择“是”。

### <a name="pin-to-quick-access"></a>固定到“快速访问”

1. 选择要固定的文件夹，以便能够轻松访问资源。
1. 在主窗格的工具栏中，选择“固定到快速访问”。

   在导航窗格中，所选文件夹被添加到“快速访问”节点中。

   ![显示在 UI 的“快速访问”节点下列出的文件夹](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

### <a name="use-deep-links"></a>使用深层链接

如果有 URL，则可在文件资源管理器或浏览器的地址路径中输入该 URL。 然后，存储资源管理器就会自动打开，并转到 URL 的位置。

![显示已复制到“文件资源管理器”窗口的 Data Lake Storage Gen1 帐户中的文件夹的 URL](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>后续步骤

* 查看[最新的存储资源管理器发行说明和视频](https://www.storageexplorer.com)。
* [存储资源管理器入门](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Data Lake Storage Gen1 入门](./data-lake-store-overview.md)
