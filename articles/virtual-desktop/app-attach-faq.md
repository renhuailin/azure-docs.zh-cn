---
title: Windows 虚拟桌面 .MSIX 应用附加常见问题解答-Azure
description: 有关 Windows 虚拟桌面的 .MSIX 应用附加的常见问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 78c21e9515965f1c15315967f6a19a0df2838dc2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591189"
---
# <a name="msix-app-attach-faq"></a>.MSIX 应用附加常见问题解答

本文解答了有关 Windows 虚拟桌面的 .MSIX 应用附加的常见问题。

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>.MSIX 和 .MSIX 应用附加之间的区别是什么？

.MSIX 是应用的打包格式，而 .MSIX 应用附加是将 .MSIX 包提供给部署的功能。

## <a name="does-msix-app-attach-use-fslogix"></a>.MSIX 应用附加是否使用 FSLogix？

.MSIX 应用附加不使用 FSLogix。 不过，.MSIX 应用附加和 FSLogix 旨在提供无缝的用户体验。

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>能否在 Windows 虚拟桌面之外使用 .MSIX 应用附加功能？

适用于 power .MSIX 应用附加的 Api 适用于 Windows 10 企业版。 这些 Api 可在 Windows 虚拟桌面之外使用。 但是，在 Windows 虚拟桌面外，.MSIX 应用附加的管理平面不存在。

## <a name="how-do-i-get-an-msix-package"></a>如何实现获取 .MSIX 包？

你的软件供应商将为你提供 .MSIX 包。 还可以将非 .MSIX 包转换为 .MSIX。 详细了解 [如何将现有安装程序移动到 .msix](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)。

## <a name="which-operating-systems-support-msix-app-attach"></a>哪些操作系统支持 .MSIX 应用附件？

Windows 10 企业版和 Windows 10 企业版多会话版本2004或更高版本。

## <a name="is-msix-app-attach-currently-generally-available"></a>.MSIX 应用附加当前是否已公开发布？

.MSIX 应用附加属于 Windows 10 企业版和 Windows 10 企业版多会话版本2004或更高版本。 这两个操作系统当前均已公开发布。 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>能否在 Windows 虚拟桌面之外使用 .MSIX 应用连接？

.MSIX 和 .MSIX 应用附加 Api 是 Windows 10 企业版和 Windows 10 企业版多会话版本2004及更高版本的一部分。 目前，我们不会在 Windows 虚拟桌面之外提供适用于 .MSIX 应用的管理软件。

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>是否可以同时运行同一应用程序的两个版本？

对于同一 .MSIX 应用程序的两个版本同时运行，每个应用程序的 appxmanifest.xml 文件中定义的 .MSIX 包系列必须不同。

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>使用 .MSIX 应用附加时，是否应禁用自动更新？

是。 .MSIX 应用附加不支持 .MSIX 应用程序的自动更新。

## <a name="how-do-permissions-work-with-msix-app-attach"></a>如何对 .MSIX 应用附加功能使用权限？

使用 .MSIX 应用附加的主机池中 (Vm) 的所有虚拟机都必须对存储 .MSIX 映像的文件共享具有读取权限。 如果它也使用 Azure 文件，则需要向他们授予基于角色的访问控制 (RBAC) 和新技术文件系统 (NTFS) 权限。

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>能否对 HTTP 或 HTTPs 使用 .MSIX 应用连接？

作为使用 .MSIX 应用附加的主机池的一部分的所有 Vm 必须对存储 .MSIX 映像的文件共享具有读取权限。 如果正在使用 Azure 文件，则必须授予 RBAC 和 NTFS 权限。

## <a name="can-i-restage-the-same-msix-application"></a>我能 restage 同一个 .MSIX 应用程序吗？

是。 您可以 restage 已预留的应用程序，这不会导致任何错误。

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>.MSIX 应用附加是否支持自签名证书？

当前不支持通过 HTTP 或 HTTPs 使用 .MSIX 应用附加。


## <a name="next-steps"></a>后续步骤

若要了解有关 .MSIX 应用附加的详细信息，请参阅 [概述](what-is-app-attach.md) 和 [术语表](app-attach-glossary.md)。 否则，开始 [安装应用程序](app-attach.md)。
