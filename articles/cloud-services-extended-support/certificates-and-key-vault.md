---
title: 在 Azure 云服务（外延支持）中存储和使用证书
description: '在 Azure 云服务中存储和使用证书的过程 (扩展支持) '
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d9ff86eeb0e64e7edbad0eeca51b04cabbd191e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722647"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>在 Azure 云服务中使用证书 (扩展支持) 

Key Vault 用于存储与云服务（外延支持）关联的证书。 可以通过 [Azure 门户](../key-vault/general/quick-create-portal.md) 和 [PowerShell](../key-vault/general/quick-create-powershell.md)创建密钥保管库。 将证书添加到 Key Vault，并在服务配置文件中引用证书指纹。 还需要启用 Key Vault 来获得适当的权限，以便云服务（外延支持）资源可从 Key Vault 检索存储为机密的证书。  

## <a name="upload-a-certificate-to-key-vault"></a>将证书上传到 Key Vault 

1.  登录到 Azure 门户并导航到 Key Vault。 如果未设置 Key Vault，则可以选择在此同一窗口中创建一个。

2. 选择 **访问策略**

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="图像显示从 key vault 边栏选项卡中选择访问策略。":::

3. 确保访问策略包括以下属性：
    - **启用对 Azure 虚拟机的访问以进行部署**
    - **启用对 Azure 资源管理器的访问以进行模板部署** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Image 显示了 &quot;Azure 门户中的&quot; 访问策略 &quot;窗口。":::
 
4.  选择 **证书** 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="图像显示在 &quot;Azure 门户中的&quot; 密钥保管库 &quot;边栏选项卡中选择&quot; 证书 &quot;选项。":::

5. 选择 **生成/导入**

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="图像显示选择 &quot;生成/导入&quot; 选项":::

4.  完成上载证书所需的信息。 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Image 显示了 Azure 门户中的导入窗口。":::

5.  将证书详细信息添加到服务配置 ( .cscfg) 文件中。 确保 Azure 门户中证书的指纹与服务配置 ( .cscfg) 文件中的指纹匹配。 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>后续步骤 
- 查看云服务的 [部署先决条件](deploy-prerequisite.md) (扩展支持) 。
- 请参阅云服务（外延支持）的[常见问题解答](faq.md)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。