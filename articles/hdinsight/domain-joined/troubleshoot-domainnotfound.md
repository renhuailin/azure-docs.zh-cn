---
title: 在 Azure HDInsight 中创建群集失败并出现 DomainNotFound 错误
description: 与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 0f5734197c0c00bb43584215f8eb26ad85e823c0
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284178"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中创建群集失败并出现 DomainNotFound 错误

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

HDI 安全（企业安全性套餐）群集创建失败并出现 `DomainNotFound` 错误消息。

## <a name="cause"></a>原因

DNS 设置不正确。

## <a name="resolution"></a>解决方法

部署已加入域的群集后，HDI 会在 AAD DS 中（为每个群集）创建内部用户名和密码，并会将所有群集节点加入此域。 域加入使用 Samba 工具来完成。 请确保满足以下先决条件：

* 域名应通过 DNS 进行解析。
* 应在要部署群集的虚拟网络的 DNS 设置中设置域控制器的 IP 地址。
* 如果此虚拟网络与 AAD DS 的虚拟网络对等互连，则必须手动执行此操作。
* 如果使用的是 DNS 转发器，则必须在虚拟网络中正确解析域名。
* 安全策略 (NSG) 不应阻止域加入。

### <a name="additional-debugging-steps"></a>其他调试步骤

* 在同一子网中部署 Windows VM，使用用户名和密码将计算机加入域（这可以通过控制面板 UI 来完成），或者

* 在同一子网中部署 ubuntu VM，并将计算机加入域
  * 通过 SSH 连接到计算机
  * sudo su
  * 使用用户名和密码运行脚本
  * 脚本将进行 ping 操作，创建所需的配置文件，然后将计算机加入域。 如果成功，表明 DNS 设置正确。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]