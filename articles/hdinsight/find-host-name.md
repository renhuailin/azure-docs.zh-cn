---
title: 如何获取 Azure HDInsight 群集节点的主机名
description: 了解如何获取 Azure HDInsight 群集节点的主机名和 FQDN 名。
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111385"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>查找群集节点的主机名

HDInsight 群集是使用公共 DNS clustername.azurehdinsight.net 创建的。 当你通过 SSH 连接到单个节点或将连接设置到同一自定义设置的虚拟网络中的群集节点时，需要使用主机名，或群集节点的完全限定的域名 (FQDN)。

本文介绍如何获取群集节点的主机名。 你可以通过 Ambari Web UI 手动获取或通过 Ambari REST API 自动获取。

> [!WARNING]
> 请使用以下推荐的方法获取群集节点的主机名。 主机名中的数字不一定是按顺序的，HDInsight 可能会更改主机名格式，使其与版本刷新的 Vm 保持一致。 不要依赖于现今存在的任何特定命名约定。 
>

你可以通过 Ambari UI 或 Ambari REST API 获取主机名。 

## <a name="get-the-host-names-from-ambari-web-ui"></a>从 Ambari Web UI 获取主机名
当你通过 SSH 连接到节点时，可以使用 Ambari Web UI 获取主机名。 你可以在 HDInsight 群集上获取 Ambari Web UI 主机视图，链接为 `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts`，其中`CLUSTERNAME` 是你的群集名称。

![Get-Host-Names-In-Ambari-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>从 Ambari REST API 获取主机名
在生成自动化脚本时，在与主机建立连接之前，你可以使用 Ambari REST API 获取主机名。 主机名中的数字不一定是按顺序的，HDInsight 可能会更改主机名格式，使其与版本刷新的 Vm 保持一致。 不要依赖于现今存在的任何特定命名约定。 

以下是有关如何检索群集中节点 FQDN 的一些示例。 更多详细信息，请参阅[使用 Apache Ambari REST API 管理 HDInsight 群集](.\hdinsight-hadoop-manage-ambari-rest-api.md)

以下示例使用 [jq](https://stedolan.github.io/jq/) 或 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) 来分析 JSON 响应文档并仅显示主机名。

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```