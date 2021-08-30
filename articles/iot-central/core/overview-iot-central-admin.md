---
title: Azure IoT Central 管理员指南
description: Azure IoT Central 是一种 IoT 应用程序平台，用于简化创建 IoT 解决方案。 本文概述了 IoT Central 中的管理员角色。
author: dominicbetts
ms.author: dobett
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4ff2ea374768189881c9e1ff28511bc403dd6306
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015558"
---
# <a name="iot-central-administrator-guide"></a>IoT Central 管理员指南

IoT Central 应用程序使你可以在设备整个生命周期内监视和管理数百万台设备。 本指南适用于管理 IoT Central 应用程序的管理员。

在 IoT Central 中，管理员的职责是：

- 管理应用程序中的用户和角色。
- 管理安全性，例如设备身份验证。
- 配置应用程序设置。
- 升级应用程序。
- 导出和共享应用程序。
- 监视应用程序的运行状况。

## <a name="users-and-roles"></a>用户和角色

IoT Central 使用基于角色的访问控制系统来管理应用程序内的用户权限。 IoT Central 具有三个内置角色，分别用于管理员、解决方案构建者和操作员。 管理员可以创建具有特定权限集的自定义角色。 管理员负责将用户添加到应用程序并将其分配给角色。

若要了解详细信息，请参阅[在 IoT Central 应用程序中管理用户和角色](howto-manage-users-roles.md)。

## <a name="application-security"></a>应用程序安全性

连接到 IoT Central 应用程序的设备通常使用 X.509 证书或共享访问签名 (SAS) 作为凭据。 管理员管理设备凭据派生自的组证书或密钥。

若要了解详细信息，请参阅 [X.509 组注册](concepts-get-connected.md#x509-group-enrollment)、[SAS 组注册](concepts-get-connected.md#sas-group-enrollment)和[如何滚动 X.509 设备证书](how-to-roll-x509-certificates.md)。

管理员还可以创建和管理客户端应用程序用来向 IoT Central 应用程序进行身份验证的 API 令牌。 客户端应用程序使用 REST API 与 IoT Central 交互。

## <a name="configure-an-application"></a>配置应用程序

管理员可以配置 IoT Central 应用程序的行为和外观。 若要了解更多信息，请参阅以下文章：

- [更改应用程序名称和 URL](howto-administer.md#change-application-name-and-url)
- [自定义 UI](howto-customize-ui.md)
- [将应用程序移至其他定价计划](howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-)
- [配置文件上传](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>导出应用程序

管理员可以执行以下操作：

- 创建应用程序的副本，前提是只需应用程序的重复副本。 例如，可能需要重复的副本以进行测试。
- 如果打算创建多个副本，请从现有应用程序中创建应用程序模板。

若要了解详细信息，请参阅[创建并使用自定义应用程序模板](howto-create-iot-central-application.md#create-and-use-a-custom-application-template)。

## <a name="migrate-to-a-new-version"></a>迁移到新版本

管理员可以将应用程序迁移到较新的版本。 目前，新创建的应用程序是 V3 应用程序。 管理员可能需要将 V2 应用程序迁移到 V3 应用程序。

若要了解详细信息，请参阅[将 V2 IoT Central 应用程序迁移到 V3](howto-migrate.md)。

## <a name="monitor-application-health"></a>监视应用程序运行状况

管理员可以使用 IoT Central 指标来评估连接设备的运行状况和正在运行的数据导出的运行状况。

要查看指标，管理员可以使用 Azure 门户中的图表、REST API 或 PowerShell 或 Azure CLI 查询。

若要了解详细信息，请参阅[监视应用程序运行状况](howto-manage-iot-central-from-portal.md#monitor-application-health)。

## <a name="monitor-connected-iot-edge-devices"></a>监视已连接的 IoT Edge 设备

若要了解如何使用 Azure Monitor 和内置指标集成远程监视 IoT Edge 队列，请参阅[收集和传输指标](../../iot-edge/how-to-collect-and-transport-metrics.md)。

## <a name="tools"></a>工具

每个 IoT Central 应用程序的“管理”部分中提供了许多管理员可用的工具。 还可以使用以下工具来完成一些管理任务：

- [Azure 命令行](howto-manage-iot-central-from-cli.md)
- [Azure 门户](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>后续步骤

了解如何管理 Azure IoT Central 应用程序后，建议接下来了解如何在 Azure IoT Central 中[管理用户和角色](howto-manage-users-roles.md)。
