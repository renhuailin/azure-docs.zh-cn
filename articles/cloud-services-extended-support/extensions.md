---
title: '云服务的扩展 (扩展支持) '
description: '云服务的扩展 (扩展支持) '
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c2eddf75f99b751773220ae677d66fe8c09abb0e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744251"
---
# <a name="extensions-for-cloud-services-extended-support"></a>云服务的扩展 (扩展支持) 

扩展是在角色上提供部署后配置和自动化任务的小型应用程序。 例如，你可以在云服务期间启用角色中的远程桌面连接 (扩展支持) 使用远程桌面扩展部署。  

## <a name="remote-desktop-extension"></a>远程桌面扩展

可以通过远程桌面访问在 Azure 中运行的角色的桌面。 你可以使用远程桌面连接，在应用程序运行时对其进行故障排除和诊断。

你可以在开发过程中通过在服务定义中加入远程桌面模块或通过远程桌面扩展来启用远程桌面连接。 

有关详细信息，请参阅 [从 Azure 门户配置远程桌面](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Windows Azure 诊断扩展

可以监视任何云服务的关键性能指标。 每个云服务角色收集极少量的数据：CPU 使用率、网络使用率和磁盘利用率。 如果云服务将 Microsoft. 诊断扩展应用于角色，则该角色可以收集更多数据点。 

使用基本监视时，默认情况下，将每隔 3 分钟从角色实例中采样和收集性能计数器数据。 此基本监视数据不会存储在存储帐户中，并且不会产生相关的额外费用。 

使用高级监视时，将每隔 5 分钟、1 小时和 12 小时采样和收集其他指标。 聚合的数据存储在存储帐户的表中，将在 10 天后予以清除。 使用的存储帐户是按角色配置的；可以对不同的角色使用不同的存储帐户。 

可以通过[PowerShell](deploy-powershell.md)或[ARM 模板](deploy-template.md)为云服务启用 (扩展支持) 的 Windows Azure 诊断扩展


## <a name="next-steps"></a>后续步骤 
- 查看云服务的 [部署先决条件](deploy-prerequisite.md) (扩展支持) 。
- 查看 [云服务的常见问题 (](faq.md) 扩展支持) 。
- ) 使用 [Azure 门户](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [模板](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)部署云服务 (扩展支持。