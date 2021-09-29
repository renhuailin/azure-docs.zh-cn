---
title: 使用 Azure 导入/导出服务从 Azure Blob 导出数据 | Microsoft Docs
description: 了解如何在 Azure 门户中创建导出作业，以便从 Azure Blob 传输数据。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 32605a78336c8d9e1aeb730be50441d43b8fe45c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659127"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>使用 Azure 导入/导出服务从 Azure Blob 存储导出数据

本文分步说明如何使用 Azure 导入/导出服务从 Azure Blob 存储安全地导出大量数据。 该服务要求你将空驱动器寄送到 Azure 数据中心。 该服务将数据从存储帐户导出到驱动器，然后将驱动器寄回。

## <a name="prerequisites"></a>先决条件

在创建导出作业以从 Azure Blob 存储传输数据之前，请仔细查看并完成以下此服务的先决条件列表。
必须具备以下条件：

- 拥有可用于导入/导出服务的有效 Azure 订阅。
- 拥有至少一个 Azure 存储帐户。 请参阅[导入/导出服务支持的存储帐户和存储类型](storage-import-export-requirements.md)的列表。 有关创建新存储帐户的信息，请参阅[如何创建存储帐户](../storage/common/storage-account-create.md)。
- 拥有足够数量的[受支持类型](storage-import-export-requirements.md#supported-disks)的磁盘。
- 具有 FedEx/DHL 帐户。 如果想使用 FedEx/DHL 以外的承运商，请通过 `adbops@microsoft.com` 与 Azure Data Box 运营团队联系。
  - 该帐户必须是有余额的有效帐户，且有退货功能。
  - 生成导出作业的跟踪号。
  - 每个作业都应有一个单独的跟踪号。 不支持多个作业共享相同跟踪号。
  - 如果没有承运商帐户，请转到：
    - [创建 FedEx 帐户](https://www.fedex.com/en-us/create-account.html)，或
    - [创建 DHL 帐户](http://www.dhl-usa.com/en/express/shipping/open_account.html)。

## <a name="step-1-create-an-export-job"></a>步骤 1：创建导出作业

### <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户中执行以下步骤来创建导出作业。

1. 登录到 <https://portal.azure.com/> 。
2. 搜索“导入/导出作业”。

    ![搜索“导入/导出作业”](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. 选择“+ 新建”  。

    ![选择“新建”以创建一个新作业 ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. 在“基本信息”中：

   1. 选择一个订阅。
   1. 选择一个资源组，或选择“新建”，创建新资源组。
   1. 输入导入作业的描述性名称。 可使用此名称来跟踪作业进度。
       * 此名称只能包含小写字母、数字和连字符。
       * 此名称必须以字母开头，并且不得包含空格。

   1. 选择“从 Azure 导出”。

    ![导出顺序的基本选项](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    选择“下一步: 作业详细信息 >”以继续。

5. 在“作业详细信息”中：

   1. 选择数据当前所在的 Azure 区域。
   1. 选择要从中导出数据的存储帐户。 请使用靠近你所在位置的存储帐户。

      放置位置根据选定存储帐户所属的区域自动进行填充。

   1. 指定要从存储帐户导出到空驱动器的 Blob 数据。 选择以下三种方法之一。

      - 选择“全部导出”以导出存储帐户中的所有 blob 数据。

        ![全部导出](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - 选择“所选容器和 Blob”，并指定要导出的容器和 Blob。 可以使用多种选择方法。 选择“添加”选项会在右侧打开一个面板，可在其中添加选择字符串。

        |选项|说明|
        |------|-----------|      
        |添加容器|导出容器中的所有 Blob。<br>选择“添加容器”并输入每个容器名称。|
        |添加 Blob|指定要导出的各个 Blob。<br>选择“添加 Blob”。 然后指定 Blob 的相对路径，以容器名称开头。 使用 *$root* 指定根容器。<br>必须以有效格式提供 blob 路径，以免在处理过程中出现错误，如以下屏幕截图所示。 有关详细信息，请参阅[有效 blob 路径示例](#examples-of-valid-blob-paths)。|
        |添加前缀|使用前缀在容器中选择一组具有相似名称的容器，或者容器中具有相似名称的 Blob。 该前缀可以是容器名称的前缀、完整容器名称或者后跟 Blob 名称前缀的完整容器名称。 |

        ![导出所选容器和 blob](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - 选择“从 Blob 列表文件导出(XML 格式)”并选择一个 XML 文件，其中包含要从存储帐户导出的 Blob 的路径和前缀列表。 必须构造该 XML 文件并将其存储在存储帐户的容器中。 该文件不能为空。

      > [!IMPORTANT]
      > 如果使用 XML 文件选择要导出的 Blob，请确保 XML 包含有效的路径和/或前缀。 如果该文件无效或没有任何数据与指定的路径相匹配，则订单导出操作将会终止，此时只导出了一部分数据或者未导出任何数据。

       若要查看如何将 XML 文件添加到容器，请参阅[使用 XML 文件导出订单](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file)。

      ![从 blob 列表文件导出](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > 如果要导出的 Blob 正在数据复制的过程中使用，则 Azure 导入/导出服务将生成该 Blob 的快照并复制该快照。

   选择“下一步: 寄送 >”以继续。

6. 在“寄送”中：

    - 从下拉列表中选择承运商。 如果要使用 FedEx/DHL 以外的承运商，请从下拉列表中选择现有的选项。 请通过 `adbops@microsoft.com` 与 Azure Data Box 运营团队联系，提供计划使用的承运商的信息。
    - 输入你已在该承运商那里创建的有效承运商帐户编号。 导出作业完成后，Microsoft 将使用此帐户寄回驱动器。
    - 提供完整且有效的联系人姓名、电话、电子邮件、街道地址、城市、邮政编码、省/自治区/直辖市和国家/地区。

        > [!TIP]
        > 请提供组电子邮件，而非为单个用户指定电子邮件地址。 这可确保即使管理员离开也会收到通知。

    选择“查看 + 创建”以继续。

7. 在“查看 + 创建”中：

   1. 查看作业详细信息。
   1. 记下作业名称以及为将磁盘寄送到 Azure 而提供的 Azure 数据中心寄送地址。

      > [!NOTE]
      > 始终将磁盘发送到 Azure 门户中记录的数据中心。 如果磁盘寄送到错误的数据中心，则不会处理该作业。

   1. 查看有关隐私和删除源数据的订单“条款”。 如果你同意这些条款，请选中条款下面的复选框。 订单验证随即开始。

   ![查看并创建导出订单](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. 通过验证后，选择“创建”。

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在 Azure 门户中按照以下步骤来创建导出作业。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>创建作业

1. 使用 [az extension add](/cli/azure/extension#az_extension_add) 命令添加 [az import-export](/cli/azure/import-export) 扩展：

    ```azurecli
    az extension add --name import-export
    ```

1. 若要获取可从中接收磁盘的位置列表，请使用 [az import-export location list](/cli/azure/import-export/location#az_import_export_location_list) 命令：

    ```azurecli
    az import-export location list
    ```

1. 运行以下 [az import-export create](/cli/azure/import-export#az_import_export_create) 命令来创建使用现有存储帐户的导出作业：

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > 请提供组电子邮件，而非为单个用户指定电子邮件地址。 这可确保即使管理员离开也会收到通知。

   此作业会导出存储帐户中的所有 blob。 可以通过将此值替换为“--export”来指定要导出的 Blob：

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   此参数值会导出根容器中名为“logo.bmp”的 blob。

   也可以选择使用前缀来选择容器中的所有 blob。 将此值替换为“--export”：

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   有关详细信息，请参阅[有效 blob 路径示例](#examples-of-valid-blob-paths)。

   > [!NOTE]
   > 如果在复制数据时，要导出的 blob 正在使用中，则 Azure 导入/导出服务将生成该 blob 的快照并复制快照。

1. 使用 [az import-export list](/cli/azure/import-export#az_import_export_list) 命令查看资源组 myierg 的所有作业：

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. 若要更新作业或取消作业，请运行 [az import-export update](/cli/azure/import-export#az_import_export_update) 命令：

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

使用以下步骤在 Azure PowerShell 中创建导出作业。

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> 尽管 Az.ImportExport PowerShell 模块为预览版，但你需要使用 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中默认提供。

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>创建作业

1. 若要获取可从中接收磁盘的位置的列表，请使用 [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) cmdlet：

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. 运行以下 [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) 示例来创建使用现有存储帐户的导出作业：

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > 请提供组电子邮件，而非为单个用户指定电子邮件地址。 这可确保即使管理员离开也会收到通知。

   此作业会导出存储帐户中的所有 blob。 可以通过将此值替换为“-ExportBlobListblobPath”来指定要导出的 Blob：

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   此参数值会导出根容器中名为“logo.bmp”的 blob。

   也可以选择使用前缀来选择容器中的所有 blob。 将此值替换为“-ExportBlobListblobPath”：

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   有关详细信息，请参阅[有效 blob 路径示例](#examples-of-valid-blob-paths)。

   > [!NOTE]
   > 如果在复制数据时，要导出的 blob 正在使用中，则 Azure 导入/导出服务将生成该 blob 的快照并复制快照。

1. 使用 [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) cmdlet 查看资源组 myierg 的所有作业：

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. 若要更新作业或取消作业，请运行 [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) cmdlet：

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>步骤 2：寄送驱动器

如果不知道所需的驱动器数量，请转到[检查驱动器数量](#check-the-number-of-drives)。 如果知道驱动器数量，则直接寄送驱动器。

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>步骤 3：使用跟踪信息更新作业

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>步骤 4：接收磁盘

当仪表板报告作业已完成时，会将磁盘寄送给你，并且门户上会提供货件的跟踪号。

1. 收到包含导出数据的驱动器后，你需要获取 BitLocker 密钥才能解锁驱动器。 转到 Azure 门户中的导出作业。 单击“导入/导出”选项卡。
2. 从列表中选择并单击导出作业。 转到“加密”，然后复制密钥。

   ![查看导出作业的 BitLocker 密钥](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. 使用 BitLocker 密钥解锁磁盘。

导出已完成。

## <a name="step-5-unlock-the-disks"></a>步骤 5：解锁磁盘

使用以下命令解锁驱动器：

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

下面是示例输入的示例。

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

现在，可删除作业，也可将其保留。 作业将在 90 天后自动删除。

## <a name="check-the-number-of-drives"></a>检查驱动器数量

此可选步骤有助于确定导出作业所需的驱动器数量。 在运行[受支持 OS 版本](storage-import-export-requirements.md#supported-operating-systems)的 Windows 系统上执行此步骤。

1. 在 Windows 系统上[下载 WAImportExport 版本 1](https://www.microsoft.com/download/details.aspx?id=42659)。
2. 解压缩到默认文件夹 `waimportexportv1`。 例如，`C:\WaImportExportV1`。
3. 使用管理员权限打开 PowerShell 或命令行窗口。 若要将目录切换到解压缩的文件夹，请运行以下命令：

   `cd C:\WaImportExportV1`

4. 若要检查选定 blob 所需的磁盘数量，请运行以下命令：

   `WAImportExport.exe PreviewExport /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    下表介绍了这些参数：

    |命令行参数|说明|
    |--------------------------|-----------------|
    |**/logdir:**|可选。 日志目录。 详细日志文件将写入此目录。 如果未指定，则使用当前目录作为日志目录。|
    |**/ExportBlobListFile:**|必需。 包含要导出的 Blob 的 Blob 路径列表或 Blob 路径前缀的 XML 文件的路径。 导入/导出服务 REST API 的[放置作业](/rest/api/storageimportexport/jobs)操作的 `BlobListBlobPath` 元素中使用的文件格式。|
    |**/DriveSize:**|必需。 用于导出作业的驱动器大小，例如 500 GB、1.5 TB。|

    请参阅 [PreviewExport 命令示例](#example-of-previewexport-command)。

5. 检查能否读取/写入要寄送的用于导出作业的驱动器。

### <a name="example-of-previewexport-command"></a>PreviewExport 命令示例

以下示例演示了 `PreviewExport` 命令：

```powershell
    WAImportExport.exe PreviewExport /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

导出 Blob 列表文件可能包含 Blob 名称和 Blob 前缀，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Azure 导入/导出工具可列出要导出的所有 blob，在考虑所有必要开销的情况下计算如何将其打包到指定大小的驱动器，然后估算保存 blob 和驱动器使用情况信息所需的驱动器数量。

下面是一个省略了信息性日志的输出示例：

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>有效 blob 路径示例

下表显示有效 Blob 路径的示例：

   | 选择器 | Blob 路径 | 说明 |
   | --- | --- | --- |
   | 开头为 |/ |导出存储帐户中的所有 Blob |
   | 开头为 |/$root/ |导出根容器中的所有 Blob |
   | 开头为 |/book |导出任何容器中以前缀 book 开头的所有 Blob |
   | 开头为 |/music/ |导出容器 music 中的所有 Blob |
   | 开头为 |/music/love |导出容器 music 中以前缀 love 开头的所有 Blob |
   | 等于 |$root/logo.bmp |导出根容器中的 Blob logo.bmp |
   | 等于 |videos/story.mp4 |导出容器 videos 中的 Blob story.mp4 |

## <a name="next-steps"></a>后续步骤

- [查看作业和驱动器状态](storage-import-export-view-drive-status.md)
- [查看导入/导出要求](storage-import-export-requirements.md)
