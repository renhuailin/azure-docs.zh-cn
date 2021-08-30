---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/22/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 528b3243a7c7d8ca1f96d9b4a311393820e19685
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738996"
---
- 不支持超级磁盘。
- 如果在你的 VM/虚拟机规模集上启用了 Azure 磁盘加密（使用 bitlocker/DM-Crypt 的来宾 VM 加密），则无法启用。
- 无法在启用了主机加密的磁盘上启用 Azure 磁盘加密。
- 可以在现有的虚拟机规模集上启用加密。 但是，只有启用加密后新建的 VM 才会自动加密。
- 现有 VM 只有在经过释放和重新分配后才能加密。
- 支持临时 OS 磁盘，但仅支持平台管理的密钥。
