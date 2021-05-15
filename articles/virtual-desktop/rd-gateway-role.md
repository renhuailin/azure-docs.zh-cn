---
title: 在 Windows 虚拟桌面中部署 RD 网关角色 - Azure
description: 如何在 Windows 虚拟桌面中部署 RD 网关角色。
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 37d859bccd7fea082e059c7ab19c789276974cd1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445680"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>在 Windows 虚拟桌面中部署 RD 网关角色（预览版）

> [!IMPORTANT]
> 此功能目前处于公开预览状态。
> 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何使用 RD 网关角色（预览版）在你的环境中部署远程桌面网关服务器。 可以在物理计算机或虚拟机上安装服务器角色，具体取决于是创建本地环境、基于云的环境还是混合环境。

## <a name="install-the-rd-gateway-role"></a>安装 RD 网关角色

1. 用管理凭据登录到目标服务器。

2. 在“服务器管理器”中选择“管理”，然后选择“添加角色和功能”。   将打开“添加角色和功能”安装程序。

3. 擨“开∪ぇ蚲”い，选择“捸捗；”愢

4. 在“选择安装类型”中，选择“基于角色或基于功能的安装”，然后单击“下一步”。  

5. 对于“选择目标服务器”，选择“从服务器池中选择一个服务器”。  对于“服务器池”，选择本地计算机的名称。 完成后，选择“下一步”  。

6. 在“选择服务器角色” >  “角色”中，选择“远程桌面服务”。 完成后，选择“下一步”  。

7. 在“远程桌面服务”中，选择“下一步”。 

8. 对于“选择角色服务”，仅选择“远程桌面网关”。当系统提示你添加所需功能时，选择“添加功能”。   完成后，选择“下一步”  。

9. 对于“网络策略和访问服务”，选择“下一步”。 

10. 对于“Web 服务器角色(IIS)”，选择“下一步”。 

11. 对于“选择角色服务”，选择“下一步”。 

12. 在“确认安装选择”中，选择“安装”。  在安装过程中，请不要关闭安装程序。

## <a name="configure-rd-gateway-role"></a>配置 RD 网关角色

安装 RD 网关角色后，需要对其进行配置。

配置 RD 网关角色：

1. 打开“服务器管理器”，然后选择“远程桌面服务”。 

2. 转到“服务器”，右键单击服务器的名称，然后选择“RD 网关管理器”。 

3. 在 RD 网关管理器中，右键单击网关的名称，然后选择“属性”。

4. 打开“SSL 证书”选项卡，选择“将证书导入 RD 网关”气泡，然后选择“浏览并导入证书…”。  

5. 选择 PFX 文件的名称，然后选择“打开”。

6. 出现提示时，输入该 PFX 文件的密码。

7. 导入证书及其私钥后，应会显示证书的密钥属性。

>[!NOTE]
>由于 RD 网关角色应是公共的，建议使用公开颁发的证书。 如果使用私下颁发的证书，则需要确保先为所有客户端配置该证书的信任链。

## <a name="next-steps"></a>后续步骤

如果要将高可用性添加到 RD 网关角色，请参阅[将高可用性添加到 RD web 和网关 web 前端](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)。