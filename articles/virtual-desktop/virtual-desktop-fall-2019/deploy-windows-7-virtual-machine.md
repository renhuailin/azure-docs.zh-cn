---
title: 在 Azure 虚拟桌面（经典版）上部署 Windows 7 虚拟机 - Azure
description: 如何在 Azure 虚拟桌面（经典版）上配置和部署 Azure 7 虚拟机。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1b7c78ab9a518649d205bb9dc92c86d7bb71bcea
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111744622"
---
# <a name="deploy-a-windows-7-virtual-machine-on-azure-virtual-desktop-classic"></a>在 Azure 虚拟桌面（经典版）上部署 Windows 7 虚拟机

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典版），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 如果要尝试管理当前版本的 Azure 虚拟桌面中引入的 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[此文](../deploy-windows-7-virtual-machine.md)。

在 Azure 虚拟桌面上部署 Windows 7 虚拟机 (VM) 的过程与运行更高版本 Windows 的 VM 略有不同。 本指南将介绍如何部署 Windows 7。

## <a name="prerequisites"></a>先决条件

在开始之前，请按照[使用 PowerShell 创建主机池](create-host-pools-powershell-2019.md)中的说明创建主机池。 之后，请按照[在 Azure 市场中创建主机池](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group)中的说明将一个或多个用户分配到桌面应用程序组。

## <a name="configure-a-windows-7-virtual-machine"></a>配置 Windows 7 虚拟机

完成先决条件后，即可在 Azure 虚拟桌面上配置 Windows 7 VM 以进行部署。

若要在 Azure 虚拟桌面上设置 Windows 7 VM：

1. 登录到 Azure 门户，搜索 Windows 7 Enterprise 映像或上传自己的自定义 Windows 7 Enterprise (x64) 映像。
2. 部署一个或多个虚拟机，将 Windows 7 Enterprise 作为其主机操作系统。 请确保虚拟机允许远程桌面协议 (RDP)（TCP/3389 端口）。
3. 使用 RDP 连接到Windows 7 Enterprise 主机，并使用配置部署时定义的凭据进行身份验证。
4. 将使用 RDP 连接到主机时使用的帐户添加到“远程桌面用户”组。 如果不这样做，则在将 VM 加入 Active Directory 域后，可能无法连接到该 VM。
5. 请在 VM 上转到 Windows 更新。
6. 安装“重要”类别中的所有 Windows 更新。
7. 安装“可选”类别中的所有 Windows 更新（语言包除外）。 这将安装完成这些说明中的操作所需的远程桌面协议 8.0 更新 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387))。
8. 打开本地组策略编辑器，导航到“计算机配置” > “管理模板” > “Windows 组件” > “远程桌面服务” > “远程桌面会话主机” > “远程会话环境”。
9. 启用远程桌面协议 8.0 策略。
10. 将此 VM 加入 Active Directory 域。
11. 通过运行以下命令重启虚拟机：

     ```cmd
     shutdown /r /t 0
     ```

12. 按照[此处](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/)的说明获取注册令牌。
13. [下载适用于 Windows 7 的 Azure 虚拟桌面代理](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)。
14. [下载适用于 Windows 7 的 Azure 虚拟桌面代理管理器](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)。
15. 打开 Azure 虚拟桌面代理安装程序，然后按照说明进行操作。 出现提示时，请提供在步骤 12 中创建的注册密钥。
16. 打开 Azure 虚拟桌面代理管理器，然后按照说明进行操作。
17. （可选）阻止 TCP/3389 端口以移除对 VM 的直接远程桌面协议访问权限。
18. （可选）确认 .NET framework 至少为版本 4.7.2。 如果要创建自定义映像，这尤其重要。

## <a name="next-steps"></a>后续步骤

Azure 虚拟桌面部署现已准备就绪，可供使用。 [下载最新版本的 Azure 虚拟桌面客户端](https://aka.ms/wvd/clients/windows)以开始工作。

有关 Azure 虚拟桌面上 Windows 7 的已知问题和疑难解答说明的列表，请参阅疑难解答文章 [Azure 虚拟桌面中的 Windows 7 虚拟机疑难解答](troubleshoot-windows-7-vm.md)。
