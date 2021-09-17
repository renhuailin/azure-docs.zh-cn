---
title: Azure 虚拟桌面必需 URL 列表 - Azure
description: 必须取消阻止的 URL 的列表。取消阻止是为了确保 Azure 虚拟桌面部署能够按预期进行。
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 95081b661d4f33577df07420ab194ec48488221a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768083"
---
# <a name="required-url-list"></a>所需的 URL 列表

若要部署并使用 Azure 虚拟桌面，必须取消阻止某些 URL，以便你的虚拟机 (VM) 可以随时访问这些 URL。 本文列出了为使 Azure 虚拟桌面正常运行而需要取消阻止的必需 URL。 

>[!IMPORTANT]
>Azure 虚拟桌面不支持那些会阻止本文所列 URL 的部署。

## <a name="required-url-check-tool"></a>所需 URL 检查工具

所需 URL 检查工具将验证 URL，并显示虚拟机运行时所需的 URL 是否可以访问。 如果不可以，则该工具将列出不可访问的 URL，以便在需要时取消阻止。

请务必记住以下内容：

- 只能将所需 URL 检查工具用于商业云中的部署。
- 所需 URL 检查工具无法检查带通配符的 URL，因此请确保先取消阻止这些 URL。

### <a name="requirements"></a>要求

需要具备以下条件才能使用所需 URL 检查工具：

- VM 必须具有 .NET 4.6.2 framework
- RDAgent 的版本为 1.0.2944.400 或更高版本
- WVDAgentUrlTool.exe 文件必须与 WVDAgentUrlTool.config 文件位于同一文件夹中

### <a name="how-to-use-the-required-url-check-tool"></a>如何使用所需 URL 检查工具

要使用所需 URL 检查工具，请执行以下操作：

1. 在 VM 上以管理员身份打开命令提示符。
2. 运行以下命令，将目录更改为与当前生成代理（本例中为 RDAgent_1.0.2944.1200）相同的文件夹：

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. 运行以下命令：

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. 运行该文件后，你将看到可访问和不可访问 URL 的列表。

    例如，以下屏幕截图显示了需要取消阻止两个所需非通配符 URL 的场景：

    > [!div class="mx-imgBorder"]
    > ![不可访问的 URL 输出的屏幕截图。](media/noaccess.png)
    
    下面是取消阻止所有所需非通配符 URL 后，输出的显示内容：

    > [!div class="mx-imgBorder"]
    > ![可访问的 URL 输出的屏幕截图。](media/access.png)

## <a name="virtual-machines"></a>虚拟机

为 Azure 虚拟桌面创建的 Azure 虚拟机必须能够访问 Azure 商业云中的以下 URL：

