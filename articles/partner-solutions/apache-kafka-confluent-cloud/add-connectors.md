---
title: 为 Confluent Cloud 添加连接器 - Azure 合作伙伴解决方案
description: 本文介绍如何为用于 Azure 资源的 Confluent Cloud 安装连接器。
ms.service: partner-services
ms.topic: conceptual
ms.date: 09/03/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a5ef809be7518c4d99ff208e769af918fe579d4f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480689"
---
# <a name="add-connectors-for-confluent-cloud"></a>为 Confluent Cloud 添加连接器

本文介绍如何为 Confluent Cloud 安装连接到 Azure 资源的连接器。

## <a name="connector-to-azure-cosmos-db"></a>到 Azure Cosmos DB 的连接器

Azure Cosmos DB 接收器连接器完全托管的连接器已在 Confluent Cloud 中正式发布。 完全托管的连接器无需开发和管理自定义集成，还降低了在 Confluent Cloud 和 Azure Cosmos DB 之间连接数据的整体运营负担。 适用于 Confluent Cloud 的 Microsoft Azure Cosmos 接收器连接器从 Microsoft Azure Cosmos 数据库中读取数据以及将数据写入到该数据库中。 连接器轮询 Kafka 中的数据，并将数据写入数据库容器。

若要设置连接器，请参阅[适用于 Confluent Cloud 的 Azure Cosmos DB 接收器连接器](https://docs.confluent.io/cloud/current/connectors/cc-azure-cosmos-sink.html)。

必须手动安装 Azure Cosmos DB 自管理的连接器。 请先从 [Cosmos DB 版本页面](https://github.com/microsoft/kafka-connect-cosmosdb/releases)中下载 uber JAR。 或者，也可以[直接从源代码生成自己的 uber JAR](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector)。 按照 Confluent 文档中描述的关于[手动安装连接器](https://docs.confluent.io/home/connect/install.html#install-connector-manually)的指南，完成安装。  

## <a name="next-steps"></a>后续步骤

有关故障排除的帮助，请参阅[对 Apache Kafka for Confluent Cloud 解决方案进行故障排除](troubleshoot.md)。
