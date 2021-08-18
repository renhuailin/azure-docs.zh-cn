---
title: 云服务扩展（外延支持）
description: 云服务扩展（外延支持）
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 84b27bc4ae30e28a1d97fb8e9931602a1eb14504
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444779"
---
# <a name="extensions-for-cloud-services-extended-support"></a>云服务扩展（外延支持）

扩展是小型应用程序，用于在角色上提供部署后配置和自动化任务。 例如，在云服务（外延支持）部署期间，你可以通过使用远程桌面扩展在你的角色中启用远程桌面连接。 

## <a name="key-vault-extension"></a>Key Vault 扩展

通过 Key Vault VM 扩展，可自动刷新 Azure Key Vault 中存储的证书。 具体而言，该扩展监视一系列观测到的存储在密钥保管库中的证书，并在检测到更改时检索并安装相应的证书。 它还允许跨区域/跨订阅引用云服务的证书（外延支持）。

有关详细信息，请参阅[配置云服务的密钥保管库扩展（外延支持）](./enable-key-vault-virtual-machine.md)

## <a name="remote-desktop-extension"></a>远程桌面扩展

可以通过远程桌面访问在 Azure 中运行的角色的桌面。 可以使用远程桌面连接，在应用程序正在运行时排查和诊断其问题。

在角色中启用远程桌面连接有两种方式：一是可以在开发过程中通过在服务定义中加入远程桌面模块启用，二是可以通过远程桌面扩展启用。 

有关详细信息，请参阅[从 Azure 门户配置远程桌面](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Windows Azure 诊断扩展

可以监视任何云服务的关键性能指标。 每个云服务角色收集极少量的数据：CPU 使用率、网络使用率和磁盘利用率。 如果云服务已将 Microsoft.Azure.诊断扩展应用到某个角色，则该角色可以收集其他数据点。 

使用基本监视时，默认情况下，将每隔 3 分钟从角色实例中采样和收集性能计数器数据。 此基本监视数据不会存储在存储帐户中，并且不会产生相关的额外费用。 

使用高级监视时，将每隔 5 分钟、1 小时和 12 小时采样和收集其他指标。 聚合的数据存储在存储帐户的表中，将在 10 天后予以清除。 使用的存储帐户是按角色配置的；可以对不同的角色使用不同的存储帐户。 

有关详细信息，请参阅[在云服务中应用 Windows Azure 诊断扩展(外延支持)](enable-wad.md)

## <a name="anti-malware-extension"></a>反恶意软件扩展
Azure 应用程序或服务可以使用 PowerShell cmdlet 来启用和配置适用于 Azure 云服务的 Microsoft 反恶意软件。 请注意，Microsoft Antimalware 以禁用状态安装在运行 Windows Server 2012 R2 及更早版本的云服务平台中，需要 Azure 应用程序执行操作来启用它。 对于 Windows Server 2016 及更高版本，默认情况下会启用 Windows Defender，因此这些 cmdlet 可用于配置 Antimalware。

有关详细信息，请参阅[使用外延支持将 Microsoft Antimalware 添加到 Azure 云服务 (CS-ES)](../security/fundamentals/antimalware-code-samples.md#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support)

若要详细了解 Azure Antimalware，请访问[此处](../security/fundamentals/antimalware.md)



## <a name="next-steps"></a>后续步骤 
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
