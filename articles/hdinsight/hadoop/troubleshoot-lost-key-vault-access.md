---
title: 具有磁盘加密的 Azure HDInsight 群集无法访问 Key Vault
description: 针对与 Azure HDInsight 群集交互时出现的 Key Vault 访问问题的故障排除步骤和可能的解决方案。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: ce2929ca84746de1ab8b51882f3004c3699f17ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943108"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>场景：具有磁盘加密的 Azure HDInsight 群集无法访问 Key Vault

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

当群集节点无法访问客户 Key Vault (KV) 时，会针对创建自己的密钥 (BYOK) 群集显示资源运行状况中心 (RHC) 警报 `The HDInsight cluster is unable to access the key for BYOK encryption at rest`。 Apache Ambari UI 上也会显示类似的警报。

## <a name="cause"></a>原因

此警报确保可从群集节点访问 KV，从而确保用户分配的托管标识的网络连接、KV 运行状况和访问策略。 此警报只是关于后续节点重新启动时即将关闭代理的警告，群集会继续正常运行，直到节点重新启动。

导航到 Apache Ambari UI，从“磁盘加密 Key Vault 状态”中找到关于警报的详细信息。 此警报会提供关于验证失败原因的详细信息。

## <a name="resolution"></a>解决方法

### <a name="kvaad-outage"></a>KV/AAD 中断

有关更多详细信息，请查看 [Azure Key Vault 可用性和冗余](../../key-vault/general/disaster-recovery-guidance.md)和 Azure 状态页 https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV 意外删除

* 将 KV 上已删除的密钥还原为自动恢复。 有关详细信息，请参阅[恢复已删除的密钥](/rest/api/keyvault/recoverdeletedkey)。
* 联系 KV 团队以恢复意外删除的密钥。

### <a name="kv-access-policy-changed"></a>KV 访问策略已更改

还原已分配到 HDI 群集的用户分配的托管标识的访问策略，以访问 KV。

### <a name="key-permitted-operations"></a>允许密钥执行的操作

对于 KV 中的每个密钥，可选择一组允许的操作。 确保为 BYOK 密钥启用包装和解包.操作

### <a name="expired-key"></a>过期的密钥

如果密钥已过期且未进行轮换，请从备份 HSM 中还原密钥，或联系 KV 团队以清除到期日期。

### <a name="kv-firewall-blocking-access"></a>KV 防火墙阻止访问

修复 KV 防火墙设置，以允许 BYOK 群集节点访问 KV。

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>虚拟网络上的 NSG 规则阻止访问

检查与连接到群集的虚拟网络相关联的 NSG 规则。

## <a name="mitigation-and-prevention-steps"></a>缓解和防护步骤

### <a name="kv-accidental-deletion"></a>KV 意外删除

* 配置 Key Vault 并[设置资源锁](../../azure-resource-manager/management/lock-resources.md)。
* 将密钥备份到其硬件安全模块。

### <a name="key-deletion"></a>密钥删除

删除密钥之前，应先删除群集。

### <a name="kv-access-policy-changed"></a>KV 访问策略已更改

定期审核和测试访问策略。

### <a name="expired-key"></a>过期的密钥

* 将密钥备份到 HSM。
* 使用未设置任何到期日期的密钥。
* 如果需要设置到期日期，请在到期日期之前轮换密钥。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。