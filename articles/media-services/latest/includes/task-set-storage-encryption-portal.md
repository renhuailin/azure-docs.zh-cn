---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013209"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>在存储帐户上设置加密

1. 在 Azure 门户中，在屏幕顶部的“搜索”字段中输入要加密的存储帐户的名称。  匹配项将显示在搜索字段下方。
1. 选择所需的存储帐户。 将显示存储帐户屏幕。
1. 选择“加密”。
1. 选择 Microsoft 托管密钥或客户管理的密钥。

### <a name="use-microsoft-managed-keys"></a>使用 Microsoft 托管密钥

默认情况下，使用 Microsoft 托管密钥加密存储帐户中的数据。

### <a name="use-customer-managed-keys"></a>使用客户管理的密钥

1. 选择“客户管理的密钥”。
1. 选择“输入密钥 URI”或“从密钥保管库中选择” 。
    1. 如果选择“输入密钥 URI”，请在“密钥 URI”字段中输入密钥 URI，然后选择订阅。 （该选项可能已选中。）
    1. 如果选择“从密钥保管库中选择”，则选择“选择密钥保管库和密钥” 。 “从 Azure Key Vault 选择密钥”屏幕将会显示。
1. 选择要使用的“密钥保管库”，然后选择密钥保管库中已有的密钥或者创建新密钥 。
    1. 如果选择创建新密钥，请从“选项”下拉列表中选择“生成”或“导入”  。 只能导入 RSA 密钥。
    1. 若要生成新密钥，请在“名称”字段中为密钥命名，然后选择密钥类型：
        1. RSA - 密钥大小：2048、3072 或 4096。 这是大多数客户的选择。
        1. EC - 椭圆曲线名称：P-256、P-384、P-521 或 P-256K
        1. （可选）可以设置密钥的激活日期和到期日期。
        1. 选择“是”，启用自动轮换密钥。
        1. 选择“创建”  。
    1. 若要导入密钥，请单击“选择文件”字段中的任意位置，选择要上传的文件。
        1. 在“名称”字段中为密钥命名。
        1. （可选）可以设置密钥的激活日期和到期日期。
        1. 选择“是”，启用自动轮换密钥。
        1. 选择“创建”  。
    1. 选择“选择”，选择此密钥以加密存储帐户。 将返回到“加密”屏幕。
1. **重要说明！** 选择“保存”，以保存加密设置，否则刚刚执行的所有操作均无效。
