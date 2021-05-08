---
title: 设置实验室来讲解关系数据库的数据库管理 |Microsoft Docs
description: 了解如何设置实验室来讲解关系数据库的管理。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 32079d6dc31cf3fcad66976737630472129b4271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462418"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>设置实验室来讲解关系数据库的数据库管理

本文介绍如何在 Azure 实验室服务中设置实验室以提供基础数据库管理课程。 数据库概念是大学的大多数计算机系讲授的入门课程之一。 结构化查询语言 (SQL) 是国际标准语言。 SQL 是一种用于关系数据库管理（包括在数据库中添加、访问和管理内容）的标准语言。  它以其快速处理能力、高可靠性、易用性和灵活性而闻名。

本文将介绍如何在实验室中创建包含 MySQL 数据库服务器和 SQL Server 2019 服务器的虚拟机模板。  [MySQL](https://www.mysql.com/) 是一个可以免费使用的开源关系数据库管理系统 (RDBMS)。  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) 是最新版本的 Microsoft RDBMS。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要具有 Azure 订阅和实验室帐户才能开始使用。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 拥有 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅[创建实验室帐户教程](tutorial-setup-lab-account.md)。  你也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 若要详细了解如何启用市场映像，请参阅[指定适用于实验室创建者的市场映像](./specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|市场映像| 启用“基于 Windows Server 2019 的 SQL Server 2019 Standard”映像，以便在实验室帐户中使用。|

### <a name="lab-settings"></a>实验室设置

设置课堂实验室时，请使用下表中的设置。  若要详细了解如何创建课堂实验室，请参阅[创建课堂实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 中等。 此大小最适用于关系数据库、内存中缓存和分析。|
|虚拟机映像| 基于 Windows Server 2019 的 SQL Server 2019 Standard|

## <a name="template-machine-configuration"></a>模板计算机配置

若要在 Windows Server 2019 上安装 MySQL，你可以按照[在虚拟机上安装和运行 MySQL 社区服务器](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)中所述的步骤进行操作。

SQL Server 2019 预安装在创建新实验室时所选的虚拟机映像中。

## <a name="cost-estimate"></a>成本估算

我们来介绍此课程可能的估算成本。  课程将提供给 25 名学生。  课程时间计划为 20 小时。  此外，在计划的课程时间之外还为每名学生提供 10 小时配额，用于完成家庭作业或课外作业。  我们所选的虚拟机大小为“中等”，包含“42 个实验室单位”。

下面是此课程可能的成本估算示例：

25 名学生 \*（计划 20 小时 + 配额 10 小时）\* 每小时 0.42 美元 = 315.00 美元

有关定价的更多详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语

本文将指导你完成所需的步骤来创建实验室，以提供使用 MySQL 和 SQL Server 的基础数据库管理概念。 你可以为其他数据库课程使用类似的创建过程。

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室时通用的。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)