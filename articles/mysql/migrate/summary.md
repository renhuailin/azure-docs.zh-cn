---
title: 从本地 MySQL 到 Azure Database for MySQL 的迁移指南摘要
description: 本文档介绍了几个与将应用程序从本地 MySQL 迁移到 Azure Database for MySQL 相关的主题。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: e343a16cc27ff607025673d1c48ef872c14fdb14
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656725"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-summary"></a>从本地 MySQL 到 Azure Database for MySQL 的迁移指南摘要

本文档介绍了几个与将应用程序从本地 MySQL 迁移到 Azure Database for MySQL 相关的主题。 我们介绍了如何开始和评估项目，直到应用程序切换。

迁移团队需要仔细查看主题，因为所做的选择可能会对项目时间线产生影响。 考虑到所提供的许多企业就绪功能，总拥有成本非常诱人。

迁移项目方法非常重要。 团队需要评估应用程序和数据库的复杂性，以确定转换时间。 转换工具有助于简化转换，但存在始终需要手动检查和更新的元素。 编写迁移前任务和迁移后测试的脚本非常重要。

应用程序体系结构和设计可以提供有关所需工作级别的强指标。 例如，使用 ORM 框架的应用程序可能是很好的选择，尤其是当业务逻辑包含在应用程序中而不是数据库对象中时。

最后，市场中存在从免费到商业性等多种工具。 本文档介绍了团队计划使用较常用开源工具选项之一进行数据库迁移所需的步骤。 无论选择哪种路径，Microsoft 和 MySQL 社区都具备使数据库成功迁移的工具和专业知识。

### <a name="questions-and-feedback"></a>问题和反馈

如果有任何关于使用 Azure Database for MySQL 的问题或建议，请发送电子邮件至 Azure Database for MySQL 团队 (AskAzureDBforMySQL@service.microsoft.com)。 

> [!Important]
> Azure Database for MySQL 团队地址仅处理常规问题，不处理支持工单。

另外，可以根据需要考虑以下联系点：

  - 若要联系 Azure 支持部门或修复帐户问题，请[通过 Azure 门户提交工单。](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

  - 若要提供反馈或请求新功能，请通过 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) 创建条目。

### <a name="find-a-partner-to-help-migrating"></a>查找合作伙伴帮助迁移

本指南可能会让人不知所措，但不要担心\! 社区中有许多专家拥有经过证实的迁移记录。 [搜索 Microsoft 合作伙伴](https://www.microsoft.com/solution-providers/home)或 [Microsoft MVP](https://mvp.microsoft.com/MvpSearch)，以帮助查找最合适的迁移策略。 你并非一个人\!

还可以浏览技术论坛和社交组，获取更详细的实际信息：

  - [Microsoft 社区论坛 ](/answers/topics/azure-database-mysql.html)

  - [Azure Database for MySQL 技术社区 ](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/bg-p/ADforMySQL)

  - [Azure MySQL 堆栈溢出 ](https://stackoverflow.com/questions/tagged/azure-database-mysql)

  - [Azure Facebook 组 ](https://www.facebook.com/groups/MsftAzure)

  - [LinkedIn Azure 组 ](https://www.linkedin.com/groups/2733961/)

  - [LinkedIn Azure 开发人员组 ](https://www.linkedin.com/groups/1731317/)

## <a name="next-steps"></a>后续步骤

若要使用端到端 MySQL 迁移指南部署示例应用程序，请访问[附录 A](appendix-a-environment-setup.md) 和[附录 B](appendix-b-arm-templates.md)。

若要查看可用的服务器参数，请访问[附录 C](appendix-c-default-server-parameters-mysql-55-and-azure-database-for-mysql.md)。