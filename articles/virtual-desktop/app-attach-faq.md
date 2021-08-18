---
title: Azure 虚拟桌面 MSIX 应用附加常见问题解答 - Azure
description: 有关 Azure 虚拟桌面 MSIX 应用附加的常见问题解答。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 487d1516e82f856f6c42188248b698b432117906
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112201453"
---
# <a name="msix-app-attach-faq"></a>MSIX 应用附加常见问题解答

本文解答有关 Azure 虚拟桌面 MSIX 应用附加的常见问题。

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>MSIX 与 MSIX 应用附加有何区别？

MSIX 是应用的打包格式，而 MSIX 应用附加是将 MSIX 包提供给部署的功能。

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX 应用附加是否使用 FSLogix？

MSIX 应用附加不使用 FSLogix。 但是，MSIX 应用附加和 FSLogix 旨在协同工作以提供无缝的用户体验。

## <a name="can-i-use-the-msix-app-attach-outside-of-azure-virtual-desktop"></a>能否在 Azure 虚拟桌面外部使用 MSIX 应用附加功能？

支持 MSIX 应用附加的 API 适用于 Windows 10 企业版。 这些 API 可在 Azure 虚拟桌面外部使用。 但是，在 Azure 虚拟桌面外部没有用于 MSIX 应用附加的管理平面。

## <a name="how-do-i-get-an-msix-package"></a>如何获取 MSIX 包？

MSIX 包由软件供应商提供。 还可以将非 MSIX 包转换为 MSIX 包。 在[如何将现有安装程序移动到 MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix) 了解详细信息。

## <a name="which-operating-systems-support-msix-app-attach"></a>哪些操作系统支持 MSIX 应用附加？

Windows 10 企业版和 Windows 10 企业版多会话版本 2004 或更高版本。

## <a name="is-msix-app-attach-currently-generally-available"></a>MSIX 应用附加当前是否已正式发布？

MSIX 应用附加是 Windows 10 企业版和 Windows 10 企业版多会话版本 2004 或更高版本的一部分。 这两种操作系统当前均已正式发布。 

## <a name="can-i-use-msix-app-attach-outside-of-azure-virtual-desktop"></a>能否在 Azure 虚拟桌面外部使用 MSIX 应用附加功能？

MSIX 和 MSIX 应用附加 API 是 Windows 10 企业版和 Windows 10 企业版多会话版本 2004 及更高版本的一部分。 目前，我们未在 Azure 虚拟桌面外部提供用于 MSIX 应用附加的管理软件。

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>能否同时运行同一应用程序的两个版本？

若要同时运行同一 MSIX 应用程序的两个版本，必须为每个应用程序在 appxmanifest.xml 文件中定义不同的 MSIX 包系列。

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>使用 MSIX 应用附加时，我是否应禁用自动更新？

是。 MSIX 应用附加不支持 MSIX 应用程序自动更新。

## <a name="how-do-permissions-work-with-msix-app-attach"></a>如何配合使用权限和 MSIX 应用附加？

主机池中使用 MSIX 应用附加的所有虚拟机 (VM) 都必须具有对存储 MSIX 映像的文件共享的读取权限。 如果它还使用 Azure 文件存储，则这些虚拟机还需要被授予基于角色的访问控制 (RBAC) 和新技术文件系统 (NTFS) 权限。

## <a name="how-many-users-can-use-an-msix-image-handle"></a>有多少用户可以使用 MSIX 映像句柄？

MSIX 应用附加基于每台计算机（而不是基于每个用户）装载 MSIX 映像。 可以使用 MSIX 映像句柄的用户数量取决于计算机文件系统的大小和网络吞吐量。 此外，Azure 文件存储的每个文件最多只能有 2,000 个打开句柄。 

## <a name="can-i-use-azure-active-directory-domain-services-azure-ad-ds-with-msix-app-attach"></a>能否将 Azure Active Directory 域服务 (Azure AD DS) 与 MSIX 应用附加一起使用？

MSIX 应用附加目前不支持 Azure AD DS。 由于 Azure AD DS 计算机对象不会同步到 Azure Active Directory (Azure AD)，因此管理员无法为 Azure 文件存储提供所需的基于角色的访问控制 (RBAC) 权限。

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>能否将 MSIX 应用附加用于 HTTP 或 HTTPS？

目前不支持将 MSIX 应用附加用于 HTTP 或 HTTPS。

## <a name="can-i-restage-the-same-msix-application"></a>能否重新暂存同一 MSIX 应用程序？

是。 可以重新暂存已重新暂存的应用程序，这不会导致任何错误。

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>MSIX 应用附加是否支持自签名证书？

是。 需要在使用 MSIX 应用附加来托管自签名应用程序的所有会话主机 VM 上安装自签名证书。 若要了解如何创建自签名证书，请阅读[为包签名创建证书](/windows/msix/package/create-certificate-package-signing)。

## <a name="what-applications-can-i-repackage-to-msix"></a>哪些应用程序可以重新打包到 MSIX？

每个应用程序使用不同的 OS 功能、编程语言和框架。 若要重新打包应用程序，请按照[如何将现有安装程序移动到 MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix) 中的说明进行操作。 在[准备打包桌面应用程序](/windows/msix/desktop/desktop-to-uwp-prepare)中可以找到重新打包应用程序所需的内容列表。 

某些应用程序无法进行分层，这意味着它们无法重新打包到 MSIX 文件中。 下面是无法重新打包的应用程序的列表：

- 驱动程序 
- Active-X 或 Silverlight
- VPN 客户端
- 防病毒程序

## <a name="how-many-misx-applications-can-i-add-to-each-session-host"></a>可以向每个会话主机添加多少个 MISX 应用程序？

每个会话主机的 CPU、内存和 OS 都有不同的限制。 超过这些限制可能会影响应用程序性能和总体用户体验。 但是 MSIX 应用附加本身对它可以使用的应用程序数量没有限制。

## <a name="how-many-vhd-or-vhdx-files-can-i-mount-on-a-host-pool"></a>可以在一个主机池上装载多少个 .VHD 或 .VHDX 文件？

MSIX 应用附加本身对能装载的文件数量没有限制。 但是，主机池本身可能会受到以下因素的限制：

- OS 处理已装载卷的能力。
- 存储解决方案或文件系统可保存的最大打开文件数。
- 主机池的会话主机内存和 CPU 使用率。

换句话说，主机池的限制与在本地安装和运行应用时的限制相同。

## <a name="next-steps"></a>后续步骤

如果想了解有关 MSIX 应用程序附加的详细信息，请查看我们的[概述](what-is-app-attach.md)和[术语表](app-attach-glossary.md)。 或者直接开始[安装应用附加](app-attach.md)。
