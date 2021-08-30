---
title: 为 VM 映像生成 SAS URI
description: 为 Azure 市场中的虚拟硬盘 (VHD) 生成共享访问签名 (SAS) URI。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 06/23/2021
ms.openlocfilehash: 4bebe0ea34f9f9cc62b0ed4e96fd6fb1c7615635
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913448"
---
# <a name="generate-a-sas-uri-for-a-vm-image"></a>为 VM 映像生成 SAS URI

> [!NOTE]
> 无需使用 SAS URI 来发布 VM。 只需在合作伙伴中心共享映像即可。 请参阅[使用已批准的基础映像创建虚拟机](azure-vm-create-using-approved-base.md)或[使用自己的映像创建虚拟机](azure-vm-create-using-own-image.md)中的说明。

为 VHD 生成 SAS URI 需要满足以下要求：

- 只需要“列出”和“读取”权限。 请不要提供“写入”或“删除”访问权限。
- 访问权限持续时间（过期日期）应至少是从 SAS URI 创建时间开始算起的 3 周。
- 若要防范 UTC 时间更改，请将开始日期设置为当前日期的前一天。 例如，如果当前日期是 2020 年 6 月 16 日，请选择 6/15/2020。

## <a name="extract-vhd-from-a-vm"></a>从 VM 提取 VHD

> [!NOTE]
> 如果已在存储帐户中上传了 VHD，则可以跳过此步骤。

若要从 VM 提取 VHD，需要创建 VM 磁盘的快照，然后从该快照提取 VHD。

首先创建 VM 磁盘的快照：

1. 登录到 Azure 门户。
2. 首先，在左上方选择“创建资源”，然后搜索并选择“快照”。
3. 在“快照”边栏选项卡中选择“创建”。
4. 输入快照的“名称”  。
5. 选择现有的资源组，或输入新资源组的名称。
6. 对于源磁盘，选择要获取其快照的托管磁盘。
7. 选择用于存储快照的“帐户类型”。 使用 Standard HDD，除非需要将其存储在高性能 SSD 上。
8. 选择“创建”。

### <a name="extract-the-vhd"></a>提取 VHD

使用以下脚本将快照导出到你的存储帐户中的某个 VHD。

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. Currently, only general purpose v1 storage is supported.
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

$sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>脚本说明
此脚本使用以下命令生成快照的 SAS URI，然后使用该 SAS URI 将基础 VHD 复制到存储帐户。 表中的每条命令均链接到特定于命令的文档。


|Command  |说明  |
|---------|---------|
| az disk grant-access    |     生成只读 SAS，使用该 SAS 可以将基础 VHD 文件复制到存储帐户或将其下载到本地    |
|  az storage blob copy start   |    将 Blob 从一个存储帐户异步复制到另一个存储帐户。 使用 az storage blob show 检查新 Blob 的状态。     |
|

## <a name="generate-the-sas-address"></a>生成 SAS 地址

有两个用于创建 SAS 地址 (URL) 的常用工具：

1. **Azure 存储资源管理器** – 在 Azure 门户上提供。
2. **Azure CLI** – 建议用于非 Windows 操作系统，以及自动化或连续集成环境。

### <a name="using-tool-1-azure-storage-explorer"></a>使用工具 1：Azure 存储资源管理器

1. 转到你的 **存储帐户**。
1. 打开存储资源管理器。

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="存储帐户窗口。":::

3. 在“容器”中，右键单击 VHD 文件并选择“获取共享访问签名”。 
4. 在“共享访问签名”对话框中填写以下字段：

    1. 开始时间 – VHD 访问权限的开始日期。 请提供当前日期的前一天的日期。
    2. 过期时间 – VHD 访问权限的过期日期。 请提供自当前日期开始算起的至少三周后的日期。
    3. 权限 – 选择“读取”和“列出”权限。
    4. 容器级别 – 选中“生成容器级别的共享访问签名 URI”复选框。

    ![“共享访问签名”对话框。](media/vm/create-sas-uri-storage-explorer.png)

5. 若要为此 VHD 创建关联 SAS URI，请选择“创建”。
6. 复制“URL”，并将其保存到位于安全位置的某个文本文件。 此生成的 SAS URL 适用于容器级访问权限。 若要使它具体化，请编辑文本文件以添加 VHD 名称。
7. 在 SAS URI（包括正斜杠）中的 vhds 字符串后面插入 VHD 名称。 最终 SAS URI 应如下所示：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. 对要发布的计划中的每个 VHD 重复这些步骤。

### <a name="using-tool-2-azure-cli"></a>使用工具 2：Azure CLI

1. 下载并安装 [Microsoft Azure CLI](/cli/azure/install-azure-cli)。 版本适用于 Windows、macOS，以及 Linux 的各种分发版。
2. 创建一个 PowerShell 文件（扩展名为 .ps1），在其中复制以下代码，然后在本地保存该文件。

    ```azurecli-interactive
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <container-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 编辑该文件以使用以下参数值。 以 UTC 日期时间格式提供日期，例如 2020-04-01T00:00:00Z。

    - account-name – 你的 Azure 存储帐户名称。
    - account-key – 你的 Azure 存储帐户密钥。
    - start-date – VHD 访问权限的开始日期。 请提供当前日期的前一天的日期。
    - expiry-date – VHD 访问权限的到期日期。 请提供自当前日期开始算起的至少三周后的日期。

    下面是（在撰写本文时）正确的参数值示例：

    ```azurecli-interactive
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name <container-name> -- permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

1. 保存更改。
2. 使用以下方法之一，通过管理特权运行此脚本，以创建容器级访问权限的 SAS 连接字符串：

    - 从控制台运行脚本。 在 Windows 中，右键单击脚本，然后选择“以管理员身份运行”。
    - 从 PowerShell 脚本编辑器（例如 [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)）运行脚本。 此屏幕显示如何在此编辑器中创建 SAS 连接字符串：

    [![在 PowerShell 编辑器中创建 SAS 连接字符串](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. 复制 SAS 连接字符串，并将其保存到位于安全位置的某个文本文件。 编辑此字符串，以添加 VHD 位置信息来创建最终 SAS URI。
7. 在 Azure 门户中，转到包含与新 URI 关联的 VHD 的 Blob 存储。
8. 复制 Blob 服务终结点的 URL：

    ![复制 Blob 服务终结点的 URL。](media/vm/create-sas-uri-blob-endpoint.png)

9. 编辑包含步骤 6 中所述 SAS 连接字符串的文本文件。 使用以下格式创建完整 SAS URI：

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>验证 SAS URI

在合作伙伴中心发布 SAS URI 之前请先对其进行检查，以免在提交请求后发生 SAS URI 相关的任何问题。 此过程是可选的，但建议这样做。

- URI 包含 VHD 映像文件名（包括扩展名 `.vhd`）。
- `Sp=rl` 出现在 URI 中间附近。 此字符串显示已指定“读取”和“列出”访问权限。
- 当 `sr=c` 出现时，这表示指定了容器级访问权限。
- 将 URI 复制并粘贴到浏览器中以对 blob 进行测试下载（可以在下载完成之前取消该操作）。

## <a name="next-steps"></a>后续步骤

- 如果遇到问题，请参阅 [VM SAS 失败消息](azure-vm-sas-failure-messages.md)
- [登录到合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165935)
- [在 Azure 市场中创建虚拟机产品/服务](azure-vm-create.md)