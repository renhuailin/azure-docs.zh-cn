---
title: include 文件
description: 包含文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e9d7b39ce267202503b90e84e934d31501d45478
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732507"
---
所有最新一代的 VM，无论容量多大，都支持主机加密：

|类型  |不支持  |支持  |
|---------|---------|---------|
|常规用途     | Dv3, Dv2, Av2        | B、DSv2、Dsv3、DC、DCv2、Dav4、Dasv4、Ddv4、Ddsv4       |
|计算优化     |         | Fsv2        |
|内存优化     | Ev3        | Esv3、M、Mv2、Eav4、Easv4、Edv4、Edsv4        |
|存储优化     |         | Ls、Lsv2（NVMe 磁盘未加密）        |
|GPU     | NC、NV        | NCv2、NCv3、ND、NVv3、NVv4、NDv2（预览版）        |
|高性能计算     | H        | HB、HC、HBv2        |
|前几代     | F、A、D、L、G        | DS、GS、Fs、NVv2        |
