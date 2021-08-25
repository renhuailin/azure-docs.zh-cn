---
title: 如何配置云服务（经典）| Microsoft Docs
description: 了解如何在 Azure 中配置云服务。 了解如何更新云服务配置以及配置对角色实例的远程访问。 这些示例使用 Azure 门户。
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 67c3d9b88e899b1c2ced93093636aeb2cc7b3e02
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823271"
---
# <a name="how-to-configure-and-azure-cloud-service-classic"></a>如何配置 Azure 云服务（经典）

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

可以在 Azure 门户中配置最常使用的云服务设置。 或者，如果希望直接更新配置文件，可下载要更新的服务配置文件，并上传更新文件并通过配置更改更新云服务。 无论使用哪种方法，配置更新都会应用于所有角色实例。

还可以管理云服务角色的实例，或者通过远程桌面进入这些角色中。

如果每个角色至少具有两个角色实例，那么 Azure 在配置更新期间只能确保 99.95% 的服务可用性。 这使得一台虚拟机可以在另一台虚拟机更新时处理客户端请求。 有关详细信息，请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/)。

## <a name="change-a-cloud-service"></a>更改云服务

打开 [Azure 门户](https://portal.azure.com/)后，导航到云服务。 在这里，可以对云服务的许多方面进行管理。

![“设置”页](./media/cloud-services-how-to-configure-portal/cloud-service.png)

可以通过“设置”或“所有设置”链接打开“设置”，在该选项卡中，可以更改“属性”、更改“配置”、管理“证书”、设置“警报规则”，以及管理有权访问该云服务的“用户”         。

![Azure 云服务设置](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>管理来宾 OS 版本

默认情况下，Azure 会定期将来宾 OS 更新为 OS 系列（在服务配置 (.cscfg) 中指定）中支持的最新映像，例如 Windows Server 2016。

如果需要设定某一特定 OS 版本，可在“配置”  中进行设置。

![设置 OS 版本](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> 选择特定 OS 版本会禁用自动 OS 更新，用户需要自行负责修补工作。 务必确保角色实例可以接收更新，否则应用程序可能存在安全漏洞。

## <a name="monitoring"></a>监视

可将警报添加到云服务。 单击“设置”   > “警报规则”   > “添加警报”  。

![“设置”窗格的屏幕截图，其中“警报规则”选项突出显示并以红框标出，“添加警报”选项以红框标出。](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

可以在此处设置警报。 使用“指标”  下拉框，可以针对以下类型的数据设置警报。

* 磁盘读取
* 磁盘写入
* 进网络
* 出网络
* CPU 百分比

![“添加警报规则”窗格的屏幕截图，其中已设置所有配置选项。](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>配置从度量值磁贴进行的监视

可以单击“云服务”边栏选项卡的“监视”部分中的某个度量值磁贴，而不必使用“设置” > “警报规则”    。

![云服务监视](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

可以在此处自定义用于磁贴的图表，也可以添加警报规则。

## <a name="reboot-reimage-or-remote-desktop"></a>重新启动、重置映像或远程桌面

可以通过 [Azure 门户(设置远程桌面)](cloud-services-role-enable-remote-desktop-new-portal.md)、[PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) 或通过 [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md) 设置远程桌面。

要重新启动、重置映像或远程登录到云服务，请选择云服务实例。

![云服务实例](./media/cloud-services-how-to-configure-portal/cs-instance.png)

可以启动远程桌面连接、以远程方式重新启动实例，或者以远程方式重置实例的映像（开始使用全新的映像）。

![云服务实例按钮](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>重新配置 .cscfg

建议通过 [service config (cscfg)](cloud-services-model-and-package.md#cscfg) 文件来重新配置云服务。 首先，用户需要下载 .cscfg 文件，对其进行修改，再上传该文件。

1. 单击“设置”图标或“所有设置”链接以打开“设置”    。

    ![“设置”页](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. 单击“配置”  项。

    ![“配置”边栏选项卡](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. 单击“下载”  按钮。

    ![下载](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. 更新服务配置文件后，上传并应用配置更新：

    ![上传](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. 选择 .cscfg 文件，并单击“确定”  。

## <a name="next-steps"></a>后续步骤

* 了解如何 [部署云服务](cloud-services-how-to-create-deploy-portal.md)。
* 配置 [自定义域名](cloud-services-custom-domain-name-portal.md)。
* [管理云服务](cloud-services-how-to-manage-portal.md)。
* 配置 [TLS/SSL 证书](cloud-services-configure-ssl-certificate-portal.md)。



