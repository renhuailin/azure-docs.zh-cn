---
title: Azure Data Box Gateway 和 Azure Data Box Edge 1906 发行说明 | Microsoft Docs
description: 介绍运行 1906 版本的 Azure Data Box Gateway 和 Azure Data Box Edge 的关键未解决问题和解决方案。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 51f4995ffd6a86022d95df15ae0eb7694d878c60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581623"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge 和 Azure Data Box Gateway 1906 发行说明

以下发行说明列出了 Azure Data Box Edge 和 Azure Data Box Gateway 的 1906 版本的关键未解决问题和已解决问题。 

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Data Box Edge/Data Box Gateway 之前，请仔细查看发行说明中包含的信息。

此版本对应于以下软件版本：

- Data Box Gateway 1906 (1.6.978.743)
- Data Box Edge 1906 (1.6.978.743)

> [!NOTE]
> 更新 1906 只能应用于运行该软件正式发布版 (GA) 或 1905 版本的 Data Box Edge 设备。

## <a name="whats-new"></a>新增功能

- 修复了恢复密钥管理工作流中的 Bug - 在早期版本中，有一个 Bug 导致无法应用恢复密钥。 在此版本中已修复这一 bug。 我们强烈建议你应用此更新，因为使用恢复密钥可以在设备无法启动时恢复设备上的数据。 有关详细信息，请参阅[如何在部署 Data Box Edge 或 Data Box Gateway 时保存恢复密钥](../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)。
- 改进了现场可编程门阵列 (FPGA) 日志记录 - 从版本 1905 开始，增强了与 FPGA 相关的日志记录和警报功能。 如果将 Edge 计算功能与 FPGA 配合使用，则这仍然是 Data Box Edge 所需的更新。 有关详细信息，请了解如何[在 Data Box Edge 上使用 Edge 计算来转换数据](../databox-online/azure-stack-edge-deploy-configure-compute-advanced.md)。

## <a name="known-issues-in-ga-release"></a>正式发布版本中的已知问题

此版本的发行说明中，没有提出任何新的问题。 在发行说明中提到的所有问题都是从以前的版本带过来的。 若要查看已知问题的列表，请参阅[正式发布版中的已知问题](data-box-gateway-release-notes.md#known-issues-in-ga-release)。


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [准备部署 Azure Data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md)
