---
title: 支持 Azure 虚拟机中的32位操作系统 |Microsoft Docs
description: 有关 Azure 虚拟机支持的操作系统的信息
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 81b7efdd6bca0471719c11d130be95405f4d54e1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210182"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>Azure 虚拟机对 32 位操作系统的支持

Microsoft Azure 现在允许用户将其32位 Windows 操作系统带入 Azure。 仅支持专用 VHD，通用化映像在 Azure 中无效。 由于其中一些操作系统已达到其生命的可支持性协议的末尾，Microsoft 可能不会为其提供其他支持。 对于基于 Linux 或 Berkeley 的软件分发 (基于 BSD) 的操作系统（在 Microsoft Azure 虚拟机 (VM) 上运行），也不提供支持。

> [!NOTE]
> Azure 平台对运行32位操作系统的 Vm 施加了内存地址空间限制，在这种情况下，虚拟机上只有1GB 的内存可供 VM (*尤其是在 Win7 或 Win10) 的客户端 sku 上* ，而 vm 的其余部分将在来宾 VM 中显示为保留内存。 这是一个已知问题，我们目前没有用于修复的 ETA。 建议移动到64位操作系统版本。
> 

## <a name="more-information"></a>详细信息

有关 Azure 虚拟机支持的操作系统的详细信息，请参阅以下 Microsoft 知识库文章：

* [Microsoft Azure 虚拟机的 Microsoft 服务器软件支持](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)
* [Azure 对 Linux 和开放源代码技术的支持](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>参考

* [详细了解 Azure 中 Windows Server 2008/R2 的免费扩展安全更新](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [详细了解 Azure 对 Windows Server 2008 SP2 32 位专用映像的支持](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [详细了解对使用 Azure Site Recovery 将 Windows Server 2008 映像迁移到 Azure 的支持](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [详细了解 Azure 扩展支持的操作系统](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [详细了解如何在 Microsoft Azure 上运行 Windows Server 2003](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>后续步骤

如果在本文的任何位置需要更多帮助，请联系 MSDN Azure 上的 Azure 专家 [并 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)。

或者，提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
