---
title: 将 AWS CloudTrail 连接到 Azure Sentinel |Microsoft Docs
description: 使用 AWS 连接器将 Azure Sentinel 访问权限委托给 AWS 资源日志，从而在 AWS CloudTrail 和 Azure Sentinel 之间创建信任关系。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 6e970d39d927a9e0aa522ad6a498388e2217b5d4
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254129"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>将 Azure Sentinel 连接到 AWS CloudTrail

使用 AWS 连接器将 AWS CloudTrail 管理事件流式传输到 Azure Sentinel。 此连接过程会将 Azure Sentinel 的访问权限委托给 AWS 资源日志，从而在 AWS CloudTrail 和 Azure Sentinel 之间创建信任关系。 这一过程通过在 AWS 上创建角色来完成，该角色可以授予 Azure Sentinel 访问 AWS 日志的权限。

> [!NOTE]
> AWS CloudTrail 在其 LookupEvents API 中具有[内置限制](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html)。 它允许的每个帐户的每秒事务数 (TPS) 不超过两个，且每个查询最多可以返回 50 个记录。 因此，如果单个租户在一个区域中每秒持续生成的记录超过 100 条，则会导致数据引入出现积压和延迟。
> 目前，只能将 AWS Commercial CloudTrail 连接到 Azure Sentinel，而不是 AWS GovCloud CloudTrail。

## <a name="prerequisites"></a>先决条件

必须拥有对 Azure Sentinel 工作区的写入权限。

> [!NOTE]
> Azure Sentinel 从所有区域收集 CloudTrail 管理事件。 建议不要将事件从一个区域流式传输到另一个区域。

## <a name="connect-aws"></a>连接 AWS 


1. 在 Azure Sentinel 中，选择“数据连接器”，然后在表中选择“Amazon Web Services”行，并在右侧的 AWS 窗格中单击“打开连接器页面”。

1. 根据“配置”下的指示，按以下步骤进行操作。
 
1.  在 Amazon Web Services 控制台的“安全性、标识与合规性”下选择“IAM”。

    ![AWS1](./media/connect-aws/aws-1.png)

1.  选择“角色”并选择“创建角色”。

    ![AWS2](./media/connect-aws/aws-2.png)

1.  选择“另一个 AWS 帐户”。 在“帐户 ID”字段中，输入可在 Azure Sentinel 门户的 AWS 连接器页中找到的“Microsoft 帐户 ID”（“123412341234”）。 

    ![AWS3](./media/connect-aws/aws-3.png)

1.  请确保选择“需要外部 ID”，然后输入可在 Azure Sentinel 门户的 AWS 连接器页中找到的外部 ID（工作区 ID）。

    ![AWS4](./media/connect-aws/aws-4.png)

1.  在“附加权限策略”下选择“AWSCloudTrailReadOnlyAccess”。

    ![AWS5](./media/connect-aws/aws-5.png)

1.  输入标记（可选）。

    ![AWS6](./media/connect-aws/aws-6.png)

1.  然后，输入“角色名称”并选择“创建角色”按钮。

    ![AWS7](./media/connect-aws/aws-7.png)

1.  在角色列表中，选择所创建的角色。

    ![AWS8](./media/connect-aws/aws-8.png)

1.  复制“角色 ARN”。 在 Azure Sentinel 门户的 Amazon Web Services 连接器屏幕中，将该值粘贴到“要添加的角色”字段，然后单击“添加”。

    ![AWS9](./media/connect-aws/aws-9.png)

1. 若要将 Log Analytics 中的相关架构用于 AWS 事件，请搜索“AWSCloudTrail”。

    > [!IMPORTANT]
    > 从 2020 年 12 月 1 日起，“AwsRequestId”字段已替换为“AwsRequestId_”字段（注意新添加的下划线）。 旧的“AwsRequestId”字段中的数据将保留到客户指定的数据保留期结束为止。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 AWS CloudTrail 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。