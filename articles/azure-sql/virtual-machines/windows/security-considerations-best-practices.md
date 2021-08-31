---
title: 安全注意事项 | Microsoft Docs
description: 本主题提供有关保护 Azure 虚拟机中运行的 SQL Server 的常规指南。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 39ef6e17e07833fede323ace8d06fd8b767eafac
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435481"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机中 SQL Server 的安全注意事项
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本主题包括总体安全指南，可帮助建立对 Azure 虚拟机 (VM) 中 SQL Server 实例的安全访问。

Azure 遵守多个行业法规和标准，使用户能够使用虚拟机中运行的 SQL Server 生成符合规定的解决方案。 有关 Azure 合规性的信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/)。

除了本主题所述做法外，建议你在借鉴传统本地安全做法和虚拟机安全最佳做法的基础上回顾并实施安全最佳做法。 

## <a name="azure-defender-for-sql"></a>Azure Defender for SQL 

[Azure Defender for SQL](../../../security-center/defender-for-sql-introduction.md) 支持 Azure 安全中心安全功能，例如漏洞评估和安全警报。 请参阅[启用 Azure Defender for SQL](../../../security-center/defender-for-sql-usage.md) 了解详细信息。 

## <a name="portal-management"></a>门户管理

[向 SQL IaaS 扩展注册 SQL Server VM](sql-agent-extension-manually-register-single-vm.md) 后，可以在 Azure 门户中使用 [SQL 虚拟机资源](manage-sql-vm-portal.md)配置许多安全设置，例如启用 Azure Key Vault 集成或 SQL 身份验证。 

此外，启用 [Azure Defender for SQL](../../../security-center/defender-for-sql-usage.md) 后，可以直接在 Azure 门户的 [SQL 虚拟机资源](manage-sql-vm-portal.md)中查看安全中心功能，例如漏洞评估和安全警报。 

请参阅[在门户中管理 SQL Server VM](manage-sql-vm-portal.md) 了解详细信息。 

## <a name="azure-key-vault-integration"></a>Azure 密钥保管库集成 

SQL Server 加密功能多种多样，包括透明数据加密 (TDE)、列级加密 (CLE) 和备份加密。 这些加密形式要求管理和存储用于加密的加密密钥。 Azure 密钥保管库服务专用于在一个高度可用的安全位置改进这些密钥的安全性和管理。 SQL Server 连接器使 SQL Server 能够使用 Azure Key Vault 中的这些密钥。
如需了解更全面详尽的信息，请参阅本系列中的其他文章：[清单](performance-guidelines-best-practices-checklist.md)、[VM 大小](performance-guidelines-best-practices-vm-size.md)、[存储](performance-guidelines-best-practices-storage.md)、[HADR 配置](hadr-cluster-best-practices.md)和[收集基线](performance-guidelines-best-practices-collect-baseline.md)。 

请参阅 [Azure Key Vault 集成](azure-key-vault-integration-configure.md)以了解详细信息。


## <a name="access-control"></a>访问控制 

创建 SQL Server 虚拟机时，请考虑如何谨慎控制有权访问虚拟机和 SQL Server 的人员。 通常，应执行以下操作：

- 仅限对此有需要的应用程序和客户端拥有 SQL Server 访问权限。
- 遵循管理用户帐户和密码的最佳做法。

在考虑这些要点时，以下部分提供了相关建议。

## <a name="secure-connections"></a>安全连接

创建具有库映像的 SQL Server 虚拟机时，“SQL Server 连接”选项提供以下选择：“本地（VM 内）”、“专用（虚拟网络内）”或“公共 (Internet)”。

![SQL Server 连接](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

为了达到最佳安全性，请为方案选择最严格的选项。 例如，如果正在运行访问同一 VM 上的 SQL Server 的应用程序，则“本地”选项最安全。 如果正在运行需要访问 SQL Server 的 Azure 应用程序，则选择“专用”选项可确保与 SQL Server 的通信仅在指定的 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)内安全地进行。 如果需要使用“公共(Internet)”选项访问 SQL Server VM，请确保遵照本主题中的其他最佳做法，以减小受攻击面。

在门户中选择的选项使用 VM [网络安全组](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) (NSG) 上的入站安全规则来允许或拒绝发往虚拟机的网络流量。 可修改或创建新的入站 NSG 规则，允许到 SQL Server 端口（默认为 1433）的流量。 还可指定允许通过此端口进行通信的特定 IP 地址。

