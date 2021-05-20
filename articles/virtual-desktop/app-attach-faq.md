---
title: Windows 虚拟桌面 MSIX 应用附加常见问题解答 - Azure
description: 有关 Windows 虚拟桌面 MSIX 应用附加的常见问题解答。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 395c274630131c2ae5f451443913e1e69c7c422a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738695"
---
# <a name="msix-app-attach-faq"></a>MSIX 应用附加常见问题解答

本文解答有关 Windows 虚拟桌面 MSIX 应用附加的常见问题。

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>MSIX 与 MSIX 应用附加有何区别？

MSIX 是应用的打包格式，而 MSIX 应用附加是将 MSIX 包提供给部署的功能。

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX 应用附加是否使用 FSLogix？

MSIX 应用附加不使用 FSLogix。 但是，MSIX 应用附加和 FSLogix 旨在协同工作以提供无缝的用户体验。

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>能否在 Windows 虚拟桌面外部使用 MSIX 应用附加功能？

支持 MSIX 应用附加的 API 适用于 Windows 10 企业版。 这些 API 可在 Windows 虚拟桌面外部使用。 但是，在 Windows 虚拟桌面外部没有用于 MSIX 应用附加的管理平面。

## <a name="how-do-i-get-an-msix-package"></a>如何获取 MSIX 包？

MSIX 包由软件供应商提供。 还可以将非 MSIX 包转换为 MSIX 包。 在[如何将现有安装程序移动到 MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix) 了解详细信息。

## <a name="which-operating-systems-support-msix-app-attach"></a>哪些操作系统支持 MSIX 应用附加？

Windows 10 企业版和 Windows 10 企业版多会话版本 2004 或更高版本。

## <a name="is-msix-app-attach-currently-generally-available"></a>MSIX 应用附加当前是否已正式发布？

MSIX 应用附加是 Windows 10 企业版和 Windows 10 企业版多会话版本 2004 或更高版本的一部分。 这两种操作系统当前均已正式发布。 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>能否在 Windows 虚拟桌面外部使用 MSIX 应用附加？

MSIX 和 MSIX 应用附加 API 是 Windows 10 企业版和 Windows 10 企业版多会话版本 2004 及更高版本的一部分。 目前，我们未在 Windows 虚拟桌面外部提供用于 MSIX 应用附加的管理软件。

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>能否同时运行同一应用程序的两个版本？

若要同时运行同一 MSIX 应用程序的两个版本，必须为每个应用程序在 appxmanifest.xml 文件中定义不同的 MSIX 包系列。

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>使用 MSIX 应用附加时，我是否应禁用自动更新？

是。 MSIX 应用附加不支持 MSIX 应用程序自动更新。

## <a name="how-do-permissions-work-with-msix-app-attach"></a>如何配合使用权限和 MSIX 应用附加？

主机池中使用 MSIX 应用附加的所有虚拟机 (VM) 都必须具有对存储 MSIX 映像的文件共享的读取权限。 如果它还使用 Azure 文件存储，则这些虚拟机还需要被授予基于角色的访问控制 (RBAC) 和新技术文件系统 (NTFS) 权限。

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>能否将 MSIX 应用附加用于 HTTP 或 HTTPS？

目前不支持将 MSIX 应用附加用于 HTTP 或 HTTPS。

## <a name="can-i-restage-the-same-msix-application"></a>能否重新暂存同一 MSIX 应用程序？

是。 可以重新暂存已重新暂存的应用程序，这不会导致任何错误。

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>MSIX 应用附加是否支持自签名证书？

是。 需要在使用 MSIX 应用附加来托管自签名应用程序的所有会话主机 VM 上安装自签名证书。


## <a name="next-steps"></a>后续步骤

如果想了解有关 MSIX 应用程序附加的详细信息，请查看我们的[概述](what-is-app-attach.md)和[术语表](app-attach-glossary.md)。 或者直接开始[安装应用附加](app-attach.md)。
