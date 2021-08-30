---
title: Azure 虚拟桌面部署应用程序 MSIX 应用附加 - Azure
description: 如何使用“MSIX 应用附加”为 Azure 虚拟桌面部署应用。
author: Heidilohr
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 929f69ca4503a48e8e8456111c85043cbf2db9f2
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730237"
---
# <a name="deploy-apps-with-msix-app-attach"></a>使用 MSIX 应用附加来部署应用

本文介绍如何使用 MSIX 应用附加功能在 Azure 虚拟桌面中发布应用程序。 在本文中，我们还将提供有关资源的链接，这些资源可以为你提供更深入的解释和说明。

## <a name="what-is-msix-app-attach"></a>什么是 MSIX 应用附加？

MSIX 应用连接是一种应用程序分层解决方案，使你能够在 Azure 虚拟桌面中将应用程序传送到活动用户会话。 MSIX 包系统将应用与操作系统分开，这样更易于为虚拟机构建映像。 借助 MSIX 包，还可以更好地控制用户在其虚拟机中可以访问哪些应用。 甚至可以将应用与主映像分开，稍后再将这些应用提供给用户。

要了解详细信息，请参阅[什么是 MSIX 引用附加？](../what-is-app-attach.md)。

## <a name="requirements"></a>要求

你将需要以下内容才能在 Azure 虚拟桌面中使用 MSIX 应用附加：

- MSIX 打包的应用程序
- 由扩展的 MSIX 应用程序生成的 MSIX 映像
- MSIX 共享，它是存储 MSIX 映像的网络位置
- 你将使用的主机池中至少有一个正常运行的活动会话主机
- 如果 MSIX 打包应用程序有一个专用证书，则该证书必须在主机池中的所有会话主机上可用
- 适用于 MSIX 应用附加的 Azure 虚拟桌面配置（用户分配、MSIX 应用程序与应用组的关联、将 MSIX 映像添加到主机池）

## <a name="create-an-msix-package-from-an-existing-installer"></a>通过现有安装程序创建 MSIX 包

要开始使用 MSIX 应用附加，需要将应用程序放在 MSIX 包中。 某些应用已采用 MSIX 格式，但如果使用的是 MSI、ClickOnce 等旧的安装程序，则需要将应用转换为 MSIX 包格式。 请参阅我们的 [MSIX 概述文章](/windows/msix/packaging-tool/create-an-msix-overview)，了解如何将现有应用转换为 MSIX 格式。

## <a name="test-the-application-fidelity-of-your-packaged-app"></a>测试打包应用的应用程序保真度 

将应用程序重新打包为 MSIX 包后，需确保应用程序的保真度很高。 应用保真度是指重新打包前后应用的行为和性能。 具有高保真度的应用包在重新打包前后其性能变化很小。

如果发现应用保真度在重新打包后下降，你的组织必须测试应用，以确保其性能符合用户标准。 如果不测试，可能需要更新应用程序以防止此问题或尝试重新打包。

## <a name="create-an-msix-image"></a>创建 MSIX 映像

接下来，将需要从打包的应用创建 MSIX 映像。 当你展开 MSIX 应用包并将生成的应用存储在 VHD(X) 或 CIM 存储中时，就会出现 MSIX 映像。 要了解如何创建 MSIX 映像，请参阅[创建 MSIX 映像](../app-attach-msixmgr.md#create-an-msix-image)。

## <a name="configure-an-msix-file-share"></a>配置 MSIX 文件共享

接下来，将需要设置 MSIX 网络共享来存储 MSIX 映像。 配置后，会话主机将使用 MSIX 共享将 MSIX 包附加到活动用户会话，并向用户提供应用。 请参阅[为 MSIX 应用附加设置文件共享](../app-attach-file-share.md)，了解如何设置 MSIX 共享。

## <a name="configure-msix-app-attach-for-azure-virtual-desktop-host-pool"></a>为 Azure 虚拟桌面主机池配置 MSIX 应用附加

将 MSIX 映像上传到 MSIX 共享后，需要打开 Azure 门户并配置要用于接受 MSIX 应用附加的主机池。 请参阅[使用 Azure 门户设置 MSIX 应用附加](../app-attach-azure-portal.md)，了解如何配置主机池。
