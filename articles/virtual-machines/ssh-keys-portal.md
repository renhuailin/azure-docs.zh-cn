---
title: 在 Azure 门户中创建 SSH 密钥
description: 了解如何在 Azure 门户中生成并存储用于连接 Linux VM 的 SSH 密钥。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: aa6cdb37e6b664398e469ab8e1c3eefbb721bba0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698443"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>在 Azure 门户中生成并存储 SSH 密钥

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

如果你经常使用门户来部署 Linux VM，可以直接在门户中创建 SSH 密钥或者从计算机上传这些密钥，以更方便地使用这些密钥。

可以在首次创建 VM 时创建 SSH 密钥，然后将其重复用于其他 VM。 或者，也可以单独创建 SSH 密钥，以根据组织的需求在 Azure 中存储一组密钥。 

如果你有现有的密钥并想要在门户中简化其使用，可以上传这些密钥，并将其存储在 Azure 中供重复使用。

有关创建 SSH 密钥并将其用于 Linux VM 的更详细信息，请参阅[使用 SSH 密钥连接到 Linux VM](./linux/ssh-from-windows.md)。

## <a name="generate-new-keys"></a>生成新密钥

1. 打开 [Azure 门户](https://portal.azure.com)。

1. 在页面顶部，键入 SSH 进行搜索。 在“市场”下，选择“SSH 密钥” 。

1. 在“SSH 密钥”页上，选择“创建” 。

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="创建新资源组并生成 SSH 密钥对":::

1. 在“资源组”中，选择“新建”以创建新的资源组用于存储密钥 。 键入资源组的名称并选择“确定”。

1. 在“区域”中，选择一个用于存储密钥的区域。 可在任何区域中使用密钥，这只是存储密钥的区域。

1. 在“密钥对名称”中键入密钥的名称。

1. 在“SSH 公钥源”中，选择“生成公钥源” 。 

1. 完成操作后，选择“查看 + 创建”。

1. 通过验证后，选择“创建”。

1. 然后会出现一个弹出窗口，请在其中选择“下载私钥并创建资源”。 这将以 .pem 文件格式下载 SSH 密钥。

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="以 .pem 文件格式下载私钥":::

1. 下载 .pem 文件后，建议将其移到计算机上可从 SSH 客户端方便访问的某个位置。


## <a name="connect-to-the-vm"></a>连接到 VM

在本地计算机上，打开 PowerShell 提示符并键入：

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

例如，键入：`ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>上传 SSH 密钥

还可以上传要存储在 Azure 中的 SSH 公钥。 有关如何创建 SSH 密钥对的信息，请参阅[使用 SSH 密钥连接到 Linux VM](./linux/ssh-from-windows.md)。

1. 打开 [Azure 门户](https://portal.azure.com)。

1. 在页面顶部，键入 SSH 进行搜索。 在“市场”下，选择“SSH 密钥”*。

1. 在“SSH 密钥”页上，选择“创建” 。

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="上传要存储在 Azure 中的 SSH 公钥":::

1. 在“资源组”中，选择“新建”以创建新的资源组用于存储密钥 。 键入资源组的名称并选择“确定”。

1. 在“区域”中，选择一个用于存储密钥的区域。 可在任何区域中使用密钥，这只是存储密钥的区域。

1. 在“密钥对名称”中键入密钥的名称。

1. 在“SSH 公钥源”中，选择“上传现有公钥” 。 

1. 将公钥的完整内容粘贴到“上传密钥”中，然后选择“查看 + 创建” 。

1. 验证完成后，选择“创建”。 

上传密钥后，可以在创建 VM 时选择使用该密钥。

## <a name="list-keys"></a>列出密钥

在门户中创建的 SSH 密钥将作为资源存储，因此你可以筛选资源视图以查看所有这些密钥。

1. 在门户中选择“所有资源”。
1. 在筛选器中选择“类型”，然后取消选择“全选”选项以清除列表 。
1. 在筛选器中键入 SSH，然后选择“SSH 密钥” 。

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="显示如何筛选列表以查看所有 SSH 密钥的屏幕截图。":::

## <a name="get-the-public-key"></a>获取公钥

如果需要公钥，可以轻松地在密钥的门户页中复制该公钥。 只需列出你的密钥（使用最后一个部分所述的过程），然后从列表中选择密钥即可。 此时将打开该密钥的页面，可以单击该密钥旁边的“复制到剪贴板”图标进行复制。

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 Azure VM 中使用 SSH 密钥，请参阅[使用 SSH 密钥连接到 Linux VM](./linux/ssh-from-windows.md)。
