---
title: 在 Azure 云服务（外延支持）中存储和使用证书
description: 在 Azure 云服务（外延支持）中存储和使用证书的流程
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 83c455d6a8c952e8f4258dbb90d1d6726e8fcd65
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447906"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>在 Azure 云服务（外延支持）中使用证书

Key Vault 用于存储与云服务（外延支持）关联的证书。 可通过 [Azure 门户](../key-vault/general/quick-create-portal.md)和 [PowerShell](../key-vault/general/quick-create-powershell.md) 创建密钥保管库。 将证书添加到密钥保管库，然后引用服务配置文件中的证书指纹。 还需要启用 Key Vault 来获得适当的权限，以便云服务（外延支持）资源可从 Key Vault 检索存储为机密的证书。  

## <a name="upload-a-certificate-to-key-vault"></a>向 Key Vault 上传证书 

1.  登录到 Azure 门户并导航到 Key Vault。 如果未设置 Key Vault，可以选择在此同一窗口中创建一个。

2. 选择“访问策略”

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="图像显示从 Key Vault 边栏选项卡中选择“访问策略”。":::

3. 确保访问策略包括以下属性：
    - **启用对 Azure 虚拟机的访问以进行部署**

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="图像显示 Azure 门户中的“访问策略”窗口。":::
 
4.  选择“证书” 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="图像显示在 Azure 门户的 Key Vault 边栏选项卡策略窗口中选择“证书”选项。":::

5. 选择“生成/导入”

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="图像显示选择“生成/导入”选项":::

4.  填写完成上传证书所需的信息。 证书需要采用 .PFX 格式。

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="图像显示 Azure 门户中的“导入”窗口。":::

5.  将证书详细信息添加到服务配置(.cscfg) 文件中的角色。 确保 Azure 门户中证书的指纹与服务配置 (.cscfg) 文件中的指纹相匹配。 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```
6.  若要通过 ARM 模板进行部署，可导航到 Key Vault 中标记为“机密标识符”的证书来找到 certificateUrl

    :::image type="content" source="media/certs-and-key-vault-6.png" alt-text="图像显示 Key Vault 中的“机密标识符”字段。":::

## <a name="next-steps"></a>后续步骤 
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
