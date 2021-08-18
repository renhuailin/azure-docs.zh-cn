---
title: Azure SAP HANA（大型实例）的数据分层和扩展节点 | Microsoft Docs
description: 了解 Azure SAP HANA（大型实例）的数据分层和扩展节点。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/17/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d13edf6e2512f6f70af8265e1859b106e75885c
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110577542"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>使用 SAP HANA 数据分层和扩展节点

SAP 支持面向不同 SAP NetWeaver 版本和 SAP BW/4HANA 的 SAP Business Warehouse (BW) 的数据分层模型。 有关数据分层模型的详细信息，请参阅[具有 SAP HANA 扩展节点的 SAP BW/4HANA and SAP BW on HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)。

利用 HANA 大型实例，你可以使用 SAP HANA 扩展节点的选项 1 配置，详见常见问题解答和 SAP 博客文档。 可使用以下 HANA 大型实例 SKU 设置选项 2 配置：S72m、S192、S192m、S384 和 S384m。

## <a name="advantages-of-sap-hana-extension-nodes"></a>SAP HANA 扩展节点的优点

通过使用 SAP HANA 扩展节点（选项 1 或 2），可以轻松地改进 SAP HANA 内存的使用。 查看 SAP 大小调整准则后，你会发现 SAP HANA 扩展节点的优势很明显。 以下是一些示例：

- SAP HANA 大小调整准则所要求的数据量通常是内存所需的两倍。 当你运行具有热数据的 SAP HANA 实例时，只有 50% 或更少的内存存储数据。 理想情况下，剩余的内存留给 SAP HANA 完成其工作。
- 这意味着，在拥有 2 TB 内存且运行 SAP BW 数据库的 HANA 大型实例 S192 单元中，你只能拥有 1 TB 的数据量。
- 如果使用另一 SAP HANA 扩展节点的选项 1 配置（仍然是 S192 HANA 大型实例 SKU），则可获得额外的 2 TB 数据量。 在选项 2 配置中，你可以获得额外的 4 TB 暖数据量。 与热节点相比，“暖”扩展节点的完整内存容量可用于选项 1 的数据存储。 选项 2 SAP HANA 扩展节点配置中的数据量可使用双倍内存。
- 使用选项 1，你最终将获得 3 TB 数据容量，其中热暖数据比为 1:2。 而使用选项 2 扩展节点配置时，你将获得 5 TB 数据容量，其中热暖数据比为 1:4。

数据量超出内存量的差值越大，请求的暖数据存储在磁盘上的可能性就越大。

## <a name="next-steps"></a>后续步骤

了解 Azure SAP HANA（大型实例）的操作模型和你的责任。

> [!div class="nextstepaction"]
> [操作模型和责任](hana-operations-model.md)
