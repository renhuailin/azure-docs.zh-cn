---
title: Azure Synapse 连接设置
description: 本文介绍如何在 Azure Synapse Analytics 中配置连接设置
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587927"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Azure Synapse Analytics 连接设置

本文将介绍 Azure Synapse Analytics 中的连接设置以及如何根据情况对其进行配置。


## <a name="connection-policy"></a>连接策略
Azure Synapse Analytics 中 Synapse SQL 的连接策略设置为“Default”。 无法在 Azure Synapse Analytics 中更改此项。 若要详细了解如何影响 Azure Synapse Analytics 中 Synapse SQL 的连接，请参阅[此文](../../azure-sql/database/connectivity-architecture.md#connection-policy)。 

## <a name="minimal-tls-version"></a>最低 TLS 版本
Azure Synapse Analytics 中的 Synapse SQL 允许使用所有 TLS 版本进行连接。 无法在 Azure Synapse Analytics 中设置 Synapse SQL 的最低 TLS 版本。

## <a name="next-steps"></a>后续步骤

创建 [Azure Synapse 工作区](./synapse-workspace-ip-firewall.md)