---
title: 快速入门 - 将 Red Hat Enterprise Linux (RHEL) 上的 JBoss Enterprise Application Platform (EAP) 部署到 Azure VM 和虚拟机规模集
description: 如何在 Azure RHEL VM 和虚拟机规模集上使用 Red Hat JBoss EAP 部署企业 Java 应用程序。
author: theresa-nguyen
ms.author: bicnguy
ms.date: 10/30/2020
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: 34d0fdb605e81606960e56a3a15f9f41ee078898
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112467412"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>使用 Red Hat Enterprise Linux 上的 JBoss EAP 将企业 Java 应用程序部署到 Azure

本文中的 Azure 快速入门模板介绍如何使用 [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) 将 [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) 部署到 Azure 虚拟机 (VM) 和虚拟机规模集。 你将使用示例 Java 应用来验证部署。 

JBoss EAP 是开放源代码应用程序服务器平台。 它为 Java 应用程序提供企业级安全性、可伸缩性和性能。 RHEL 是开放源代码操作系统 (OS) 平台。 通过它可缩放现有应用，并在所有环境中推出新兴技术。 

JBoss EAP 和 RHEL 包括在任何环境中生成、运行、部署和管理企业 Java 应用程序所需的一切内容。 该组合是用于本地环境、虚拟环境、私有云、公有云和混合云的开放源代码解决方案。

## <a name="prerequisites"></a>先决条件 

