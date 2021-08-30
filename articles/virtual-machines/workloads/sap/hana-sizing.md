---
title: 对 Azure SAP HANA（大型实例）调整大小 | Microsoft Docs
description: 了解如何为 Azure SAP HANA（大型实例）调整大小。
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
ms.date: 07/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4050a986c98b492f71383bd5c677d68288882611
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114404652"
---
# <a name="sizing"></a>调整大小

本文介绍有助于为 HANA 大型实例调整大小的信息。 通常情况下，为 HANA 大型实例调整大小与为 HANA 调整大小没有什么不同。 

## <a name="moving-an-existing-system-to-sap-hana-large-instances"></a>将现有系统移到 SAP HANA（大型实例）

假设你要将现有的已部署系统从另一个关系数据库管理系统 (RDBMS) 移到 HANA。 SAP 提供将在现有 SAP 系统上运行的报告。 如果数据库移到 HANA，这些报告会针对 HANA 实例检查数据并计算内存需求。 

请阅读以下 SAP 说明来获取有关如何运行这些报告以及如何获取其最新修补程序或版本的详细信息：

- [SAP 说明 #1793345 - 为 HANA 上的 SAP 套件调整大小](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP 说明 #1872170 - HANA 和 S/4 HANA 上的套件的大小调整报告](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP 说明 #2121330 - 常见问题解答：HANA 上的 SAP BW 的大小调整报告](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP 说明 #1736976 - 适用于 HANA 上的 BW 的大小调整报告](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP 说明 #2296290 - 新的适用于 HANA 上的 BW 的大小调整报告](https://launchpad.support.sap.com/#/notes/2296290)

## <a name="sizing-greenfield-implementations"></a>调整全新实现的大小

从头开始实现时，SAP Quick Sizer 会计算在 HANA 基础上实现 SAP 软件时的内存要求。

## <a name="memory-requirements"></a>内存需求

HANA 的内存需求将随数据量增长而增加。 请注意当前的内存消耗并能够预测将来的内存消耗。 然后，可以根据内存需求将需求映射到其中一个 HANA 大型实例 SKU。

## <a name="next-steps"></a>后续步骤

了解 HANA 大型实例的加入要求。

> [!div class="nextstepaction"]
> [加入要求](hana-onboarding-requirements.md)
