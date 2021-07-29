---
title: 解决存储防火墙设置问题
description: 在 Azure HPC 缓存中创建 Azure Blob 存储目标时，存储帐户网络防火墙设置可能会导致失败。 本文在软件修补程序出现之前，针对此限制提供了一种变通方法。
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: v-erkel
ms.openlocfilehash: 45a7169330b11e98a8618b08205217212414ca5d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258922"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>解决 Blob 存储帐户防火墙设置问题

存储帐户防火墙中使用的特定设置可能会导致 Blob 存储目标创建失败。 Azure HPC 缓存团队正在开发针对此问题的软件修补程序，但你可以按照本文中的说明操作以绕过此问题。

仅允许从“选定的网络”访问的防火墙设置可以阻止缓存创建或修改 Blob 存储目标。 此配置位于存储帐户的“防火墙和虚拟网络”设置页面中。 （此问题不适用于 ADLS-NFS 存储目标）

问题是缓存服务使用独立于客户环境的隐藏服务虚拟网络。 无法显式授权此网络访问存储帐户。

创建 Blob 存储目标时，缓存服务使用此网络检查容器是否为空。 如果防火墙不允许从隐藏网络进行访问，则检查将失败，存储目标的创建也会失败。

防火墙也可阻止对 Blob 存储目标命名空间路径的更改。

要绕过此问题，请在创建存储目标时暂时更改防火墙设置：

1. 转到存储帐户“防火墙和虚拟网络”页，然后将“允许的访问来源”设置更改为“所有网络” 。
1. 在 Azure HPC 缓存中创建 Blob 存储目标。
1. 创建存储目标的命名空间路径。
1. 成功创建存储目标和路径后，将帐户的防火墙设置更改回“选定的网络”。

Azure HPC 缓存不使用服务虚拟网络访问完成的存储目标。

有关此变通方法的帮助，请[与 Microsoft 服务和支持部门联系](hpc-cache-support-ticket.md)。
