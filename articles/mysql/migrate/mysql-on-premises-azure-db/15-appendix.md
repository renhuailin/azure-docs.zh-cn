---
title: 从本地 MySQL 到 Azure Database for MySQL 的迁移指南附录
description: 下载我们为此迁移指南创建的其他文档，并了解如何进行配置。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: feb02ded8e597f38c941b96eb6027061180a0177
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082696"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-appendix"></a>从本地 MySQL 到 Azure Database for MySQL 的迁移指南附录

## <a name="prerequisites"></a>先决条件

[摘要](14-summary.md)

## <a name="overview"></a>概述

本文介绍如何使用端到端 MySQL 迁移指南来部署示例应用程序，以及如何查看可用的服务器参数。

## <a name="environment-setup"></a>环境设置

[下载我们为此迁移指南创建的其他文档](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20A.pdf)，并了解如何配置环境，以针对示例[会议演示应用程序](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/sample-app)执行指南的迁移步骤。

## <a name="azure-resource-manager-arm-templates"></a>Azure 资源管理器 (ARM) 模板

### <a name="secure"></a>安全

ARM 模板部署具有专用终结点的所有资源。 ARM 模板有效地消除了从 Internet 对 PaaS 服务的任何访问。

[安全的 ARM 模板](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>不安全

此 ARM 模板将使用标准部署来部署资源，这种情况下，所有资源均可从 Internet 获得。

[不安全的 ARM 模板](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)

## <a name="default-server-parameters-mysql-55-and-azure-database-for-mysql"></a>MySQL 5.5 和 Azure Database for MySQL 的默认服务器参数

可以在我们的 GitHub 存储库中找到 [MySQL 5.5 和 Azure Database for MySQL 的默认服务器参数的完整列表](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20C.pdf)。