![网络安全组规则](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

除了使用 NSG 规则限制网络流量外，还可以对虚拟机使用 Windows 防火墙。

如果通过经典部署模型使用终结点，不使用它们时，请删除虚拟机上的所有终结点。 有关在终结点上使用 ACL 的说明，请参阅[管理终结点上的 ACL](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)。 对于使用 Azure 资源管理器的 VM 来说，无需执行此操作。

最后，考虑为 Azure 虚拟机中的 SQL Server 数据库引擎实例启用加密连接。 使用签名证书配置 SQL Server 实例。 有关详细信息，请参阅[启用到数据库引擎的加密连接](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)和[连接字符串语法](/dotnet/framework/data/adonet/connection-string-syntax)。

## <a name="encryption"></a>Encryption

托管磁盘提供服务器端加密和 Azure 磁盘加密。 [服务器端加密](../../../virtual-machines/disk-encryption.md)提供静态加密并保护数据，让你的组织能够信守安全性与合规性方面所做的承诺。 [Azure 磁盘加密](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md)使用 BitLocker 或 DM-Crypt 技术并与 Azure Key Vault 集成，可对 OS 磁盘和数据磁盘进行加密。 

## <a name="non-default-port"></a>非默认端口

默认情况下，SQL Server 侦听已知端口 1433。 为了提高安全性，请将 SQL Server 配置为侦听 1401 等非默认端口。 如果在 Azure 门户中配置 SQL Server 库映像，则可在“SQL Server 设置”边栏选项卡中指定此端口。

若要在预配后配置此端口，可以使用两个选项：

- 对于资源管理器 VM，可以从 [SQL 虚拟机资源](manage-sql-vm-portal.md#access-the-resource)中选择“安全性”。 这会提供一个用于更改端口的选项。

  ![在门户中更改 TCP 端口](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- 对于经典 VM 或未通过门户进行配置的 SQL Server VM，可通过远程连接到 VM 来手动配置端口。 有关配置步骤，请参阅[将服务器配置为侦听特定 TCP 端口](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)。 如果使用此手动方法，还需添加 Windows 防火墙规则，允许该 TCP 端口上的传入流量。

> [!IMPORTANT]
> 如果 SQL Server 端口对公共 Internet 连接打开，则最好指定非默认端口。

SQL Server 侦听非默认端口时，必须在连接时指定该端口。 例如，考虑下服务器 IP 地址为 13.55.255.255，SQL Server 侦听端口 1401 的情况。 若要连接到 SQL Server，需在连接字符串中指定 `13.55.255.255,1401`。

## <a name="manage-accounts"></a>管理帐户

希望攻击者难以猜测帐户名或密码。 使用以下技巧会有所帮助：

- 创建一个唯一的本地管理员帐户，不要命名为 **Administrator**。

- 对所有帐户使用复杂的强密码。 若要深入了解如何创建强密码，请参阅[创建强密码](https://support.microsoft.com/account-billing/how-to-create-a-strong-password-for-your-microsoft-account-f67e4ddd-0dbe-cd75-cebe-0cfda3cf7386)一文。

- 默认情况下，Azure 在 SQL Server 虚拟机安装期间会选择 Windows 身份验证。 因此，会禁用 **SA** 登录名，并由安装程序分配密码。 建议不要使用或启用 SA 登录名。 如果必须使用 SQL 登录名，请使用以下策略之一：

  - 创建一个名称唯一且具有 sysadmin 成员资格的 SQL 帐户。 可通过在预配期间启用 SQL 身份验证，从门户执行此操作。

    > [!TIP] 
    > 如果预配期间未启用 SQL 身份验证，则必须手动将身份验证模式更改为 SQL Server 和 Windows 身份验证模式。 有关详细信息，请参阅 [更改服务器身份验证模式](/sql/database-engine/configure-windows/change-server-authentication-mode)。

  - 如果必须使用 SA 登录名，请在预配后启用该登录名，并分配新的强密码。



## <a name="next-steps"></a>后续步骤

如果还对有关性能的最佳做法感兴趣，请参阅 [Azure 虚拟机中 SQL Server 的性能最佳做法](./performance-guidelines-best-practices-checklist.md)。

若要了解与在 Azure VM 中运行 SQL Server 相关的其他主题，请参阅 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.yml)。


若要了解详细信息，请参阅本系列中的其他文章：

- [快速核对清单](performance-guidelines-best-practices-checklist.md)
- [VM 大小](performance-guidelines-best-practices-vm-size.md)
- [存储](performance-guidelines-best-practices-storage.md)
- [安全性](security-considerations-best-practices.md)
- [HADR 设置](hadr-cluster-best-practices.md)
- [收集基线](performance-guidelines-best-practices-collect-baseline.md)


