---
title: 如何创建具有共享资源的实验室 | Azure 实验室服务
description: 了解如何创建需要在学生间共享资源的实验室。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: d4bf4127dc163bb5f034e077b84664828374ba87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647964"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>如何在 Azure 实验室服务中创建具有共享资源的实验室

有时在创建课堂实验室时，可能需要在实验室中的所有学生间共享某些资源。  例如，有一个用于数据库类的授权服务器或 SQL Server。  本文将介绍为实验室启用共享资源的步骤。  还将讨论如何限制对该共享资源的访问权限。

## <a name="architecture"></a>体系结构

如下图中所示，有一个实验室，对应一个实验室帐户。  该实验室帐户将具有 VNet 对等互连设置，以便该实验室的虚拟网络可以连接到共享资源的网络。  在下图中，有两个虚拟网络，具有不重叠的 IP 范围。  这些 IP 范围都只是示例范围。  另请注意，共享资源虚拟网络与实验室帐户位于同一订阅中。

![具有共享资源体系结构的实验室服务](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>设置共享资源

必须先创建共享资源的虚拟网络，然后才能创建实验室。  有关如何创建虚拟网络的详细信息，请参阅[创建虚拟网络](../virtual-network/quick-create-portal.md)。  规划虚拟网络范围，使其与实验室计算机的 IP 地址不重叠，这一点很重要。  有关规划网络的详细信息，请参阅[计划虚拟网络](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。 在本示例中，共享资源所处的虚拟网络范围是 10.2.0.0/16。  如果尚未执行此操作，请[创建子网](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)以保存共享资源。  本示例使用的范围是 10.2.0.0/24，但实际范围可能有所不同，具体取决于具体网络需求。

共享资源可以是在虚拟机上运行的软件，也可以是 Azure 提供的服务。 应该可以通过专用 IP 地址访问共享资源。  通过设置共享资源仅可通过专用 IP 进行访问，即可限制对该共享资源的访问权限。

该图中还显示了网络安全组 (NSG)，其作用是限制来自学生 VM 的流量。  例如，可以编写一条安全规则，规定来自学生 VM 的 IP 地址的流量只能访问一个共享资源，而不能访问其他任何资源。  有关如何设置安全规则的详细信息，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md#work-with-security-rules)。 如果想要限制共享资源仅供特定实验室访问，请从[实验室帐户的实验室设置](manage-labs.md#view-labs-in-a-lab-account)中获取该实验室的 IP 地址，并将入站规则设置为仅允许来自该 IP 地址的访问。  不要忘记允许端口 49152 到 65535 用于该 IP 地址。  （可选）可以使用[虚拟机池](how-to-set-virtual-machine-passwords.md)页查找学生 VM 的专用 IP 地址。

如果共享资源是运行必要软件的 Azure 虚拟机，则可能需要修改此虚拟机的默认防火墙规则。

### <a name="tips-for-shared-resources---license-server"></a>适用于共享资源的提示 - 许可证服务器
一个更常见的共享资源是许可证服务器，下面是一些有关如何成功设置的提示。
#### <a name="server-region"></a>服务器区域
许可证服务器将需要连接到对等互连到实验室的虚拟网络，因此许可证服务器需要位于与实验室帐户相同的区域中。

#### <a name="static-private-ip-and-mac-address"></a>静态专用 IP 和 MAC 地址
默认情况下，虚拟机具有动态专用 IP，在[设置任何软件之前，先将专用 IP 设置为静态](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)。 这会将专用 IP 和 MAC 地址设置为静态。  

#### <a name="control-access"></a>控制访问
控制对许可证服务器的访问是关键所在。  VM 设置完成后，仍然需要具备一定的访问权限才能进行维护、故障排除和更新。  可通过多种不同的方式执行此操作。
- [在 Azure 安全中心中设置实时 (JIT) 访问。](../security-center/security-center-just-in-time.md?tabs=jit-config-asc%252cjit-request-asc)
- [设置网络安全组以限制访问。](../virtual-network/network-security-groups-overview.md)
- [设置 Bastion 以允许对许可证服务器进行安全访问。](https://azure.microsoft.com/services/azure-bastion/)

## <a name="lab-account"></a>实验室帐户

若要使用共享资源，必须将实验室帐户设置为使用[对等互连的虚拟网络](how-to-connect-peer-virtual-network.md)。  在这种情况下，将对等互连到共享资源所在的虚拟网络。

>[!WARNING]
>必须在实验室帐户对等互连到共享资源虚拟网络 **之后**，再创建用于课程的实验室。  
模板计算机

将实验室帐户对等互连到虚拟网络后，模板计算机现在应可以访问共享资源。  可能需要更新防火墙规则，具体取决于所访问的共享资源。