|地址|出站 TCP 端口|目的|服务标记|
|---|---|---|---|
|*.wvd.microsoft.com|443|服务流量|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|代理流量|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|代理流量|AzureCloud|
|*xt.blob.core.windows.net|443|代理流量|AzureCloud|
|*eh.servicebus.windows.net|443|代理流量|AzureCloud|
|*xt.table.core.windows.net|443|代理流量|AzureCloud|
|*xt.queue.core.windows.net|443|代理流量|AzureCloud|
|catalogartifact.azureedge.net|443|Azure 市场|AzureCloud|
|kms.core.windows.net|1688|Windows 激活|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|代理和 SXS 堆栈更新|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure 门户支持|AzureCloud|
| 169.254.169.254 | 80 | [Azure 实例元数据服务终结点](../virtual-machines/windows/instance-metadata-service.md) | 不适用 |
| 168.63.129.16 | 80 | [会话主机运行状况监视](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 不适用 |

>[!IMPORTANT]
>Azure 虚拟桌面现在支持 FQDN 标记。 有关详细信息，请参阅[使用 Azure 防火墙保护 Windows 虚拟桌面部署](../firewall/protect-windows-virtual-desktop.md)。
>
>建议使用 FQDN 标记或服务标记（而不是 URL）来防止服务问题。 列出的 URL 和标记只对应于 Azure 虚拟桌面站点和资源。 这些 URL 不包含其他服务（如 Azure Active Directory）的 URL。

为 Azure 虚拟桌面创建的 Azure 虚拟机必须能够访问 Azure 政府云中的以下 URL：

|地址|出站 TCP 端口|目的|服务标记|
|---|---|---|---|
|*.wvd.azure.us|443|服务流量|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|代理流量|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|代理流量|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|代理流量|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|代理流量|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|代理流量|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|代理流量|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows 激活|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|代理和 SXS 堆栈更新|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure 门户支持|AzureCloud|
| 169.254.169.254 | 80 | [Azure 实例元数据服务终结点](../virtual-machines/windows/instance-metadata-service.md) | 不适用 |
| 168.63.129.16 | 80 | [会话主机运行状况监视](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 不适用 |

下表列出了 Azure 虚拟机可以访问的可选 URL：

|地址|出站 TCP 端口|目的|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|向 Microsoft Online Services 进行身份验证|login.microsoftonline.us|
|*.events.data.microsoft.com|443|遥测服务|无|
|www.msftconnecttest.com|443|检测 OS 是否已连接到 Internet|无|
|*.prod.do.dsp.mp.microsoft.com|443|Windows 更新|无|
|login.windows.net|443|登录到 Microsoft 365 等 Microsoft Online Services|login.microsoftonline.us|
|*.sfx.ms|443|OneDrive 客户端软件更新|oneclient.sfx.ms|
|*.digicert.com|443|证书吊销检查|无|
|*.azure-dns.com|443|Azure DNS 解析|无|
|*.azure-dns.net|443|Azure DNS 解析|无|

>[!NOTE]
>Azure 虚拟桌面目前没有可通过取消阻止它们来允许网络流量的 IP 地址范围列表。 我们目前仅支持取消阻止特定 URL。
>
>如果使用的是下一代防火墙 (NGFW)，则需要使用专门为 Azure IP 创建的动态列表来确保你可以进行连接。
>
>如需与 Office 相关的安全 URL（包括所需的 Azure Active Directory 相关 URL）的列表，请参阅 [Office 365 URL 和 IP 地址范围](/office365/enterprise/urls-and-ip-address-ranges)。
>
>对于涉及服务流量的 URL，必须使用通配符 (*)。 如果不希望为代理相关的流量使用 *，下面提供了有关如何在不使用通配符的情况下查找 URL 的信息：
>
>1. 将虚拟机注册到 Azure 虚拟桌面主机池。
>2. 打开“事件查看器”，转到“Windows 日志” > “应用程序” > “WVD-Agent”，查找事件 ID 3701。  
>3. 取消阻止在事件 ID 3702 下找到的 URL。 事件 ID 3701 下的 URL 特定于区域。 你需要对要在其中部署虚拟机的每个区域的相关 URL 重复执行取消阻止过程。

## <a name="remote-desktop-clients"></a>远程桌面客户端

所用的任何远程桌面客户端都必须有权访问以下 URL：

|地址|出站 TCP 端口|目的|客户端|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|服务流量|All|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|排查数据问题|All|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLink|All|无|
|aka.ms|443|Microsoft URL 缩短符|All|无|
|docs.microsoft.com|443|文档|All|无|
|privacy.microsoft.com|443|隐私声明|All|无|
|query.prod.cms.rt.microsoft.com|443|客户端更新|Windows 桌面|无|

>[!IMPORTANT]
>若要获取可靠的客户端体验，必须打开这些 URL。 不支持阻止访问这些 URL，否则会影响服务功能。
>
>这些 URL 仅对应于客户端站点和资源。 此列表不包含其他服务（如 Azure Active Directory）的 URL。 可以在 [Office 365 URL 和 IP 地址范围](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)的 ID 56、59 和 125 下找到 Azure Active Directory URL。
