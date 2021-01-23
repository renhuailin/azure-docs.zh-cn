---
title: '在云服务中应用远程桌面 (扩展支持) '
description: '为云服务启用远程桌面 (扩展支持) '
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 53f873013a6f16ce5a28ee5d915afa556057f643
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744214"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a> (扩展支持将远程桌面扩展应用到 Azure 云服务) 

Azure 门户使用远程桌面扩展，即使在部署应用程序之后也能启用远程桌面。 你的云服务的 "远程桌面" 设置允许你启用远程桌面、更新本地管理员帐户、选择用于身份验证的证书并设置这些证书的到期日期。 

## <a name="apply-remote-desktop--extension"></a>应用远程桌面扩展
1. 导航到要为其启用远程桌面的云服务，然后在左侧导航窗格中选择 **"远程桌面"** 。

    :::image type="content" source="media/remote-desktop-1.png" alt-text="图像显示在 Azure 门户中选择 &quot;远程桌面&quot; 选项":::

2. 选择 **添加** 。
3. 选择要为其启用远程桌面的角色。
4. 填写 "用户名"、"密码"、"到期" 和 "证书" 的必填字段 (不需要) 。

    :::image type="content" source="media/remote-desktop-2.png" alt-text="图像显示了连接到远程桌面所需的信息。":::

5. 完成后，选择“保存”。 可能需要一些时间角色实例才能完成接收连接的准备。

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>连接到启用了远程桌面的角色实例
为角色启用远程桌面后，可以直接从 Azure 门户启动连接。

1. 单击 " **角色和实例** " 打开实例设置。

    :::image type="content" source="media/remote-desktop-3.png" alt-text="图像显示在 &quot;配置&quot; 边栏选项卡中选择 &quot;角色和实例&quot; 选项。":::

2. 选择配置了远程桌面的角色实例。
3. 单击 " **连接** " 下载远程桌面连接文件。

    :::image type="content" source="media/remote-desktop-4.png" alt-text="图像显示在 Azure 门户中选择辅助角色实例。":::
    
4. 打开文件以连接到角色实例。


## <a name="next-steps"></a>后续步骤 
- 查看云服务的 [部署先决条件](deploy-prerequisite.md) (扩展支持) 。
- 查看 [云服务的常见问题 (](faq.md) 扩展支持) 。
- ) 使用 [Azure 门户](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [模板](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)部署云服务 (扩展支持。