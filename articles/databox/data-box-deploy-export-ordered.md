---
title: 从 Azure Data Box 导出数据的教程 | Microsoft Docs
description: 了解部署先决条件以及如何从 Azure Data Box 导出数据
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.custom: contperf-fy22q1
ms.openlocfilehash: 298d935b9b673e0b77bdd3e66cc3d348e2d52a07
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361534"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>教程：创建 Azure Data Box 的导出命令

Azure Data Box 是一种混合解决方案，可让将 Azure 中的数据移到用户的位置。 本教程介绍了如何创建 Azure Data Box 的导出命令。 创建导出命令的主要原因是为了进行灾难恢复，以防本地存储区受损以致于需要还原备份。

本教程的介绍内容包括：

> [!div class="checklist"]
>
> * 导出的先决条件
> * Data Box 的导出命令
> * 跟踪导出命令
> * 取消导出命令

## <a name="prerequisites"></a>先决条件

请先完成下述适用于 Data Box 服务和设备的配置先决条件，然后部署设备：

### <a name="for-service"></a>对于服务

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* 请确保现有的资源组可以与 Azure Data Box 一同使用。

* 请确保要从中导出数据的 Azure 存储帐户是受支持的存储帐户类型中的一种，如“[Data Box 受支持的存储帐户](data-box-system-requirements.md#supported-storage-accounts)”所述。

### <a name="for-device"></a>对于设备

在开始之前，请确保：

* 应该将一个主机连接到数据中心网络。 将 Azure Data Box 中的数据复制到此计算机。 主机必须按照 [Azure Data Box 系统要求](data-box-system-requirements.md)中的说明运行支持的操作系统。

* 数据中心需要有高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，则可使用 1-GbE 数据链路，但复制速度会受影响。

## <a name="order-data-box-for-export"></a>Data Box 的导出命令

在 Azure 门户中执行以下步骤以订购设备。

1. 使用 Microsoft Azure 凭据在以下 URL 登录：[https://portal.azure.com](https://portal.azure.com)。

2. 选择“+ 创建资源”并搜索 *Azure Data Box*。 选择“Azure Data Box”。

   ![创建资源](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. 选择“创建”。

   ![创建 Azure Data Box 资源](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. 检查在用户的区域是否有 Azure Data Box 服务可以使用。 输入或选择以下信息，然后选择“应用”。

    |设置  |值  |
    |---------|---------|
    |传输类型     | 选择“**导出到 CSV**”。        |
    |订阅     | 对于 Data Box 服务，选择一个 EA、CSP 或 Azure 赞助订阅。 <br> 该订阅将链接到计费帐户。       |
    |资源组     |    选择现有资源组。 <br> 资源组是可以统一管理或部署的资源的逻辑容器。         |
    |源 Azure 区域    |    选择数据当前所在的 Azure 区域。         |
    |目标国家/地区     |     选择要将此设备寄送到的国家/地区。        |

   ![选择 Data Box 设置](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. 选择“Data Box”。 单次订购的可用最大容量为 80 TB。 可以创建多个订单，以增加数据大小。

   ![选择 Data Box 容量](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. 在“**顺序**”中指定“**基本** 顺序详细信息”。 输入或选择以下信息。

    |设置  |值  |
    |---------|---------|
    |订阅     | 系统会根据前面所做的选择自动填充此订阅。|
    |资源组 | 之前选择的资源组。 |
    |导出命令名称     |  提供友好名称用于跟踪订单。 <br> 名称可以为 3 到 24 个字符，包括字母、数字和连字符。 <br> 名称必须以字母或数字开头和结尾。      |

    ![导出命令基础知识](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    选择“**下一步：数据选择**”以继续。

7. 在“**数据选择**”中，选择“**添加存储帐户和导出类型**”。

    ![添加存储帐户和导出类型](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. 在“**选择导出选项**”中，指定导出选项的详细信息。 输入或选择以下信息，然后选择“**添加**”。

    |设置  |值  |
    |---------|---------|
    |存储帐户     | 要从中导入数据的 Azure 存储帐户。 |
    |导出类型     | 指定要从 **所有对象** 导出并 **使用 XML 文件** 的数据类型。<ul><li> **所有对象** -指定作业根据用户选择的“**传输选项**”导出所有数据。</li><li> **使用 XML 文件** –指定一个 XML 文件，其中包含要从存储帐户导出的 blob 和/或文件的一组路径和前缀。 该 XML 文件需要位于所选存储帐户的容器中，并且当前不支持从文件共享中进行选择。 文件必须为非空 .xml 文件。</li></ul>        |
    |传输选项     |  指定“**全选**”、“**所有 blob**”和“**所有文件**”中的数据传输选项。 <ul><li> **全选** -指定导出所有 Blob 和 Azure 文件。 如果用户使用的存储帐户仅支持 blob（Blob 存储帐户），则“**所有文件**”选项将不可选择。</li><li> **所有 Blob** -指定仅导出块和页 blob。</li><li> **所有文件** -指定导出所有文件时不包括 blob。 已有的存储帐户类型（GPv1 和 GPv2、高级存储或 blob 存储）可确定可导出的数据类型。 有关详细信息，请参阅“[支持的存储帐户导出](../import-export/storage-import-export-requirements.md#supported-storage-types)”。</li></ul>         |
    |包括详细日志     | 指示是否需要一个详细日志文件，其中包含已成功导出的所有文件的列表。 有关导出工单的复制日志和详细日志的详细信息，请参阅[查看日志](data-box-export-logs.md#view-logs-during-data-copy)。 |

    > [!NOTE]
    > 如果为“导出类型”设置选择“使用 XML 文件”，则需要确保 XML 文件包含有效路径和/或前缀 。 必须构造和提供 XML 文件。 如果文件无效或没有任何数据与指定的路径相匹配，则该顺序将以部分数据终止或未导出任何数据。 有关指导，请参阅[创建 XML 文件](#create-xml-file)。

    若要查看如何将 XML 文件添加到容器，请参阅 [使用 XML 文件导出命令](data-box-deploy-export-ordered.md#export-order-using-xml-file)。

   ![选择导出选项](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   若要查看 XML 输入示例，请参阅[创建 XML 文件](#create-xml-file)

9. 在“**数据选择**”中，查看设置，然后选“**下一步：安全>** ”以继续。

   ![导出命令，数据选择](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    通过“**安全性**”屏幕，可以使用自己的加密密钥，还可选择使用双重加密。

    “安全性”屏幕上的所有设置都是可选的。 如果不更改任何设置，则将应用默认设置。

    ![Data Box 导入订单向导的“安全性”屏幕](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. 如果要使用自己的客户管理的密钥来保护新资源的解锁密钥，请展开“加密类型”。

    可选择性地为 Azure Data Box 配置客户管理的密钥。 默认情况下，Data Box 使用 Microsoft 管理的密钥来保护解锁密钥。

    客户管理的密钥不影响设备上数据的加密方式。 该密钥仅用于加密设备解锁密钥。

    如果不想使用客户管理的密钥，请跳到步骤 16。

    ![显示加密类型设置的“安全性”屏幕](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. 选择“客户管理的密钥”作为密钥类型。 然后，选中“选择密钥保管库和密钥”。
   
    ![安全屏幕，客户管理的密钥的设置](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. 在“**从 Azure Key Vault 中选择密钥**”屏幕上，订阅会自动填充。

    - 对于“密钥保管库”，可以从下拉列表中选择现有的密钥保管库。

      ![从 Azure Key Vault 屏幕上选择密钥](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - 还可以选择“新建”来创建新的密钥保管库。 在“创建密钥保管库”屏幕上，输入资源组和密钥保管库名称。 确保已启用软删除和清除保护 。 接受其他所有默认值，然后选择“查看 + 创建”。

      ![创建新的 Azure Key Vault 设置](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      查看密钥保管库的信息，然后选择“创建”。 等待几分钟，直到密钥保管库创建完成。

      ![新的 Azure Key Vault 查看屏幕](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. 在“**从 Azure Key Vault 选择密钥**”屏幕上，可选择密钥保管库中的现有密钥。

    ![从 Azure Key Vault 中选择现有密钥](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    如果要创建新的密钥，请选择“新建”。 必须使用 RSA 密钥。 大小可以是 2048 或更大。 输入新密钥的名称，接受其他默认值，然后选择“创建”。

      ![创建新的密钥选项](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      在密钥保管库中创建密钥后，你将收到通知。

14. 选择要使用的密钥的版本，然后选中“选择” 。

      ![在密钥保管库中创建的新密钥](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    如果要创建新的密钥版本，请选择“新建”。

    ![打开用于创建新密钥版本的对话框](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    在“**新建密钥**”屏幕上，选择新密钥版本的“设置”，然后选择“**创建**”。

    ![创建新的密钥版本](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    “安全性”屏幕上的“加密类型”设置将显示密钥保管库和密钥 。

    ![用于客户管理的密钥的密钥和密钥保管库](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. 选择将用于管理对此资源的访问权限的用户标识。 选中“选择用户标识”。 在右侧面板中，选择要使用的订阅和托管标识。 然后选取“选择”  。

    用户分配的托管标识是一个可用于管理多个资源的独立 Azure 资源。 有关详细信息，请参阅[托管标识类型](../active-directory/managed-identities-azure-resources/overview.md)。  

    如果需要创建新的托管标识，请按照[使用 Azure 门户创建、列出和删除用户分配的托管标识以及如何为其分配角色](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)中的指南操作。
    
    ![选择用户标识](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    用户标识显示在“加密类型”设置中。

    现在可以折叠“**加密类型**”设置。

    ![加密类型设置中显示的所选用户标识](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. 如果要启用基于软件的双重加密，请展开“**双重加密(适用于高度安全的环境)** ”，然后选择“**为订单启用双重加密**”。 

    除了对 Data Box 上的数据进行 AES-256 位加密，还可执行基于软件的加密。

    > [!NOTE]
    > 启用此选项可能会导致订单处理和数据复制耗时较长。 创建订单后，不能更改此选项。

    ![Data Box 导入双重加密的安全性屏幕](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    在完成时选择“下一步:联系人详细信息”以继续。

11. 在“**联系人详细信息**”中，选择“ **+ 添加送货地址**”以输入送货信息。

    ![添加送货地址](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. 在“**送货地址**”中，提供用户的姓名、公司的名称和邮政地址，以及有效的电话号码。 选择“验证”。 服务将验证寄送地址，以确定服务是否在该区域可用。 如果服务在指定的寄送地址可用，则会将结果通知给你。

    ![验证送货地址](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    如果要在可托管送货的区域订购，则可以选择此选项。 有关自托管寄送的详细信息，请参阅[使用自托管寄送](data-box-portal-customer-managed-shipping.md)。

13. 成功验证送货详细信息后，选择“**添加送货地址**”。

14. 在“**联系人详细信息**”中，查看送货地址和电子邮件地址。 服务会将有关任何订单状态更新的电子邮件通知发送到指定的电子邮件地址。

    我们建议使用组电子邮件，以便在组中的管理员离任后，可以持续收到通知。

    ![联系人详细信息](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. 选择“**下一步：核对 + 订单>** ”。 必须接受条款和条件才能继续创建订单。

16. 选择“订单”。 创建订单需要几分钟时间。

    ![提交订单](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>使用 XML 文件创建导出命令

如果选择“**使用 XML 文件**”，则可以指定要导出的（页面和块）的特定容器和 blob。 以下步骤说明如何使用 XML 文件导出数据。 若要构造 XML 文件，请遵循[创建 XML 文件](#create-xml-file)中的指导。

若要使用 XML 文件导出数据，请执行以下操作：

1. 对于“**导出类型**”，请选择“**使用 XML 文件**”。 这是 XML 文件，用于指定要导出的特定 blob 和 Azure 文件。 若要添加 XML 文件，请选择“**单击此处以选择 XML 文件**”。

     ![选择导出选项，XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. 选择“ **+ 容器**”来新建容器。

    ![选择导出选项，容器](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. 在从 Azure 门户右侧弹出的“**新建容器**”选项卡中，为容器添加一个名称。 名称必须为小写，并且可以包含数字和短划线“-”。 然后从下拉列表框中选择“**公共访问级别**”。 建议选择“**专用（非匿名访问）** ”，以防止其他人访问数据。 有关容器访问级别的详细信息，请参阅“[容器访问权限](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)”。

   ![选择导出选项，新建容器设置](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. 选择“创建”。

   ![选择“导出”选项，然后单击“新建容器”。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   如果已成功创建容器，用户将收到以下消息：

   ![已成功创建容器。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. 选择所创建的容器，并双击它。

   ![显示容器详细信息](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. 双击容器将打开容器属性视图。 现在想要附加（或浏览）包含要导出的 blob 和/或 Azure 文件列表的 XML 文件。 选择“上传”。

   ![将 Blob 上传到容器](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. 已成功将 XML 文件添加到容器中。 仅导出在该 XML 中指定的 blob 和 Azure 文件。

   ![已将 XML 文件添加到容器](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="create-xml-file"></a>创建 XML 文件

如果你使用 XML 文件来选择要导出的 Blob 和文件，请遵循以下指导原则创建 XML 文件：
- 示例 XML 文件选项卡：复制示例 XML 文件，其中包含每个标记的示例。
- XML 文件概述选项卡：查看 XML 文件的标记要求。
- 前缀示例选项卡：查看选择多个要导出的 Blob 和文件的有效前缀示例。

### <a name="sample-xml-file"></a>[示例 XML 文件](#tab/sample-xml-file)

此示例 XML 文件包含每个 XML 标记（用于选择要在 Data Box 导出工单中导出的 Blob 和文件）的示例。 

- 若要查看 XML 文件要求，请转到“XML 文件概述”选项卡。
- 若要查看有效 Blob 和文件前缀的更多示例，请转到“前缀示例”选项卡。

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!--BlobList selects individual blobs (BlobPath) and multiple blobs (BlobPathPrefix) in Blob storage for export.-->
   <BlobList>
      <BlobPath>/container1/blob.txt</BlobPath> <!-- Exports /container1/blob.txt -->
      <BlobPathPrefix>/container2/</BlobPathPrefix> <!--Exports all blobs in container2 -->
      <BlobPathPrefix>/container</BlobPathPrefix>  <!-- Exports all containers beginning with prefix: "container" -->
      <BlobPathPrefix>/container1/2021Q2</BlobPathPrefix> <!-- Exports all blobs in container1 with prefix: "2021Q2" -->
   </BlobList>
   
   <!--AzureFileList selects individual files (FilePath) and multiple files (FilePathPrefix) in Azure File storage for export.-->
   <AzureFileList>
      <FilePath>/fileshare1/file.txt</FilePath> <!-- Exports /fileshare1/file.txt -->
      <FilePathPrefix>/fileshare1/</FilePath> <!-- Exports all directories and files in fileshare1 -->
      <FilePathPrefix>/fileshare</FilePathPrefix> <!-- Exports all directories and files in any fileshare with prefix: "fileshare" -->
      <FilePathPrefix>/fileshare2/contosowest</FilePathPrefix> <!-- Exports all directories and files in fileshare2 with prefix: "contosowest" -->
   </AzureFileList>
```

### <a name="xml-file-overview"></a>[XML 文件概述](#tab/xml-file-overview)

为导出工单构造 XML 文件时，请遵循以下指导原则。 错误的标记格式会导致导出失败。

有关在下达导出工单时上传 XML 文件的步骤，请参阅[使用 XML 文件的导出工单](#export-order-using-xml-file)。

#### <a name="path-vs-prefix"></a>路径与前缀

若要在 XML 文件中正确使用 XML 标记格式，需要了解路径与前缀之间的差别：

* 路径会选择并筛选单个 Blob 或文件。
* 前缀会选择并筛选多个 Blob 或多个文件。

若要查看格式正确的前缀示例，请转到“前缀示例”选项卡。

#### <a name="tag-usage"></a>标记用法

在 Data Box 导出工单的 XML 文件中使用以下 XML 标记：

| XML 标记           |说明 |
|-------------------|------------|
|`<BlobList>`       |&lt;BlobPath &gt; 和 &lt; BlobPathPrefix&gt; 标记的父标记。|
|`<BlobPath>`       |选择单个 Blob。 |
|`<BlobPathPrefix>` |选择具有通用前缀的 Blob。 若要查看示例，请转到“前缀示例”选项卡。|
|`<AzureFileList>`  |&lt;FilePath&gt; 和 &lt; FilePathPrefix&gt; 标记的父标记。|
|`<FilePath>`       |选择单个文件。 |
|`<FilePathPrefix>` |选择具有通用前缀的文件。 若要查看示例，请转到“前缀示例”选项卡。|

若要查看上下文中的标记，请转到“示例 XML 文件”选项卡。

#### <a name="xml-tag-requirements"></a>XML 标记要求

* 所有 XML 标记区分大小写，需要与上表中的标记完全匹配。
* 开始和结束标记必须匹配。
* 错误的 XML 标记或格式可能导致数据导出失败。
* 如果 Blob 前缀或文件前缀无效，则不会导出任何数据。 若要查看有效前缀的示例，请转到“前缀示例”选项卡。

### <a name="prefix-examples"></a>[前缀示例](#tab/prefix-examples)

这些示例路径显示了构造前缀以选择多个要导出的 Blob 或文件的各种方法。

#### <a name="valid-blob-path-prefixes"></a>有效的 Blob 路径前缀

以下示例路径与 &lt;BlobPathPrefix&gt; 标记一起使用，以在 Azure Blob 存储中选择要导出的多个 Blob。

|Blob 路径前缀        |说明                                                                     |标记示例                         |
|------------------------|--------------------------------------------------------------------------------|------------------------------------|
|/                       |导出存储帐户中的所有 Blob。                                       |`<BlobPathPrefix>/</BlobPathPrefix>`|
|/$root/                 |导出根容器中的所有 Blob。                                        |`<BlobPathPrefix>/$root/</BlobPathPrefix>`|
|/container2/            |导出容器 container2 中的所有 Blob。                              |`<BlobPathPrefix>/container2/</BlobPathPrefix>`|
|/container          |导出以前缀 container 开头的任何容器中的所有 Blob。      |`<BlobPathPrefix>/container</BlobPathPrefix>`|
|/container1/2021Q2      |导出容器 container1 中以前缀 2021Q2 开头的所有 Blob 。|`<BlobPathPrefix>/container1/2021Q2</BlobPathPrefix>`|

若要选择单个要导出的 Blob，请使用具有容器路径和 Blob 名称的 &lt;BlobPath&gt; 标记。 例如，若要选择 container1 容器中的 blob.txt，请使用以下标记： `<BlobPath>/container1/blob.txt</BlobPath>`。

#### <a name="valid-file-path-prefixes"></a>有效的文件路径前缀

以下示例路径与 &lt;FilePathPrefix&gt; 标记一起使用，以选择多个要导出的 Azure 文件。

|文件路径前缀        |说明                                                                                          |标记示例|
|------------------------|-----------------------------------------------------------------------------------------------------|-----------|
|/                       |导出存储帐户中的所有文件和目录。 |`<FilePathPrefix>/</FilePath>Prefix>`|
|/fileshare1/            |导出名为 fileshare1 的共享中的所有文件和目录。                                                 |`<FilePathPrefix>/fileshare1/</FilePath>Prefix>`|
|/fileshare              |导出以前缀 fileshare 开头的任何文件共享中的所有文件和目录。 |`<FilePathPrefix>/fileshare</FilePath>Prefix>`|
|/fileshare2/contosowest |导出文件共享 fileshare2 中以前缀 contosowest 开头的所有文件和目录 。|`<FilePathPrefix>/fileshare2/contosowest</FilePath>Prefix>`|

若要选择单个要导出的 Blob，请使用具有共享路径和文件名的 &lt;FilePath&gt; 标记。 例如，若要选择 fileshare1 中的 file.txt，请使用以下标记： `<FilePath>/fileshare1/file.txt</FilePath>`

---

## <a name="track-the-order"></a>跟踪订单

下单后，可以从 Azure 门户跟踪订单状态。 转到你的 Data Box 订单，然后转到“概述”来查看状态。 门户中会显示订单处于“已订购”状态。

设备准备完成后，将从所选存储帐户开始数据复制。 门户会显示订单处于“**正在进行数据复制**”状态。

![Data Box 导出命令，正在进行数据复制](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box 复制源存储帐户的数据。 数据复制完成后，Data Box 被锁定，门户将显示订单处于“**复制已完成**”状态。

![Data Box 导出命令，数据复制完成](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

从 Azure 存储到 Data Box 的数据导出有时会失败。 请确保这些 blob 不是存档 blob，因为不支持导出这些 blob。 

> [!NOTE]
> 对于存档 blob，需要先将这些 blob 解除冻结，然后才能将其从 Azure 存储帐户导出到 Data Box。 有关详细信息，请参阅[将存档 blob 解除冻结]( ../storage/blobs/storage-blob-rehydration.md)。

如果没有设备可以使用，用户会收到通知。 如果设备有货，Microsoft 会确定要发货的设备，并准备发货。 在设备准备期间，会执行以下操作：

* 将为与设备关联的每个存储帐户创建 SMB 共享。
* 将为每个共享生成访问凭据（例如用户名和密码）。
* 设备已锁定，只能使用设备解锁密码进行访问。 若要检索密码，需要登录到 Azure 门户帐户并选择“**设备详细信息**”。

然后，Microsoft 会安排区域承运人发运设备。 设备发货后，你会收到跟踪号。 门户会显示订单处于“已发运”状态。

![Data Box 导出命令已处理](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

如果选择了自托管寄送，则当设备准备好从数据中心提货时，用户将收到一封电子邮件通知，其中包含后续步骤。 有关自托管寄送的详细信息，请参阅“[自托管寄送](data-box-portal-customer-managed-shipping.md)”。

![自托管寄送待提货](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>取消订单

若要取消此订单，请在 Azure 门户中转到“概览”，然后在命令栏中选择“取消” 。

下单后，只要订单状态尚未标记为“已处理”，就可以随时取消订单。

若要删除已取消的订单，请转到“概况”，然后在命令栏中选择“删除” 。

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
>
> * 导出的先决条件
> * Data Box 的导出命令
> * 跟踪导出命令
> * 取消导出命令

请继续学习下一篇教程，了解如何设置 Data Box。

> [!div class="nextstepaction"]
> [设置 Azure Data Box](./data-box-deploy-set-up.md)
