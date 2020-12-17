---
title: 用于在 SAP HANA 中启用 Kdump (大型实例) 的脚本 |Microsoft Docs
description: 用于在 SAP HANA 中启用 Kdump 的脚本 () ，b-hli 类型 I，B-HLI 类型 II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a799242ecaae7b7152d79b7d341a9cb5cc18d7fe
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654466"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>用于 Azure SAP HANA 大型实例 (，) 的 Kdump

配置和启用 kdump 是解决不具有明显原因的系统崩溃所需的步骤。
有时，系统会意外崩溃，因为硬件或基础结构问题无法解释。
在这些情况下，它可能是操作系统或应用程序问题，kdump 将允许 SUSE 确定系统崩溃的原因。

## <a name="enable-kdump-service"></a>启用 Kdump 服务

本文档介绍有关如何在 Azure HANA 大型实例 (**类型 I 和类型 II** 上启用 Kdump 服务的详细信息) 

## <a name="supported-skus"></a>支持的 SKU

|  Hana 大型实例类型   |  操作系统供应商   |  OS 包版本   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   键入 I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   键入 I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   键入 I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   键入 I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   键入 I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   键入 I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   键入 I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   键入 I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   键入 I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   键入 I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   键入 I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   类型 II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   类型 II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   类型 II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   类型 II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   类型 II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   类型 II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   类型 II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   类型 II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   类型 II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>先决条件

- Kdump 服务使用 `/var/crash` 目录写入转储，请确保该分区对应于此目录有足够的空间来容纳转储。

## <a name="setup-details"></a>设置详细信息

- 可在[此处](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)找到用于启用 Kdump 的脚本
> [!NOTE]
> 此脚本基于实验室设置进行，客户应与操作系统供应商联系以获得进一步的优化。
> 将为新的和现有的服务器预配单独的 LUN，以便保存转储，脚本将负责将文件系统配置在 LUN 外。
> Microsoft 不负责分析转储。 客户必须使用操作系统供应商打开票证，才能对其进行分析。

- 使用以下命令在 HANA 大型实例上运行此脚本

    > [!NOTE]
    > 运行此命令所需的 sudo 权限。

    ```bash
    sudo bash enable-kdump.sh
    ```

- 如果成功启用了命令输出 Kdump，请确保重新启动系统以成功应用更改。

- 如果命令输出无法执行特定操作，请退出!!!!,，则不启用 Kdump 服务。 请参阅 [支持问题](#support-issue)部分。

## <a name="test-kdump"></a>测试 Kdump

> [!NOTE]
>  以下操作将触发内核崩溃和系统重新启动。

- 触发内核故障

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- 系统成功重新启动后，检查 `/var/crash` 内核故障日志的目录。

- 如果 `/var/crash` 具有具有当前日期的目录，则 Kdump 已成功启用。

## <a name="support-issue"></a>支持问题

如果脚本失败并出现错误或未启用 Kdump，请向 Microsoft 支持团队上报服务请求，并提供以下详细信息。

* B-HLI 订阅 ID

* 服务器名称

* 操作系统供应商

* OS 版本

* 内核版本

## <a name="related-documents"></a>相关文档
- 了解有关[配置 kdump 的](https://www.suse.com/support/kb/doc/?id=3374462)详细信息
