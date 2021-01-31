---
title: 设置 Kerberos 密钥发行中心 proxy Windows 虚拟桌面-Azure
description: 如何将 Windows 虚拟桌面主机池设置为使用 Kerberos 密钥发行中心代理。
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219649"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>配置 Kerberos 密钥发行中心代理 (预览版) 

> [!IMPORTANT]
> 此功能目前处于公开预览状态。
> 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何为主机池配置 Kerberos 密钥发行中心 (KDC) 代理 (预览) 。 此代理可让组织通过其企业边界之外的 Kerberos 进行身份验证。 例如，你可以使用 KDC 代理为外部客户端启用智能卡身份验证。

## <a name="how-to-configure-the-kdc-proxy"></a>如何配置 KDC 代理

配置 KDC 代理：

1. 以管理员身份登录到 Azure 门户。

2. 请参阅 Windows 虚拟桌面页面。

3. 选择要为其启用 KDC 代理的主机池，然后选择 " **RDP 属性**"。

    > [!div class="mx-imgBorder"]
    > !["Azure 门户" 页面的屏幕截图，显示用户选择 "主机池"，然后选择 "示例" 主机池的名称，然后选择 "RDP 属性"。](media/rdp-properties.png)

4. 选择 " **高级** " 选项卡，然后输入以下格式的值（不含空格）：

    > kdcproxyname： s：\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![显示选定的 "高级" 选项卡的屏幕截图，并按步骤4中所述输入值。](media/advanced-tab-selected.png)

5. 选择“保存”。

6. 所选主机池现在应开始发出 RDP 连接文件，其中包含输入的 kdcproxyname 字段。

## <a name="next-steps"></a>后续步骤

远程桌面服务中的 RDGateway 角色包含 KDC 代理服务。 请参阅 [在 Windows 虚拟桌面中部署 RD 网关角色](rd-gateway-role.md) ，了解如何将其设置为 Windows 虚拟桌面的目标。
