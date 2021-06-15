---
title: 用于在 SAP HANA（大型实例）中启用 Kdump 的脚本 | Microsoft Docs
description: 用于在 SAP HANA（大型实例）HLI 示例 I、HLI 示例 II 中启用 Kdump 的脚本
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c33288f80a6375b6a0c5e77d928e4314147efe0a
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110577303"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Azure SAP HANA 大型实例 (HLI) 的 Kdump

配置和启用 kdump 是排查没有明确原因的系统崩溃的必需步骤。
有时，系统会意外崩溃，而崩溃原因不是硬件或基础结构问题。
在这些情况下，可能是操作系统或应用程序问题，kdump 将允许 SUSE 确定系统崩溃的原因。

## <a name="enable-kdump-service"></a>启用 Kdump 服务

本文档介绍有关如何在 Azure HANA 大型实例（示例 I 和示例 II）上启用 Kdump 服务的详细信息

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

- Kdump 服务使用 `/var/crash` 目录写入转储，请确保与此目录对应的分区有足够的空间来容纳转储。

## <a name="setup-details"></a>设置详细信息

- 可在[此处](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh)找到用于启用 Kdump 的脚本
> [!NOTE]
> 此脚本是基于实验室设置生成的，客户应与 OS 供应商联系以进行进一步优化。
> 将为新服务器和现有的服务器预配单独的 LUN，以便保存转储，脚本将负责通过 LUN 配置文件系统配置。
> Microsoft 不负责分析转储。 客户必须向 OS 供应商提交票证以分析转储。

- 使用以下命令在 HANA 大型实例上运行此脚本

    > [!NOTE]
    > 运行此命令所需的 sudo 权限。

    ```bash
    sudo bash enable-kdump.sh
    ```

- 如果命令输出“已成功启用 Kdump”，请确保重新启动系统以成功应用更改。

- 如果命令输出“无法执行特定操作，正在退出!!!!”，则未启用 Kdump 服务。 请参阅[支持问题](#support-issue)部分。

## <a name="test-kdump"></a>测试 Kdump

> [!NOTE]
>  以下操作将触发内核崩溃和系统重新启动。

- 触发内核崩溃

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- 系统成功重新启动后，请查看 `/var/crash` 目录中的内核崩溃日志。

- 如果 `/var/crash` 具有当前日期的目录，则已成功启用 Kdump。

## <a name="support-issue"></a>支持问题

如果脚本失败并出现错误，或未启用 Kdump，请向 Microsoft 支持团队提出服务请求，并提供以下详细信息。

* HLI 订阅 ID

* 服务器名称

* OS 供应商

* OS 版本

* 内核版本

## <a name="related-documents"></a>相关文档
- 详细了解如何[配置 kdump](https://www.suse.com/support/kb/doc/?id=3374462)
