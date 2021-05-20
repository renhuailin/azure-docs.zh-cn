---
title: Azure FarmBeats 体系结构
description: 介绍 Azure FarmBeats 的体系结构
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: ebc1cdc7c762117851072037624f11e9fa98b6f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182598"
---
# <a name="integration-patterns"></a>集成模式

Azure FarmBeats 是 Azure Marketplace 中提供的企业到企业产品/服务。 FarmBeats 可通过合并数据集生成人工智能 (AI) 或机器学习 (ML) 模型，从而跨提供商聚合农业数据集并生成可操作的见解。

![FarmBeats 项目](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

以下部分介绍了 Azure FarmBeats 的集成模式。

## <a name="why-integrate-with-azure-farmbeats"></a>为什么要集成 Azure FarmBeats？

本部分主要针对想要将其数据系统（例如传感器、无人机、气象站）集成到 Azure FarmBeats 的合作伙伴。

Azure FarmBeats 是一种可扩展的产品/服务，它可以让农业企业将不同的历史和实时农业数据集添加到单个平台。 Azure FarmBeats 有助于农业企业在农场情景下将其数据标准化、情景化以及将数据聚合。

成为 Azure FarmBeats 的数据合作伙伴，你可以通过数据产品/服务扩大自己系统的使用规模，并与更多客户建立联系。 Azure FarmBeats 提供了一个可扩展的 API 层（称为“数据中心”），它可帮助你将设备中的数据系统地引入标准化架构中。

客户可从其 Azure FarmBeats 实例中获取此数据后，即可基于你的数据生成更多分析和工具。

## <a name="next-steps"></a>后续步骤

有关传感器数据集成的详细信息，请参阅[传感器数据集成](sensor-partner-integration-in-azure-farmbeats.md)；有关图像合作伙伴集成的信息，请参阅[图像合作伙伴集成](imagery-partner-integration-in-azure-farmbeats.md)。
