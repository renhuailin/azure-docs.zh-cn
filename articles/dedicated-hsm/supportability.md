---
title: 支持 - Azure 专用 HSM | Microsoft Docs
description: 在不同方案中支持 Azure 专用 HSM 的选项和责任范围
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cd949bdb7c489478df6a16d6dccd0bf358637604
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606923"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure 专用 HSM 的支持

Azure 专用 HSM 服务提供供单个客户使用的物理设备，由客户对设备进行完全的管理控制并承担管理责任。 所提供的设备是 [Thales Luna 7 HSM 型号 A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms)。 在客户进行预配之后，Microsoft 没有任何管理访问权限，但可以连接一个充当监视角色的物理串行端口。  由于没有访问权限，Microsoft 不能承担软件级别的持续维护或系统管理责任。 因此，典型的操作活动由客户负责。
客户对使用 HSM 的应用程序完全负责，并应与 Thales 密切合作来获取支持或咨询帮助。 考虑到客户对操作安全机制的责任程度，Microsoft 不可能为此服务提供任何类型的高可用性保证。 客户负责确保其应用程序得到正确的配置，以实现高可用性。 Microsoft 会监视并维护设备运行状况和网络连接。

## <a name="getting-support"></a>获取支持

专用 HSM 的客户支持由 Microsoft 与 Thales 共同提供。 Microsoft 将负责解决任何硬件问题或网络路径问题，而有关配置、软件、固件和应用程序开发等实际 HSM 的任何问题，将由 Thales 负责解决。 这种支持模式可确保以最快的速度获得最有效的支持。 如果对特定问题存有疑问，请向 Microsoft 提出支持请求，我们将确保你得到适当的指导。 Microsoft 将继续参与所有支持方案，力求为客户提供最佳支持体验。

## <a name="thales-support"></a>Thales 支持

使用专用 HSM 服务的客户有资格根据其 Plus 支持计划获取 Thales 支持。 只需按照 Thales 支持门户的流程进行注册即可。 为此，我们将在初次与 Microsoft 接洽过程中提供客户 ID 和说明，以获取对专用 HSM 服务的访问权限。 从 Thales 获得支持的机制是[客户支持门户](https://supportportal.thalesgroup.com/csm)。
重要的一点是，Thales 将通过从客户支持门户下载的方式提供使用 HSM 所需的所有软件和文档（例如客户端访问软件和 SDK）。

### <a name="software-components"></a>软件组件

HSM 设备的配置中使用各种软件组件：

* 客户端软件
* SDK 中 IsInRole 中的声明
* 工具

### <a name="guidance"></a>指南

Thales 通过 [Thales 客户支持门户](https://supportportal.thalesgroup.com/csm)提供管理和配置指导。 使用有效的客户 ID 登录后，可以下载这些文档。 Thales 还提供一系列集成指导，以帮助客户实现不同的场景和软件集成。 有关详细信息，请参阅 [Microsoft 的 Thales 合作伙伴站点](https://cpl.thalesgroup.com/partners/overview)。

### <a name="support"></a>支持

与在专用 HSM 服务中使用 HSM 相关的任何软件级问题都应该直接转递给 Thales 支持部门。 以上所列的所有软件组件，以及需要后期预配的任何自定义 HSM 配置问题都将由 Thales 解决。 有关详细信息，请参阅 [Thales 客户支持门户](https://supportportal.thalesgroup.com/csm)。

### <a name="consulting-services"></a>咨询服务

对于使用 HSM 的自定义应用程序的设计、开发和部署，如需任何帮助，请联系 Thales 客户代表。

## <a name="microsoft-support"></a>Microsoft 支持

Microsoft 将确保物理 HSM 设备可访问网络且处于正常运行状态，仅供单个客户使用。 设备的配置和管理由客户负责。 Microsoft 的责任包括：

* 确保为设备提供电源和散热装置
* 保持 HSM 的正常运行状态（例如，中断/修复方案）
* 可通过网络访问设备。

如下所述的问题应向 Microsoft 汇报：

* 组件故障
* 整个设备发生故障
* 网络访问问题
* 预配和取消预配问题

Microsoft 可以通过监视角色（非管理角色）使用物理串行端口来访问设备，以获取基本的运行状况遥测数据。  这样，在出现问题时，Microsoft 便可以向客户提供主动通知，但如果客户选择限制此权限，则 Microsoft 做不到这一点。 

### <a name="provisioning-and-decommissioning"></a>预配和解除

客户获取专用 HSM 服务的已批准注册后，可以创建 HSM 资源（目前可以通过 PowerShell 或命令行接口来创建，但不能使用 Azure 门户）。 资源将经历一个分配过程，该过程将指定区域中的物理设备映射到客户的预定义虚拟网络 (VNet)。 设备在 VNet 中出现后，客户可以访问该设备，并按要求对其进一步进行配置。 客户使用 Thales 客户端软件和工具访问其专用 HSM。 在资源创建过程中，Microsoft 将提供支持。 自定义配置过程和其他活动由 Thales 提供支持。 （请参阅上述的 Thales 支持）。 客户用完 HSM 后，必须将其重置（或归零），以确保不会保存任何数据。 设备重置过程会删除所有自定义配置和数据。 Microsoft 会解除分配该设备，并将其以原始状态返回到池中。 这意味着，将设备返回到池后，不存在以前的客户活动的证据。 

### <a name="hardware-issues"></a>硬件问题

HSM 设备带有冗余且可更换的电源和风扇装置。  但是，移除风扇装置仍会导致篡改事件。 发生组件故障时，Microsoft 将采用最适当的过程来解决组件级问题，并尽最大努力减少对客户服务可用性造成中断和风险。
如果发生更严重的设备故障，Microsoft 将使用可用池中的全新设备来更换故障设备。 客户只需将新设备加入到现有的高可用性对中，即可让其同步并恢复完全正常运行状态。 将拆除故障设备上的数据保存装置，然后在数据中心现场粉碎该设备。 

### <a name="networking-issues"></a>网络问题

如果客户在访问 HSM 设备时遇到网络问题，应联系 Microsoft 支持部门。 使用 SSH 连接到 HSM 设备可以方便地测试网络访问。 如果连接失败，请联系 Microsoft 支持部门。

## <a name="service-level-expectations-for-support"></a>支持的服务级别期望

有关 Microsoft 支持服务级别，请参阅 [Azure 支持计划](https://azure.microsoft.com/support/plans/)。
有关 Thales 支持服务级别，请参阅 [Thales 支持概要](https://azure.microsoft.com/support/plans/)。

## <a name="next-steps"></a>后续步骤

建议在进行设备预配和应用程序设计或部署之前，深刻理解高可用性和安全性等重要概念。

* [部署体系结构](deployment-architecture.md)
* [高可用性](high-availability.md)
* [物理安全性](physical-security.md)
* [网络](networking.md)

