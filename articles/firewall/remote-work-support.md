---
title: Azure 防火墙远程工作支持
description: 本文介绍 Azure 防火墙如何支持远程工作人员需求。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 5112cf06952754eb1313166b82a9cf14876c797f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598263"
---
# <a name="azure-firewall-remote-work-support"></a>Azure 防火墙远程工作支持

Azure 防火墙是一种基于云的托管网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>虚拟桌面基础结构 (VDI) 部署支持

在家工作策略要求许多 IT 组织应对容量、网络、安全性和治理方面的根本性变化。 在家工作时，员工不受与本地服务相关的分层安全策略的保护。 Azure 上的虚拟桌面基础结构 (VDI) 部署可以帮助组织快速应对这种变化的环境。 但是，你需要通过某种方法来保护进出这些 VDI 部署的入站/出站 Internet 访问。 可以使用 Azure 防火墙 [DNAT 规则](rule-processing.md)及其基于[威胁情报](threat-intel.md)的筛选功能来保护 VDI 部署。

## <a name="azure-windows-virtual-desktop-support"></a>Azure Windows 虚拟桌面支持

Windows 虚拟桌面是在 Azure 中运行的综合性桌面和应用虚拟化服务。 这是唯一一种提供简化管理、多会话 Windows 10、对 Microsoft 365 企业应用版的优化以及对远程桌面服务 (RDS) 环境的支持的虚拟桌面基础结构 (VDI)。 只需几分钟即可在 Azure 上部署和缩放 Windows 桌面和应用，并获得内置的安全性和符合性功能。 Windows 虚拟桌面不需要你开放对虚拟网络的任何入站访问。 但是，你必须允许在虚拟网络中运行的 Windows 虚拟桌面虚拟机的一组出站网络连接。 有关详细信息，请参阅[使用 Azure 防火墙保护 Windows 虚拟桌面部署](protect-azure-virtual-desktop.md)。

## <a name="next-steps"></a>后续步骤

详细了解 [Windows 虚拟桌面](../virtual-desktop/index.yml)。