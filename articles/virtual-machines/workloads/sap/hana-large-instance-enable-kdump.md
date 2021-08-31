---
title: 用于在 SAP HANA（大型实例）中启用 kdump 的脚本 | Microsoft Docs
description: 了解如何在 Azure HANA 大型实例类型 I 和类型 II 上启用 kdump 服务。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 272ac309629c62ee9fc7d12236aac4b2c3106b8a
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540917"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances"></a>Azure SAP HANA 大型实例的 kdump

本文介绍如何在 Azure HANA 大型实例 (HLI) 类型 I 和类型 II 中启用 kdump 服务。

需要配置和启用 kdump 来排查没有明确原因的系统崩溃。 有时，系统崩溃不能用硬件或基础结构问题来解释。 在这种情况下，操作系统或应用程序可能会导致此问题。 kdump 将允许 SUSE 确定系统崩溃的原因。

## <a name="supported-skus"></a>支持的 SKU

|  HANA 大型实例示例   |  OS 供应商   |  OS 包版本   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   示例 I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   示例 I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   示例 I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   示例 I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   示例 I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   示例 I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   示例 I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   示例 I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   示例 I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   示例 I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   示例 I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   示例 I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   示例 I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   示例 I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   示例 I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   示例 I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   示例 I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   示例 II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   示例 II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   示例 II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   示例 II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   示例 II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   示例 II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   示例 II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   示例 II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   示例 II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>先决条件

- kdump 服务使用 `/var/crash` 目录来写入转储。 请确保与此目录对应的分区有足够的空间来容纳转储。

## <a name="setup-details"></a>设置详细信息

- 可在 [GitHub 上的 Azure sap-hana-tools](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh) 中找到启用 kdump 的脚本

> [!NOTE]
> 此脚本是基于实验室设置生成的。 需要联系 OS 供应商进行进一步优化。
> 将为新的和现有服务器预配单独的逻辑单元号 (LUN)，用于保存转储。 脚本将负责在 LUN 外配置文件系统。
> Microsoft 不负责分析转储。 需要向 OS 供应商开具票证，以进行分析。

- 使用以下命令在 HANA 大型实例上运行此脚本：

    > [!NOTE]
    > 运行此命令需要 Sudo 权限。

    ```bash
    sudo bash enable-kdump.sh
    ```

- 如果命令的输出显示 kdump 已成功启用，请重新启动系统以应用更改。

- 如果命令的输出显示操作失败，则 kdump 服务未启用。 请参阅以下[支持问题](#support-issues)部分。

## <a name="test-kdump"></a>测试 kdump

> [!NOTE]
>  以下操作将触发内核崩溃和系统重新启动。

- 触发内核崩溃

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- 系统成功重新启动后，请查看 `/var/crash` 目录中的内核崩溃日志。

- 如果 `/var/crash` 具有当前日期的目录，则已成功启用 kdump。

## <a name="support-issues"></a>支持问题

如果脚本失败并出现错误，或未启用 kdump，请向 Microsoft 支持团队提出服务请求。 包括以下详细信息：

* HLI 订阅 ID

* 服务器名称

* OS 供应商

* OS 版本

* 内核版本

有关详细信息，请参阅[配置 kdump](https://www.suse.com/support/kb/doc/?id=3374462)。

## <a name="next-steps"></a>后续步骤

了解 HANA 大型实例上的操作系统升级。

> [!div class="nextstepaction"]
> [操作系统升级](os-upgrade-hana-large-instance.md)
