---
title: 使用 Azure 实验室服务来设置用于讲授大数据分析的实验室 | Microsoft Docs
description: 了解如何使用 Hortonworks 数据平台 (HDP) 的 Docker 部署来设置用于讲授大数据分析的实验室。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659890"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>使用 HortonWorks 数据平台的 Docker 部署来设置大数据分析实验室

本文展示了如何设置用于讲授大数据分析课程的实验室。  通过此类课程，学生可以了解如何处理大量数据，并应用计算机和统计学习算法来获取数据见解。  学生的主要目标是了解如何使用数据分析工具，例如 [Apache Hadoop 的开源软件包](https://hadoop.apache.org/)，该软件包提供用于存储、管理和处理大数据的工具。

在此实验室中，学生将使用由 [Cloudera](https://www.cloudera.com/) 提供的常用商业版 Hadoop，称为 [Hortonworks 数据平台 (HDP)](https://www.cloudera.com/products/hdp.html)。  具体而言，学生将使用 [HDP 沙盒 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html)，它是一个简单易用的免费平台版本，适用于学习和试验。  尽管此课程可以使用部署了 HDP 沙盒的 Windows 或 Linux 虚拟机 (VM) ，本文将展示如何使用 Windows。

此实验室的另一个有趣特性是，我们将使用 [Docker](https://www.docker.com/) 容器在实验室 VM 上部署 HDP 沙盒。  每个 Docker 容器都提供了自己的隔离环境，让软件应用程序在内部运行。  从概念上讲，Docker 容器类似于嵌套 VM，可用于基于 [Docker Hub](https://www.docker.com/products/docker-hub) 提供的容器映像来轻松部署和运行各种软件应用程序。  Cloudera 针对 HDP 沙盒的部署脚本会自动从 Docker Hub 拉取 [HDP 沙盒 3.0.1 Docker 映像](https://hub.docker.com/r/hortonworks/sandbox-hdp)，并运行两个 Docker 容器：
  - sandbox-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要有 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅[有关设置实验室帐户的教程](tutorial-setup-lab-account.md)。  也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 若要详细了解如何启用市场映像，请参阅[指定可供实验室创建者使用的市场映像](./specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|市场映像| 启用 Windows 10 专业版映像，以便在实验室帐户中使用。|

### <a name="lab-settings"></a>实验室设置

设置课堂实验室时，请使用下表中的设置。  若要详细了解如何创建课堂实验室，请参阅[创建课堂实验室教程](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 中等（嵌套虚拟化）。 此 VM 大小最适用于关系数据库、内存中缓存和分析。  此大小还支持嵌套虚拟化。|  
|虚拟机映像| Windows 10 专业版|

> [!NOTE] 
> 需要使用中等（嵌套虚拟化），因为使用 Docker 部署 HDP 沙盒需要：
>   - 带有嵌套虚拟化的 Windows Hyper-V
>   - 至少 10 GB 的 RAM

## <a name="template-machine-configuration"></a>模板计算机配置

若要设置模板计算机，我们要：
- 安装 Docker
- 部署 HDP 沙盒
- 使用 PowerShell 和 Windows 任务计划程序来自动启动 Docker 容器

### <a name="install-docker"></a>安装 Docker

本部分中的步骤基于 [Cloudera 有关使用 Docker 容器部署的说明](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)。 

若要使用 Docker 容器，必须首先在模板 VM 上安装 Docker Desktop：

1. 遵循[先决条件部分](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中的步骤来安装[用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/install/)。 

    > [!IMPORTANT] 
    > 确保“使用 Windows 容器而不是 Linux 容器”配置选项处于未选中状态。

1. 确保“Windows 容器和 Hyper-V 功能”已打开。
   ![打开或关闭 Windows 功能](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. 遵循[用于 Windows 的内存](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows)部分中的步骤来配置 Docker 的内存配置。

    > [!WARNING]
    > 如果在安装 Docker 时无意中选中了“使用 Windows 容器而不是 Linux 容器”选项，则不会看到内存配置设置。  若要修补此问题，可以通过[单击 Windows 系统托盘中的 Docker 图标](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)切换到使用 Linux 容器；Docker Desktop 菜单开启后，选择“切换到 Linux 容器”。
 
### <a name="deploy-hdp-sandbox"></a>部署 HDP 沙盒

在本部分中，将部署 HDP 沙盒，然后再使用浏览器访问 HDP 沙盒。

1. 请确保已安装了指南的[先决条件部分](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)中列出的 [Git Bash](https://gitforwindows.org/)，因为建议用于完成后续步骤。

1. 使用[Cloudera 对于 Docker 的部署和安装指南](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)，完成以下部分中的步骤：
   
   -    部署 HDP 沙盒
   -    验证 HDP 沙盒

    > [!WARNING] 
    > 下载 HDP 的最新 .zip 文件时，请确保“不要”将 .zip 文件保存在包含空格的目录路径中。

    > [!NOTE] 
    > 如果在部署过程中收到异常，指出“驱动器尚未共享”，则需要与 Docker 共享 C 盘，以便 HDP 的 Linux 容器可以访问本地 Windows 文件。  若要修补此问题，请[单击 Windows 系统托盘中的 Docker 图标](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)以开启 Docker Desktop 菜单并选择“设置”。  当“Docker 的设置”对话开启时，选择“资源 > 文件共享”，然后检查“C”盘。    然后，可以重复这些步骤来部署 HDP 沙盒。

1. 部署并运行适用于 HDP 沙箱的 Docker 容器后，可以通过启动浏览器并按照 Cloudera 的说明开启[沙盒欢迎页](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page)并启动 HDP 仪表板来访问该环境。

    > [!NOTE] 
    > 这些说明假定你已首先将沙盒环境的本地 IP 地址映射到模板 VM 上的主机文件中的 sandbox-hdp.hortonworks.com。  如果“未”执行此映射，则可以通过导航到 `http://localhost:8080` 来访问沙盒欢迎页。

### <a name="automatically-start-docker-containers-when-students-log-in"></a>学生登录时自动启动 Docker 容器

若要为学生提供易于使用的体验，我们将使用自动执行以下操作的 PowerShell 脚本：
  - 当学生启动并连接到其实验室 VM 时，启动 HDP 沙盒 Docker 容器。
  - 启动浏览器并导航到沙盒欢迎页。
当学生登录到其 VM 时，我们还将使用 Windows 任务计划程序来自动运行此脚本。
若要进行此设置，请执行以下步骤：[大数据分析脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)。

## <a name="cost-estimate"></a>成本估算

如果要估计此实验室的成本，可以使用以下示例。

如果一个班级有 25 个学生，计划的课堂时间为 20 小时且家庭作业或作业时数配额为 10 小时，则实验室的价格为：
  - 25 个学生 * (20 + 10) 小时 * 55 实验室单位 * 0.01 美元/小时 = 412.50 美元

有关定价的更多详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语

本文指导你完成了使用部署了 Docker 的 Hortonworks 数据平台为大数据分析课程创建实验室所必需的步骤。  此课程类型的设置可用于类似的数据分析课程。  此设置还适用于使用 Docker 进行部署的其他类型的课程。

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室时通用的。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)