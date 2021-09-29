---
title: Azure NetApp 文件的快照策略故障排除 |Microsoft Docs
description: 介绍有助于排除 Azure NetApp 文件快照策略管理问题的错误消息和解决方法。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: e86761ed15089ef8544ec80b23f3ab585522df7a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128613111"
---
# <a name="troubleshoot-snapshot-policies"></a>快照策略问题故障排除

本文介绍在管理 Azure NetApp 文件快照策略时可能会遇到的错误情况。 此外，还提供解决方案来帮助你解决问题。

## <a name="error-conditions-and-resolutions"></a>错误情况和解决方法 

|     添加状态    |     解决方法    |
|-|-|
| 由于快照策略名称无效，快照策略创建失败。 | 如果快照策略名称无效，则会在创建快照策略的过程中出错。 快照策略名称适用以下准则：  <ul><li> 快照策略名称不能包含非 ASCII 字符或特殊字符。 </li> <li> 快照策略名称必须以字母或数字开头，并且只能包含字母、数字、下划线（“_”）和连字符（“-”）。 </li> <li> 快照策略名称的长度必须介于 1 到 64 个字符之间。  </li></ul> 请根据该准则修改快照策略名称。  |
| 由于值无效，快照策略创建失败。 | 如果为字段（如 `Number of snapshots to keep` 或 `Minute on the hour to take snapshot`）输入的值无效，Azure NetApp 文件将无法创建快照策略。 有效值如下：  <ul><li>该值必须是有效的数字。</li> <li>该值必须介于 0 和 59 之间。</li></ul> 请确保为字段提供有效的值。 | 
| 快照策略创建失败，并出现错误 `Total number of snapshots to keep exceeds 255`。 | 每个卷[最多可以有 255 个快照](azure-netapp-files-resource-limits.md)。 最大值包括每小时、每日、每周和每月快照的总和。 <br> 请减小 `Snapshots to keep` 值，然后重试。 |
| 将策略分配到卷失败，并出现错误 `Total snapshot policy is over the max '255'`。 | 每个卷[最多可以有 255 个快照](azure-netapp-files-resource-limits.md)。 当按需、每小时、每日、每周和每月快照的总和超过最大值时，将发生错误。 <br> 请减小 `snapshots to keep` 值或删除一些按需快照，然后重试。 | 

## <a name="next-steps"></a>后续步骤  

* [管理快照策略](snapshots-manage-policy.md)
