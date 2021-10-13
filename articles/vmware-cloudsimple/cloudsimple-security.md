---
title: Azure VMware Solution by CloudSimple - CloudSimple 服务的安全性
description: 描述 CloudSimple 服务安全性的共享责任模型
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c846b07c5409dba778e758b14caceb061ae2986
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612363"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple 安全性概述

本文概述了在 Azure VMware Solution by CloudSimple 服务、基础结构和数据中心的安全性实现原理。 了解数据保护和安全性、网络安全以及如何管理漏洞和补丁。

## <a name="shared-responsibility"></a>共担责任

Azure VMware Solution by CloudSimple 使用共享责任模型来实现安全性。 云中的可信安全性通过客户和 Microsoft 作为服务提供商共享责任而实现。 此责任矩阵提供更高的安全性，并消除了单一故障点。

## <a name="azure-infrastructure"></a>Azure 基础结构

Azure 基础结构安全注意事项包括数据中心和设备位置。

### <a name="datacenter-security"></a>数据中心安全性

Microsoft 专门设立了一个完整的部门来设计、构建和运营支持 Azure 的物理设施。 此团队在维持一流物理安全性方面投入了大量的人力物力。 有关物理安全性的详细信息，请参阅[Azure 设施、本地和物理安全性](../security/fundamentals/physical-security.md)。

### <a name="equipment-location"></a>设备位置

运行私有云的裸机硬件设备托管在 Azure 数据中心位置。  设备所在的机舱需要通过基于生物识别的双因素身份验证才能访问。

## <a name="dedicated-hardware"></a>专用硬件

作为 CloudSimple 服务的一部分，所有 CloudSimple 客户都将获得具有本地附加磁盘的专用裸机主机，这些主机与其他租户的硬件相互物理隔离。 在每个节点上运行包含 vSAN 的 ESXi 虚拟机监控程序。 节点通过客户专用 VMware vCenter 和 NSX 进行管理。 不在租户之间共享硬件还提供了额外的隔离层和安全保护层。

## <a name="data-security"></a>数据安全性

客户拥有其数据的控制和所有权。 对客户数据进行数据管理是客户的责任。

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>对内部网络中的静态数据和动态数据进行数据保护

对于私有云环境中的静态数据，可以使用 vSAN 加密。 vSAN 加密适用于自己虚拟网络中或本地的 VMware 认证外部密钥管理服务器 (KMS)。  可以自行控制数据加密密钥。 对于私有云中的动态数据，vSphere 支持对所有 vmkernel 流量（包括 vMotion 流量）进行有线数据加密。

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>对需要通过公用网络移动的数据进行数据保护

若要保护通过公用网络移动的数据，可以为私有云创建 IPsec 和 TLS VPN 隧道。 支持通用加密方法，其中包括 128 字节和 256 字节的 AES。 传输中的数据（包括身份验证、管理访问和客户数据）使用标准加密机制（SSH、TLS 1.2 和安全 RDP）进行加密。 传输敏感信息的通信使用标准加密机制。

### <a name="secure-disposal"></a>安全处置

如果 CloudSimple 服务过期或终止，你负责移除或删除数据。 CloudSimple 将与你合作来删除或返回客户协议中提供的所有客户数据，但适用法律要求 CloudSimple 保留部分或全部个人数据的情况除外。 如果需要保留任何个人数据，CloudSimple 将对数据进行存档并执行合理的措施，以防止客户数据被进一步处理。

### <a name="data-location"></a>数据位置

设置私有云时，请选择要在其中部署云的 Azure 区域。 除非执行数据迁移或场外数据备份，否则不会从该物理数据中心移动 VMware 虚拟机数据。 需要的话，还可以在多个 Azure 区域内托管工作负载并存储数据。

如果租户管理员未执行明确操作，则位于私有云超聚合节点中的客户数据不会遍历位置。 你需要负责以高度可用的方式实现工作负载。

### <a name="data-backups"></a>数据备份

CloudSimple 不会备份或存档客户数据。 CloudSimple 会定期备份 vCenter 和 NSX 数据，以提供管理服务器的高可用性。 在备份之前，将使用 VMware API 在 vCenter 源对所有数据进行加密。 加密的数据会被传输并存储在 Azure blob 中。 备份加密密钥存储于高度安全的 CloudSimple 托管保管库中，该保管库运行于 Azure 中的 CloudSimple 虚拟网络。

## <a name="network-security"></a>网络安全

CloudSimple 解决方案依赖于网络安全层。

### <a name="azure-edge-security"></a>Azure 边缘安全

CloudSimple 服务基于 Azure 提供的基本网络安全性。 Azure 应用深层防御技术来检测和及时响应与异常流入量或流出量模式相关的网络攻击，以及分布式拒绝服务 (DDoS) 攻击。 此安全控制适用于私有云环境和 CloudSimple 开发的控制平面软件。

### <a name="segmentation"></a>分段

CloudSimple 服务在逻辑上分离了第 2 层网络，限制了在私有云环境中访问自己的专用网络。 用户可以使用防火墙进一步保护私有云网络。 通过 CloudSimple 门户，可以为所有网络流量定义 EW 和 NS 网络流量控制规则，包括私有云内部流量、私有云间流量、访问 Internet 的一般流量以及通过 IPsec VPN 或 ExpressRoute 连接访问本地的网络流量。

## <a name="vulnerability-and-patch-management"></a>漏洞和补丁管理

CloudSimple 负责定期对托管 VMware 软件（ESXi、vCenter 和 NSX）进行安全修补。

## <a name="identity-and-access-management"></a>标识和访问管理

客户可选择首选多重身份验证或 SSO 对 Azure 帐户（在 Azure AD 中）进行身份验证。 在 Azure 门户中，无需重新输入凭据，即可启动 CloudSimple 门户。

CloudSimple 支持私有云 vCenter 的身份源的可选配置。 用户可以使用[本地身份源](set-vcenter-identity.md)、私有云的新身份源或 [Azure AD](azure-ad.md)。

默认情况下，将向客户提供对私有云中 vCenter 进行日常操作所需的权限。 此权限级别不包括对 vCenter 的管理访问权限。 如果临时需要管理访问权限，用户可以在完成管理任务时，在有限的时间内[升级权限](escalate-private-cloud-privileges.md)。
