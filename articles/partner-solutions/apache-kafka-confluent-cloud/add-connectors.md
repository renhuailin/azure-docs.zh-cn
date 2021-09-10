---
title: 为 Confluent Cloud 添加连接器 - Azure 合作伙伴解决方案
description: 本文介绍如何为用于 Azure 资源的 Confluent Cloud 安装连接器。
ms.service: partner-services
ms.topic: conceptual
ms.date: 08/31/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: d37f34005dd2b4774828e79006d33d7a398cbc07
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315897"
---
# <a name="add-connectors-for-confluent-cloud"></a>为 Confluent Cloud 添加连接器

本文介绍如何为 Confluent Cloud 安装连接到 Azure 资源的连接器。

## <a name="connector-to-azure-cosmos-db"></a>到 Azure Cosmos DB 的连接器

Azure Cosmos DB 接收器连接器完全托管的连接器在 Confluent Cloud 中可用。 完全托管的连接器无需开发和管理自定义集成，还降低了在 Confluent Cloud 和 Azure Cosmos DB 之间连接数据的整体运营负担。 适用于 Confluent Cloud 的 Microsoft Azure Cosmos 接收器连接器从 Microsoft Azure Cosmos 数据库中读取数据以及将数据写入到该数据库中。 连接器轮询 Kafka 中的数据，并将数据写入数据库容器。

若要设置连接器，请参阅[适用于 Confluent Cloud 的 Azure Cosmos DB 接收器连接器](https://docs.confluent.io/cloud/current/connectors/cc-azure-cosmos-sink.html)。

要手动安装连接器，请首先从 [Cosmos DB 版本](https://github.com/microsoft/kafka-connect-cosmosdb/releases)页中下载 uber JAR。 或者，也可以[直接从源代码生成自己的 uber JAR](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)。 按照 Confluent 文档中描述的关于[手动安装连接器](https://docs.confluent.io/home/connect/install.html#install-connector-manually)的指南，完成安装。  

## <a name="next-steps"></a>后续步骤

有关故障排除的帮助，请参阅[对 Apache Kafka for Confluent Cloud 解决方案进行故障排除](troubleshoot.md)。
