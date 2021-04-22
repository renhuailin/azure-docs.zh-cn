---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86230948"
---
- 不支持超级磁盘。
- 如果在你的 VM/虚拟机规模集上启用了 Azure 磁盘加密（使用 bitlocker/VM-Decrypt 的来宾 VM 加密），则无法启用。
- 无法在启用了主机加密的磁盘上启用 Azure 磁盘加密。
- 可以在现有的虚拟机规模集上启用加密。 但是，只有启用加密后新建的 VM 才会自动加密。
- 现有 VM 只有在经过释放和重新分配后才能加密。