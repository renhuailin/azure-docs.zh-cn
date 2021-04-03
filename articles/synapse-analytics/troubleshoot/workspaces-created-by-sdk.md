---
title: 故障排除：SDK 创建的工作区无法启动 Synapse Studio
description: 解决由 SDK 创建的工作区无法启动 Synapse Studio 的步骤
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466041"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>对使用 SDK 创建的 Azure Synapse Analytics 工作区进行故障排除

本文提供了从使用软件开发工具包 (SDK) 创建的 Synapse 工作区启动 Synapse Studio 的故障排除步骤。


## <a name="prerequisites"></a>先决条件

- 使用 SDK 创建的 Synapse 工作区

## <a name="workaround"></a>解决方法

若要从 SDK 创建的工作区启动 Synapse Studio，请完成以下步骤： 
  1.    通过运行 `az synapse workspace create` 来创建工作区。
  2.    通过运行 `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` 来提取托管标识 ID。
  3.    通过运行 ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.` 将“存储 Blob 数据参与者”角色授予托管标识存储帐户
  4.    通过运行 ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` 来添加防火墙规则。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure Synapse](../overview-what-is.md)
* [入门](../get-started.md)
