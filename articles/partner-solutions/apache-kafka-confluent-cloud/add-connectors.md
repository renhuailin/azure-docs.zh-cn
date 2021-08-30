---
title: 为 Confluent Cloud 添加连接器 - Azure 合作伙伴解决方案
description: 本文介绍如何为用于 Azure 资源的 Confluent Cloud 安装连接器。
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c4a15a54c5b9fb3b73a3fcaf81d63a9e176b49c5
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112534639"
---
# <a name="add-connectors-for-confluent-cloud"></a>为 Confluent Cloud 添加连接器

本文介绍如何为 Confluent Cloud 安装连接到 Azure 资源的连接器。

## <a name="connector-to-azure-cosmos-db"></a>到 Azure Cosmos DB 的连接器

在 Confluent 中心客户端中，按照 [Confluent 中心列表](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos)中的建议来安装 Cosmos DB 连接器。 

要手动安装连接器，请首先从 [Cosmos DB 版本](https://github.com/microsoft/kafka-connect-cosmosdb/releases)页中下载 uber JAR。 或者，也可以[直接从源代码生成自己的 uber JAR](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)。 按照 Confluent 文档中描述的关于[手动安装连接器](https://docs.confluent.io/home/connect/install.html#install-connector-manually)的指南，完成安装。  

## <a name="next-steps"></a>后续步骤

有关故障排除的帮助，请参阅[对 Apache Kafka for Confluent Cloud 解决方案进行故障排除](troubleshoot.md)。
