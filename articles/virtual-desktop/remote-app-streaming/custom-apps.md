---
title: Azure 虚拟桌面主机自定义应用 - Azure
description: 如何使用 Azure 虚拟桌面为自定义应用提供服务。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 35e07fad22760e6c8c87e60f77cd6d98e5db42a2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798730"
---
# <a name="how-to-host-custom-apps-with-azure-virtual-desktop"></a>如何使用 Azure 虚拟桌面托管自定义应用

Azure 虚拟桌面可以托管多种类型的 Windows 应用程序。 建议根据计划用于部署应用的应用包的类型来准备应用。 本文将介绍针对每种应用包类型需要执行的操作。 

>[!NOTE]
>建议在多会话主机上托管应用。 此外，还建议测试应用，以确保它们按预期在多会话主机上运行。 例如，运行测试以确定同一会话主机上的两个或多个用户是否能同时成功运行应用。

## <a name="msix"></a>MSIX

建议使用 MSIX 包类型在 Azure 虚拟桌面中部署自定义应用，因为它们可以利用此服务内置的 [MSIX 应用附加功能](../app-attach-glossary.md)。 若要了解如何以 MSIX 格式重新打包现有的 Win32 应用程序，请访问[以 MSIX 格式重新打包现有 Win32 应用程序](/windows/application-management/msix-app-packaging-tool)。

以 MSIX 格式打包应用后，可以使用 Azure 虚拟桌面的 MSIX 应用附加功能将应用传送给客户。 请参阅[使用 MSIX 应用附加来部署应用](msix-app-attach.md)，了解如何针对应用使用 MSIX 应用附加功能。

## <a name="other-options-for-win32-applications"></a>针对 Win32 应用程序的其他选项

通过使用以下选项，还可以将 Win32 应用程序提供给用户，而无需以 MSIX 格式重新打包这些应用。

### <a name="include-the-application-manually-on-session-hosts"></a>在会话主机上手动添加应用程序

按照[准备和自定义主 VHD 映像](../set-up-customize-master-image.md)中的说明，将应用作为供虚拟机使用的 Windows 映像的一部分进行添加。 更具体地说，按照[其他应用程序和注册表配置](../set-up-customize-master-image.md#other-applications-and-registry-configuration)部分中的说明，为所有用户安装应用程序。

### <a name="use-microsoft-endpoint-manager-to-deploy-the-application-at-scale"></a>使用 Microsoft Endpoint Manager 大规模部署应用程序

如果使用 Microsoft Endpoint Manager 管理会话主机，可以按照[在 Windows 10 设备上安装应用](/mem/intune/apps/apps-windows-10-app-deploy#install-apps-on-windows-10-devices)中的说明来部署应用程序。 请确保在“设备上下文”模式下将应用部署到所有会话主机，以确保部署中的所有用户都可以访问该应用程序。

### <a name="manual-installation"></a>手动安装

不建议手动安装应用，因为这需要为每个会话主机重复此过程。 此方法更常由 IT 专业人员用于测试目的。

如果必须手动安装应用，则需要在设置 Azure 虚拟桌面主机池后，使用管理员帐户远程访问会话主机。 然后，如同在物理 PC 上一样安装应用程序。 需要重复此过程，以在主机池的每个会话主机上安装应用程序。

>[!NOTE]
>如果安装过程提供了为所有用户安装该应用程序的选项，请选择此选项。

## <a name="microsoft-store-applications"></a>Microsoft Store 应用程序

目前不建议使用 Microsoft Store 应用在 Azure 虚拟桌面中进行远程应用流式处理。

## <a name="next-steps"></a>后续步骤

若要了解如何使用 MSIX 应用附加功能来打包和部署应用，请参阅[使用 MSIX 应用附加来部署应用](msix-app-attach.md)。