---
title: Azure 澳大利亚备份和灾难恢复
description: Microsoft Azure 备份和灾难恢复因与 ASD Essential 8 相关而适用于澳大利亚政府机构
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 22f6e970a0d5f73d975223b184979615aaa6d5ae
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "117028950"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Azure 澳大利亚备份和灾难恢复

制定具有配套基础结构的备份和灾难恢复计划对于所有组织而言都至关重要。 [澳大利亚网络安全中心的 Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm) 强调了备份解决方案的重要性。

Microsoft Azure 提供了两种可实现复原能力的服务：Azure 备份和 Azure Site Recovery。 利用这些服务可保护本地和云中数据，以实现各种设计方案。 Azure 备份和 Azure Site Recovery 均使用公用存储和管理资源，即 Azure 恢复服务保管库。 此保管库用于管理、监视和隔离 Azure 备份和 Azure Site Recovery 数据。

本文详细介绍了根据[澳大利亚信号局 (ASD) 信息安全手册 (ISM) 控制](https://acsc.gov.au/infosec/ism/index.htm)实施 Azure 备份和 Azure Site Recovery 的关键设计元素。

## <a name="azure-backup"></a>Azure 备份

![Azure 备份](media/backup-overview.png)

Azure 备份类似于传统的本地备份解决方案，且能够同时备份本地和 Azure 托管的数据。 Azure 备份可用于将下列数据类型备份到 Azure：

* 文件和文件夹
* 受支持的 Windows 和 Linux 操作系统的托管位置：
  * Hyper-V 和 VMWare 虚拟机监控程序
  * 物理硬件
* 受支持的 Microsoft 应用程序

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery 可复制由单个虚拟机或多层应用程序组成的工作负载， 支持从本地到 Azure、在 Azure 区域之间或在由 Azure Site Recovery 协调的本地位置之间进行复制。 本地虚拟机可复制到 Azure 中，也可复制到受支持的本地虚拟机监控程序中。 配置完成后，Azure Site Recovery 会协调复制、故障转移和故障恢复。

## <a name="key-design-considerations"></a>关键设计考虑因素

实施备份或灾难恢复解决方案时，所提出的解决方案需要考虑以下因素：

* 待捕获数据的范围和数量
* 数据保留时间
* 如何安全地存储和管理此数据
* 存储数据的地理位置
* 例程系统测试流程

ISM 针对设计解决方案时的安全注意事项提供了指导， Microsoft Azure 则提供了应对这些安全注意事项的方法。

### <a name="data-sovereignty"></a>数据主权

在使用基于云的存储位置时，组织需要确保数据主权得到维护。 Azure Policy 所提供的方法可限制允许创建 Azure 资源的位置。 内置 Azure Policy“允许的位置”用于确保在分配的 Azure Policy 范围内创建的任何 Azure 资源只能在指定的地理位置创建。

用于对 Azure 资源进行地理限制的 Azure Policy 项目包括：

* allowedLocations
* allowedSingleLocation

Azure 管理员可利用这些策略，限制仅可在一系列指定位置，甚至是单个地理位置创建 Azure 资源。

### <a name="redundant-and-geographically-dispersed-storage"></a>冗余存储和异地分散存储

Azure 恢复服务保管库中存储的数据始终存储在冗余存储中。 默认情况下，恢复服务保管库使用 Azure 异地冗余存储 (GRS)。 使用 GRS 存储的数据将复制到恢复服务保管库的[辅助配对区域](../best-practices-availability-paired-regions.md)中的其他 Azure 数据中心。 此复制数据存储为只读数据，并且仅在发生 Azure 故障转移事件时才可写入。 在 Azure 数据中心内，数据在单独的容错域和升级域之间进行复制，以最大限度地降低硬件故障或基于维护的中断的可能性。 GRS 每年至少 99.99999999999999% 的时间均可用。

Azure 恢复服务保管库可以配置为使用本地冗余存储 (LRS)。 LRS 是一种成本较低的存储选项，但会降低可用性。 此冗余模型在单独的容错域和升级域之间进行相同的复制，但不会在地理区域之间进行复制。 位于 LRS 存储中的数据虽然没有 GRS 的复原能力，但每年至少 99.999999999% 的时间均可用。

与传统场外存储技术（如磁带介质）不同，该数据的其他副本为自动创建，无需任何额外的管理开销。

### <a name="restricted-access-and-activity-monitoring"></a>受限制的访问权限和活动监视

必须保护备份数据免遭损坏、修改和未经批准的删除。 Azure 备份和 Azure Site Recovery 均使用通用的 Azure 管理结构。 此结构为位于 Azure 中的资源提供详细的审核、日志记录和 Azure 基于角色的访问控制 (Azure RBAC)。 可以限制仅选定的管理人员可访问备份数据，且可以记录并审核涉及备份数据的所有操作。

Azure 备份和 Azure Site Recovery 均具有内置日志记录和报告功能。 如在备份或复制过程中发生任何问题，均可使用 Azure 管理结构向管理员报告。

Azure 恢复服务保管库还提供以下附加数据安全措施：

* 删除之后，备份数据将保留 14 天
* 针对关键操作（例如“停止备份并删除数据”）提供警报和通知
* 针对关键操作提出安全 PIN 要求
* 执行最小保持期检查

最小保持期检查具体情况如下：

* 对于日保留期，至少保留七天
* 对于周保留期，至少保留四周
* 对于月保留期，至少保留三个月
* 对于年保留期，至少保留一年

Azure 中存储的所有备份数据均使用 Azure 存储服务加密 (SSE) 进行静态加密。 默认情况下，针对所有新的和现有的存储帐户启用 SSE，并且不能禁用。 加密数据在检索过程中自动解密。 默认情况下，使用 SSE 加密的数据会使用 Microsoft 提供和管理的密钥进行加密。 组织可以选择提供和管理自己的加密密钥，以与 SSE 配合使用。 这可为加密数据提供可选的附加安全层。 此密钥可由客户在本地存储，也可以安全地存储在 Azure Key Vault 中。

### <a name="secure-data-transport"></a>保护数据传输

使用 AES 256 在传输过程中加密 Azure 备份数据。 使用管理人员在首次配置备份时创建的密码可保护此数据。 Microsoft 无权访问此密码，这意味着客户必须确保安全存储此密码。 然后，可通过安全的 HTTPS 连接在本地环境和 Azure 恢复服务保管库之间传输数据。  之后，使用 Azure SSE 对恢复服务保管库中的数据进行静态加密。

Azure Site Recovery 数据在传输过程中也始终加密。 所有复制的数据均使用 HTTPS 和 TLS 安全地传输至 Azure。 当 Azure 客户使用 ExpressRoute 连接与 Azure 连接时，Azure Site Recovery 数据将通过此专用连接发送。  当 Azure 客户使用 VPN 连接与 Azure 连接时，数据会通过 Internet 在本地和恢复服务保管库之间安全地复制。

这种安全网络数据传输消除了管理传统场外备份存储解决方案（如磁带介质）的安全风险和风险缓解要求。

### <a name="data-retention-periods"></a>数据保留期

建议备份保留期至少为三个月，但是，通常需要更长的保留期。 Azure 备份最多可提供 9999 个备份副本。 如果每天对受保护的实例进行一次 Azure 备份，则可以保留 27 年的每日备份。 如果每月对受保护的实例进行一次备份，则可以保留 833 年的每月备份。 随着备份数据过时且保留的备份粒度较小，备份数据的总保持期窗口会延长。  Azure 不会限制数据保留在 Azure 恢复服务保管库中的时间长度，仅限制每个实例的备份总数。 从旧备份还原或从新备份还原之间不存在性能差异，每次还原所需的时间相同。

Azure 恢复服务保管库具有许多默认的备份和保留策略。  管理人员还可以创建自定义备份和保留策略。

![Azure 备份策略](media/create-policy.png)

配置 Azure 备份策略和保留策略时，需要在备份频率和长期保留要求之间取得平衡。

### <a name="backup-and-restore-testing"></a>备份和还原测试

ISM 建议对备份数据进行测试，以确保在需要还原或故障转移时受保护的数据依然有效。 Azure 备份和 Azure Site Recovery 还能够在备份或复制受保护的数据后对其进行测试。 由 Azure 备份管理的数据可以还原到指定位置，然后可以验证数据的一致性。

Azure Site Recovery 具有执行故障转移测试的内置功能。 复制到恢复服务保管库的工作负载可以还原到指定的 Azure 环境。 目标还原环境可以完全独立于任何生产环境，以确保在执行测试时不会影响生产系统。 测试完成后，可以立即删除测试环境和所有资源，以降低运营成本。

可以使用 Azure 平台中内置的 Azure 自动化服务自动执行故障转移测试和验证。 此操作支持安排自动执行故障转移测试，以确保数据成功复制到 Azure。

## <a name="next-steps"></a>后续步骤

查看[使用 Azure Policy 确保安全性](azure-policy.md)一文。