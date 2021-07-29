---
title: 本地 MySQL 到 Azure Database for MySQL 的迁移指南 - 附录 B：ARM 模板
description: 此模板将使用专用终结点部署所有资源。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 07e689b8458650ca39a0aa949504dd762621bac1
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656680"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-appendix-b-arm-templates"></a>本地 MySQL 到 Azure Database for MySQL 的迁移指南 - 附录 B：ARM 模板

### <a name="secure"></a>安全

此模板将使用专用终结点部署所有资源。 这有效地消除了从 Internet 对 PaaS 服务的任何访问。

[ARM 模板](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>不安全

此模板会使用标准部署来部署资源，这种情况下，所有资源均可从 Internet 获得。

[ARM 模板](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)