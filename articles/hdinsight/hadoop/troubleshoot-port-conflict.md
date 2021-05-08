---
title: 在 Azure HDInsight 中启动服务时的端口冲突
description: 与 Azure HDInsight 群集交互时出现的端口冲突问题的故障排除步骤和可能的解决方法。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f42e84d5d9c1dd49d9bf5604fe2f967eae0b6276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943102"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中启动服务时的端口冲突

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

服务无法启动。

## <a name="cause"></a>原因

存在端口冲突。

## <a name="resolution"></a>解决方法

### <a name="method-1"></a>方法 1

使用以下命令来获取/终止受端口问题影响的所有正在运行的进程。

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

然后，启动服务。

### <a name="method-2"></a>方法 2

重新启动该节点。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。