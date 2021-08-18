---
title: Azure HDInsight Ubuntu 18.04 更新
description: 了解 Azure HDInsight Ubuntu 18.04 OS 所做的更改。
ms.service: hdinsight
ms.topic: conceptual
ms.author: yanacai
author: yanancai
ms.date: 05/25/2021
ms.openlocfilehash: 61d801d7091d2a619ff6a8b6436f386c125ca4be
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414896"
---
# <a name="hdinsight-ubuntu-1804-os-update"></a>HDInsight Ubuntu 18.04 OS 更新

本文介绍了有关 HDInsight Ubuntu 18.04 OS 更新和可能需更改的更多详细信息。

## <a name="update-overview"></a>更新概述

HDInsight 已经开始在 2021 年 5 月推出新的 HDInsight 4.0 群集映像，其在 Ubuntu 18.04 上运行。 新创建的 HDInsight 4.0 群集将在可用时在 Ubuntu 18.04 上运行。 Ubuntu 16.04 上的现有群集将按原样运行，并受到完全支持。

HDInsight 3.6 将继续在 Ubuntu 16.04 上运行。 它将在 2021 年 6 月 30 日结束标准支持，并从 2021 年 7 月 1 日起改为标准支持。 有关日期和支持选项的详细信息，请参阅 [Azure HDInsight 版本](./hdinsight-component-versioning.md)。 HDInsight 3.6 不支持 Ubuntu 18.04。 如果想要使用 Ubuntu 18.04，需要将群集迁移到 HDInsight 4.0。

要将现有群集移动到 Ubuntu 18.04，需要删除并重新创建群集。 请计划创建或重新创建群集。 

## <a name="script-actions-changes"></a>脚本操作更改

HDInsight 脚本操作用于安装其他组件以及更改配置设置。 脚本操作是指在 HDInsight 群集的节点上运行的 Bash 脚本。

你可能需要对脚本操作进行一些可能的更改。

无论在何地抓取包时，将每个 `xenial` 实例更改为 `bionic`：

例如：
- 将 `http://packages.treasuredata.com/3/ubuntu/xenial/ xenial contrib` 更新到 `http://packages.treasuredata.com/3/ubuntu/bionic/ bionic contrib`。
- 将 `http://azure.archive.ubuntu.com/ubuntu/ xenial main restricted` 更新到 `http://azure.archive.ubuntu.com/ubuntu/ bionic main restricted`。

bionic 某些包的版本不存在： 

例如，在 bionic 存储库中不存在 [Node.js 版本 4.x](https://deb.nodesource.com/node_4.x/dists/)。 存在 [Node.js 版本 12.x](https://deb.nodesource.com/node_12.x/dists/bionic/)。

需要将 bionic 不存在的旧版本安装的脚本更新为更高版本。

默认情况下，18.04 中不存在 /etc/rc.local：

某些脚本用于 `/etc/rc.local` 服务启动，但默认情况下，Ubuntu 18.04 中不存在这些脚本。 应将其转换为正确的 systemd 单位。 

已更新基本 OS 包：

如果脚本依赖于 Ubuntu 16.04 中较早版本的包，它可能无法正常运行。 通过 SSH 连接到群集节点并在群集节点上运行 `dpkg --list`，以显示所有已安装包的详细信息。
 
通常，Ubuntu 18.04 的规则比 16.04 更严格。

## <a name="custom-applications"></a>自定义应用程序
某些[第三方应用程序](./hdinsight-apps-install-applications.md)可以安装到 HDInsight 群集。 对于 Ubuntu 18.04，这些应用程序可能无法正常工作。 为了降低中断性变更的风险，HDInsight 不会推出自 2021 年 2 月 25 日以来已安装自定义应用程序订阅的新映像。 如果要使用测试订阅尝试该新映像，请打开支持票证以启用订阅。

## <a name="edge-nodes"></a>边缘节点
对于新映像，群集边缘节点的 OS 也将更新为 Ubuntu 18.04。 你的现有客户端需要通过 Ubuntu 18.04 测试。 为了降低中断性变更的风险，HDInsight 不会推出自 2021 年 2 月 25 日以来已使用边缘节点订阅的新映像。 如果要使用测试订阅尝试该新映像，请打开支持票证以启用订阅。

## <a name="references"></a>参考
 - [Ubuntu 18.04 LTS 发行说明](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes/)