* 具有活动订阅的 Azure 帐户。 若要获取 Azure 订阅，请激活[适用于 Visual Studio 订阅者的 Azure 额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或[免费创建帐户](https://azure.microsoft.com/pricing/free-trial)。

* JBoss EAP 安装。 需要有一个 Red Hat 帐户，该帐户具有针对 JBoss EAP 的 Red Hat 订阅管理 (RHSM) 权利。 此权利允许你下载 Red Hat 经过测试和认证的 JBoss EAP 版本。  

  如果没有 EAP 权利，请在开始之前获取 [JBoss EAP 计算订阅](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)。 若要新建 Red Hat 订阅，请转到 [Red Hat 客户门户](https://access.redhat.com/)并设置帐户。

* [Azure CLI](/cli/azure/overview)。

* RHEL 选项。 选择即用即付 (PAYG) 或自带订阅 (BYOS)。 如果使用 BYOS，需要在部署快速启动模板之前激活 [Red Hat Cloud Access](https://access.redhat.com/) RHEL 黄金映像。

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE 和 Jakarta EE 应用程序迁移

### <a name="migrate-to-jboss-eap"></a>迁移到 JBoss EAP
JBoss EAP 7.2 和 7.3 是 Java Enterprise Edition (Java EE) 8 和 Jakarta EE 8 规范的认证实现。 JBoss EAP 为高可用性 (HA) 群集、消息传递和分布式缓存等功能提供预先配置的选项。 它还允许用户使用 JBoss EAP 提供的各种 API 和服务来编写、部署和运行应用程序。  

有关 JBoss EAP 的详细信息，请参阅 [JBoss EAP 7.2 简介](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index)或 [JBoss EAP 7.3 简介](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)。

 #### <a name="applications-on-jboss-eap"></a>JBoss EAP 上的应用程序

* **Web 服务应用程序**。 Web 服务提供在软件应用程序之间进行互操作的标准方法。 每个应用程序都可以在不同的平台和框架上运行。 这些 Web 服务有助于内部和异类子系统的通信。 

  若要了解详细信息，请参阅[在 EAP 7.2 上开发 Web 服务应用程序](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index)或[在 EAP 7.3 上开发 Web 服务应用程序](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)。

* **企业 Java Beans (EJB) 应用程序**。 EJB 3.2 是一种用于开发分布式、事务性、安全且可移植的 Java EE 和 Jakarta EE 应用程序的 API。 EJB 使用称为 Enterprise Beans 的服务器端组件，以一种鼓励重用的分离方式实现应用程序的业务逻辑。 

  若要了解详细信息，请参阅[在 EAP 7.2 上开发 EJB 应用程序](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index)或[在 EAP 7.3 上开发 EJB 应用程序](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index)。

* **Hibernate 应用程序**。 开发人员和管理员可以通过 JBoss EAP 开发和部署 Java Persistence API (JPA) 和 Hibernate 应用程序。 Hibernate Core 是面向 Java 语言的对象关系映射框架。 它提供了一个用于将面向对象的域模型映射到关系数据库的框架，以便应用程序可以避免直接与数据库交互。 

  Hibernate 实体管理器实现 [JPA 2.1 规范](https://www.jcp.org/en/jsr/overview)定义的编程接口和生命周期规则。 此包装器结合了 Hibernate 注解，在成熟的 Hibernate Core 基础上实现了一个完整的（且独立的）JPA 解决方案。 
  
  若要了解有关 Hibernate 的详细信息，请参阅 [EAP 7.2 上的 JPA](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) 或 [EAP 7.3 上的 Jakarta Persistence](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api)。

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit for Applications
[Red Hat 适用于应用程序的迁移工具包 (MTA)](https://developers.redhat.com/products/mta/overview) 是面向 Java 应用程序服务器的迁移工具。 使用此工具可从另一台应用服务器迁移到 JBoss EAP。 它与适用于 [Eclipse IDE](https://www.eclipse.org/ide/) 的 IDE 插件、[Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) 以及适用于 Java 的 [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) 配合使用。 

MTA 是一个免费的开放源代码工具，可执行以下操作：
* 自动执行应用程序分析。
* 支持工作量估算。
* 加速代码迁移。
* 支持容器化。
* 与 Azure 工作负荷生成器集成。

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>将 JBoss EAP 从本地迁移到 Azure
RHEL 上的 JBoss EAP 的 Azure 市场产品/服务将在不到 20 分钟的时间内在 Azure VM 上安装和预配。 可以从 [Azure 市场](https://azuremarketplace.microsoft.com/)访问这些产品/服务。

此 Azure 市场产品/服务包含 EAP 和 RHEL 版本的各种组合，以满足你的要求。 JBoss EAP 始终为 BYOS，但对于 RHEL OS，你可以在 BYOS 或 PAYG 之间进行选择。 Azure 市场产品/服务包括在 RHEL 上作为独立 VM 或群集 VM 的 JBoss EAP 计划选项：

* RHEL 7.7 VM 上的 JBoss EAP 7.2 (PAYG)
* RHEL 8.0 VM 上的 JBoss EAP 7.2 (PAYG)
* RHEL 8.0 VM 上的 JBoss EAP 7.3 (PAYG)
* RHEL 7.7 VM 上的 JBoss EAP 7.2 (BYOS)
* RHEL 8.0 VM 上的 JBoss EAP 7.2 (BYOS)
* RHEL 8.0 VM 上的 JBoss EAP 7.3 (BYOS)

除了 Azure 市场产品/服务，还可以使用快速启动模板，开始 Azure 迁移之旅。 这些快速入门包括预生成的 Azure 资源管理器 (ARM) 模板和脚本，用于在 RHEL 上以各种配置和版本组合部署 JBoss EAP。 你将拥有：

* 负载均衡器。
* 用于负载均衡的专用 IP 和 VM。
* 包含单个子网的虚拟网络。
* VM 配置（群集或独立）。
* 示例 Java 应用程序。

这些模板的解决方案体系结构包括：

* 独立 RHEL VM 上的 JBoss EAP。
* 跨多个 RHEL VM 聚集的 JBoss EAP。
* 通过 Azure 虚拟机规模集聚集的 JBoss EAP。

#### <a name="linux-workload-migration-for-jboss-eap"></a>适用于 JBoss EAP 的 Linux 工作负载迁移
Azure 工作负荷生成器将对本地 Java 应用到 Azure 的概念证明、评估和迁移过程进行简化。 工作负荷生成器与 Azure Migrate 发现工具集成，以识别 JBoss EAP 服务器。 然后，它会动态生成用于 JBoss EAP 服务器部署的 Ansible playbook。 它使用 Red Hat MTA 工具将服务器从其他应用服务器迁移到 JBoss EAP。 

简化迁移的步骤包括：
1. **评估**。 使用 Azure VM 或虚拟机规模集评估 JBoss EAP 群集。
1. **评估**。 扫描应用程序和基础结构。
1. **基础结构配置**。 创建工作负荷配置文件。
1. **部署和测试**。 部署、迁移和测试工作负荷。
1. **部署后配置**。 与数据、监视、安全性、备份等集成。

## <a name="server-configuration-choice"></a>服务器配置选择

对于 RHEL VM 的部署，可以选择 PAYG 或 BYOS。 [Azure 市场](https://azuremarketplace.microsoft.com)中的映像默认为 PAYG。 如果你有自己的 RHEL OS 映像，请部署 BYOS 类型的 RHEL VM。 在部署 VM 或虚拟机规模集之前，请确保 RHSM 帐户具有通过 Cloud Access 获得的 BYOS 权利。

JBoss EAP 具有强大的管理功能，并向其应用程序提供功能和 API。 这些管理功能根据用于启动 JBoss EAP 的操作模式而有所不同。 它在 RHEL 和 Windows Server 上受支持。 JBoss EAP 提供独立服务器操作模式，用于管理离散实例。 它还提供了托管域操作模式，用于从单个控制点管理实例组。 

> [!NOTE]
> Microsoft Azure 不支持 JBoss EAP 托管域，因为 Azure 基础结构服务管理 HA 功能。 

环境变量 `EAP_HOME` 表示 JBoss EAP 安装的路径。 使用以下命令在独立模式下启动 JBoss EAP 服务：

```
$EAP_HOME/bin/standalone.sh
```
    
此启动脚本使用 EAP_HOME/bin/standalone.conf 文件来设置某些默认首选项，如 JVM 选项。 可以在此文件中自定义设置。 默认情况下，JBoss EAP 使用 standalone.xml 配置文件在独立模式下启动，但可以使用其他模式启动它。 

有关可用独立配置文件及如何使用它们的详细信息，请参阅[适用于 EAP 7.2 的独立服务器配置文件](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)或[适用于 EAP 7.3 的独立服务器配置文件](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)。 

若要使用其他配置启动 JBoss EAP，请使用 `--server-config` 参数。 例如：
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
有关所有可用启动脚本参数及其用途的完整列表，请使用 `--help` 参数。 有关详细信息，请参阅 [EAP 7.2 上的服务器运行时参数](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)或 [EAP 7.3 上的服务器运行时参数](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)。
    
JBoss EAP 还可以在群集模式下工作。 JBoss EAP 群集消息传递允许对 JBoss EAP 消息传递服务器进行分组，分担消息处理负载。 群集中的每个活动节点均为活动的 JBoss EAP 消息传递服务器，该服务器管理自己的消息并处理自己的连接。 若要了解详细信息，请参阅 [EAP 7.2 上的群集概述](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview)或 [EAP 7.3 上的群集概述](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview)。 

## <a name="support-and-subscription-notes"></a>支持和订阅说明
这些快速启动模板以如下形式提供： 

- RHEL OS 通过 Red Hat 黄金映像模型以 PAYG 或 BYOS 形式提供。
- JBoss EAP 仅以 BYOS 形式提供。

#### <a name="using-rhel-os-with-the-payg-model"></a>将 RHEL OS 与 PAYG 模型配合使用

默认情况下，这些快速启动模板使用 Azure 市场中的按需 RHEL 7.7 或 8.0 PAYG 映像。 除了正常的计算、网络和存储成本之外，PAYG 映像还有额外的 RHEL 小时订阅费用。 同时，实例会注册到 Red Hat 订阅。 这意味着你将使用你的一项权利。 

此 PAYG 映像将导致“双重计费”。 可以通过构建自己的 RHEL 映像来避免此问题。 若要了解详细信息，请阅读关于[如何预配适用于 Microsoft Azure 的 RHEL VM](https://access.redhat.com/articles/uploading-rhel-image-to-azure) 的 Red Hat 知识库文章。 或激活 [Red Hat Cloud Access](https://access.redhat.com/) RHEL 黄金映像。

有关 PAYG VM 定价的详细信息，请参阅 [Red Hat Enterprise Linux 定价](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/)。 若要在 PAYG 模型中使用 RHEL，你需要一个 Azure 订阅，该订阅具有 [Azure 市场中的 RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) 或 [Azure 市场中的 RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM) 的指定付款方式。 这些产品/服务需要在 Azure 订阅中指定付款方式。

#### <a name="using-rhel-os-with-the-byos-model"></a>将 RHEL OS 与 BYOS 模型配合使用

若要将 BYOS 用于 RHEL OS，需要具有有效的 Red Hat 订阅，该订阅具有在 Azure 中使用 RHEL OS 的权利。 在使用 BYOS 模型部署 RHEL OS 之前，请先完成以下先决条件：

1. 确保已将 RHEL OS 和 JBoss EAP 权利附加到 Red Hat 订阅。
2. 授权 Azure 订阅 ID 使用 RHEL BYOS 映像。 按照 [Red Hat 订阅管理文档](https://access.redhat.com/documentation/red_hat_subscription_management/1/)完成此过程，其中包括以下步骤：

   1. 在 Red Hat Cloud Access 仪表板中启用 Microsoft Azure 作为提供商。

   1. 添加 Azure 订阅 ID。

   1. 在 Microsoft Azure 上启用 Cloud Access 的新产品。
    
   1. 为 Azure 订阅激活 Red Hat 黄金映像。 有关详细信息，请参阅 [Microsoft Azure 上的 Red Hat 黄金映像](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access#proc_using-gold-images-azure_cloud-access)。

   1. 等待 Red Hat 黄金映像在 Azure 订阅中可用。 这些映像通常在提交后 3 小时内可用。
    
3. 接受 RHEL BYOS 映像的 Azure 市场条款和条件。 可以通过运行以下 Azure CLI 命令来完成此过程。 有关详细信息，请参阅 [Azure 中的 RHEL BYOS 黄金映像](./byos.md)文档。 务必运行最新 Azure CLI 版本。

   1. 打开 Azure CLI 会话，并使用 Azure 帐户进行身份验证。 如需帮助，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。

   1. 通过运行以下 CLI 命令，验证 RHEL BYOS 映像在订阅中是否可用。 如果此处未显示任何结果，请确保已为 RHEL BYOS 映像激活 Azure 订阅。
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. 运行以下命令，分别接受 RHEL 7.7 BYOS 和 RHEL 8.0 BYOS 的 Azure 市场条款：
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
订阅现已准备好在 Azure 虚拟机上部署 RHEL 7.7 或 8.0 BYOS。

#### <a name="using-jboss-eap-with-the-byos-model"></a>将 JBoss EAP 与 BYOS 模型配合使用

JBoss EAP 仅在 Azure 上通过 BYOS 模型提供。 部署此模板时，需要提供 RHSM 凭据以及具有有效 EAP 权利的 RHSM 池 ID。 如果没有 EAP 权利，请在开始之前获取 [JBoss EAP 评估订阅](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)。

## <a name="deployment-options"></a>部署选项

可通过以下方式部署模板：

- **PowerShell**。 通过运行以下命令部署模板： 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  有关如何安装和配置 Azure PowerShell 的信息，请参阅 [PowerShell 文档](/powershell/azure/)。  

- **Azure CLI**。 通过运行以下命令部署模板：

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  有关如何安装和配置 Azure CLI 的详细信息，请参阅[安装 CLI](/cli/azure/install-azure-cli)。

- **Azure 门户**。 可以通过转到 Azure 快速启动模板部署到 Azure 门户，如下一部分中所述。 在快速入门中，选择“部署到 Azure”或“在 GitHub 上浏览”按钮 。

## <a name="azure-quickstart-templates"></a>Azure 快速入门模板

可以从以下用于 RHEL 上的 JBoss EAP 的其中一个满足部署目标的快速启动模板开始：

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/">RHEL 上的 JBoss EAP（独立 VM）</a>。 此模板会将 Azure 上名为 JBoss-EAP 的 Web 应用部署到在 RHEL 7.7 或 8.0 VM 上运行的 JBoss EAP 7.2 或7.3。

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/">RHEL 上的 JBoss EAP（多个群集 VM）</a>。 此模板会将名为 eap-session-replication 的 Web 应用部署到在 n 个 RHEL 7.7 或 8.0 VM 上运行的 JBoss EAP 7.2 或 7.3 群集。 由用户确定 n 的值。 所有 VM 都将添加到负载均衡器的后端池。

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/">RHEL 上的 JBoss EAP（群集虚拟机规模集）</a>。 此模板会将名为 eap-session-replication 的 Web 应用部署到在 RHEL 7.7 或 8.0 虚拟机规模集上运行的 JBoss EAP 7.2 或 7.3 群集。

## <a name="resource-links"></a>资源链接

* [Azure 混合权益](../../windows/hybrid-use-benefit-licensing.md)
* [为 Azure 应用服务配置 Java 应用](../../../app-service/configure-language-java.md)
* [Azure Red Hat OpenShift 上的 JBoss EAP](https://azure.microsoft.com/services/openshift/)
* [Azure 应用服务上的 JBoss EAP (Linux)](../../../app-service/quickstart-java.md)
* [在 Azure 应用服务上部署 JBoss EAP](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>后续步骤

* 详细了解 [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)。
* 详细了解 [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)。
* 详细了解 [Red Hat 订阅管理](https://access.redhat.com/products/red-hat-subscription-management)。
* 了解 [Azure 上的 Red Hat 工作负荷](./overview.md)。
* [通过 Azure 市场在 RHEL VM 或虚拟机规模集上部署 JBoss EAP](https://aka.ms/AMP-JBoss-EAP)。
* [通过 Azure 快速入门模板在 RHEL VM 或虚拟机规模集上部署 JBoss EAP](https://aka.ms/Quickstart-JBoss-EAP)。
