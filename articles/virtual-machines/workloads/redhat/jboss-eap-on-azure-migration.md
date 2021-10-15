---
title: JBoss EAP 到 Azure 虚拟机的虚拟机规模集迁移指南
description: 本指南提供有关如何将企业 Java 应用程序从另一台应用程序服务器迁移到 JBoss EAP 以及从传统的本地服务器迁移到 Azure RHEL VM 和虚拟机规模集的信息。
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 06/08/2021
ms.openlocfilehash: 188e40f9bc3d2d23035549578cdc6440af60a3e5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359228"
---
# <a name="how-to-migrate-java-applications-to-jboss-eap-on-azure-vms-and-virtual-machine-scale-sets"></a>如何将 Java 应用程序迁移到 Azure VM 和虚拟机规模集上的 JBoss EAP

本指南提供以下相关信息：如果云策略是按原样“直接迁移”Java 应用程序，如何将 [Red Hat JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) 上的企业 Java 应用程序从传统的本地服务器迁移到 Azure Red Hat Enterprise Linux (RHEL) 虚拟机 (VM) 和虚拟机规模集。 但是，如果你想要“提升并优化”，则可以使用 Red Hat 库中的 JBoss EAP 映像将容器化的应用程序迁移到 [Azure Red Hat OpenShift (ARO)](https://azure.microsoft.com/services/openshift/)，或将 Java 应用代码直接放入 Azure App Service 实例上的 JBoss EAP 中。

## <a name="best-practice-starting-with-azure-marketplace-offers-and-quickstarts"></a>Azure 市场产品/服务和快速入门的最佳做法

Red Hat 和 Microsoft 进行合作引入了一组 [Azure 市场产品/服务上的 JBoss EAP](https://aka.ms/AMP-JBoss-EAP)，为迁移到 Azure 提供坚实的基础。 请参阅文档以获取产品/服务列表和计划，然后选择与现有部署最匹配的产品/服务和计划。 请参阅有关 [Azure 上的 JBoss EAP 最佳做法](./jboss-eap-on-azure-best-practices.md)的文章

如果现有的产品/服务都不是很好的起点，则可以使用 Azure VM 和其他可用资源手动重现部署。 有关详细信息，请参阅[什么是 IaaS？](https://azure.microsoft.com/overview/what-is-iaas/)

### <a name="azure-marketplace-offers"></a>Azure 市场产品/服务

与 Microsoft 合作的 Red Hat 已在 Azure 市场中发布了以下产品/服务。 你可以从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/)或 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)访问这些产品/服务。 有关更多详细信息，请参阅有关如何[使用 Azure 市场产品/服务在 Azure VM 和虚拟机规模集上部署 Red Hat JBoss EAP](./jboss-eap-marketplace-image.md) 的文章。

这种市场产品/服务包括 JBoss EAP 和 RHEL 版本与灵活支持订阅模型的各种组合。 JBoss EAP 可作为自带订阅 (BYOS)，但对于 RHEL，你可以在 BYOS 或即用即付 (PAYG) 之间进行选择。 Azure 市场产品/服务包括作为独立虚拟机、群集虚拟机和群集虚拟机规模集的 RHEL 上的 JBoss EAP 计划选项。 6 个计划包括：

- RHEL 8.3 独立虚拟机上的 JBoss EAP 7.3 (PAYG)
- RHEL 8.3 独立虚拟机上的 JBoss EAP 7.3 (BYOS)
- RHEL 8.3 群集虚拟机上的 JBoss EAP 7.3 (PAYG)
- RHEL 8.3 群集虚拟机上的 JBoss EAP 7.3 (BYOS)
- RHEL 8.3 群集虚拟机规模集上的 JBoss EAP 7.3 (PAYG)
- RHEL 8.3 群集虚拟机规模集上的 JBoss EAP 7.3 (BYOS)

### <a name="azure-quickstart-templates"></a>Azure 快速入门模板

除了 Azure 市场产品/服务，还提供了快速启动模板，让你在 Azure 上体验 EAP。 这些快速入门包括预建的 ARM 模板和脚本，用于在 Azure 上以各种配置和版本组合部署 JBoss EAP。 解决方案体系结构包括：

- RHEL 独立虚拟机上的 JBoss EAP
- RHEL 群集虚拟机上的 JBoss EAP
- RHEL 群集虚拟机规模集上的 JBoss EAP

若要快速开始，请选择与 RHEL 上的 JBoss EAP 版本组合密切匹配的 Quickstart 模板之一。 有关详细信息，请参阅 [Azure 上的 JBoss EAP 快速入门](./jboss-eap-on-rhel.md)文档。 

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户 - 如果没有 Azure 订阅，可以激活 [Visual Studio 订阅订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)（以前称为 MSDN）或[免费创建帐户](https://azure.microsoft.com/pricing/free-trial)。

- JBoss EAP 安装 - 需要有一个 Red Hat 帐户，该帐户具有针对 JBoss EAP 的 Red Hat 订阅管理 (RHSM) 权利。 此权利允许你下载 Red Hat 经过测试和认证的 JBoss EAP 版本。  如果没有 EAP 权利，可以通过[适用于个人的 Red Hat 开发人员订阅](https://developers.redhat.com/register)注册免费的开发人员订阅。 注册后，你可以在 [Red Hat 客户门户](https://access.redhat.com/management/)中找到必要的凭据（池 ID）。

- **RHEL 选项** - 在即用即付 (PAYG) 或自带订阅 (BYOS) 之间进行选择。 若使用 BYOS，则在使用解决方案模板部署市场产品/服务之前需要激活 [Red Hat Cloud Access](https://access.redhat.com/) [RHEL 黄金映像](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/)。 按照[以下说明](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide)启用 RHEL 黄金映像，以在 Microsoft Azure 中使用。

- **[Azure 命令行界面 (CLI)](/cli/azure/overview)** 。

- **Java 源代码和 [Java 开发工具包 (JDK) 版本](https://www.oracle.com/java/technologies/javase-downloads.html)**

- **[基于 JBoss EAP 7.2 的 Java 应用程序](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/index)** 或 **[基于 JBoss EAP 7.3 的 Java 应用程序](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/development_guide/index#get_started_developing_applications)** 。

RHEL 选项 - 在 PAYG 或 BYOS 之间选择。 对于 BYOS，你将需要激活 [Red Hat Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index) RHEL 黄金映像，才能使用 Azure 市场产品/服务。 BYOS 产品/服务将显示在 Azure 门户的“专用产品/服务”部分中。 

**产品版本**

* [JBoss EAP 7.2](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2)
* [JBoss EAP 7.3](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3)
* [RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM)
* [RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)

## <a name="migration-flow-and-architecture"></a>迁移流和体系结构

本部分概述了用于将 JBoss EAP 应用程序从在 JBoss EAP 上运行的另一台应用程序服务器以及从传统的本地服务器迁移到 Microsoft Azure 云环境的免费工具。 

### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Red Hat 适用于应用程序的迁移工具包 (MTA)

建议你在计划周期开始时使用 Red Hat MTA 来迁移 Java 应用程序，然后再执行任何与 EAP 相关的迁移项目。 MTA 是工具的程序集，支持跨[广泛的转换和用例](https://developers.redhat.com/products/mta/use-cases)的大规模 Java 应用程序现代化和迁移项目。 它可以加快应用程序代码分析、支持工作量估算、加快代码迁移，以及帮助你将应用程序移动到云和容器。

:::image type="content" source="./media/migration-toolkit.png" alt-text="显示迁移工具包应用的仪表板页的图像。":::

Red Hat MTA 允许将应用程序从其他应用程序服务器迁移到 Red Hat JBoss EAP。

## <a name="pre-migration"></a>预迁移

若要确保迁移成功，请在开始之前完成以下各节中所述的评估和清点步骤。

### <a name="validate-the-compatibility"></a>验证兼容性

建议在规划迁移之前验证当前部署模型和版本。 如果当前版本不受支持，你可能需要对应用程序进行重大更改。

MTA 支持从第三方企业应用程序服务器（例如 Oracle WebLogic Server）迁移到 JBoss EAP，并升级到 JBoss EAP 的最新版本。

下表介绍了最常见的受支持迁移路径。

**表 - 支持的迁移路径：源到目标**

|源平台 ⇒ | JBoss EAP 6 | JBoss EAP 7 | Red Hat OpenShift | OpenJDK 8 & 11 | Apache Camel 3 | Spring Boot on RH Runtimes | Quarkus
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Oracle WebLogic Server | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| IBM WebSphere Application Server | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| JBoss EAP 4 | &#x2714; | &#x2714; | X<sup>1</sup> | &#x2714; | - | - | - |
| JBoss EAP 5 | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| JBoss EAP 7 | 空值 | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| Oracle JDK | - | - | &#x2714; | &#x2714; | - | - | - |
| Apache Camel 2 | - | - | &#x2714; | &#x2714; | &#x2714; | - | - |
| SpringBoot | - | - | &#x2714; | &#x2714; | - | &#x2714; | &#x2714; |
| Java 应用程序 | - | - | &#x2714; | &#x2714; | - | - | - |

<sup>1</sup> 虽然 MTA 目前并不提供此迁移路径的规则，但是 Red Hat 咨询可以帮助从任何源平台迁移到 JBoss EAP 7。

你还可以检查 MTA 的[系统要求](https://access.redhat.com/documentation/en/migration_toolkit_for_applications/5.0/html-single/introduction_to_the_migration_toolkit_for_applications/index#system_requirements_getting-started-guide)。

在规划迁移之前，请查看 [JBoss EAP 7.3 支持的配置](https://access.redhat.com/articles/2026253#EAP_73)和 [JBoss EAP 7.2 支持的配置](https://access.redhat.com/articles/2026253#EAP_72)。

若要获取当前的 Java 版本，请登录到服务器并运行以下命令：

```
java -version
```

### <a name="validate-operating-mode"></a>验证运行模式

RHEL、Windows Server 和 Oracle Solaris 支持 JBoss EAP。 JBoss EAP 以独立服务器运行模式运行以管理离散实例，或以托管域运行模式运行以从单个控制点管理实例组。

Microsoft Azure 不支持 JBoss EAP 托管域。 仅支持独立的 JBoss EAP 服务器实例。 请注意，Azure 支持使用独立 JBoss EAP 服务器配置 JBoss EAP 群集，这就是 Azure 市场产品/服务创建群集 VM 或虚拟机规模集的方式。

### <a name="inventory-server-capacity"></a>清点服务器容量

记录当前生产服务器的硬件（内存、CPU、磁盘等），以及平均和峰值请求计数和资源利用率。 无论选择了哪种迁移路径，都将需要此信息。 有关大小的其他信息，请访问[云服务的大小](../../../cloud-services/cloud-services-sizes-specs.md)。

### <a name="inventory-all-secrets"></a>清点所有机密

在出现“配置即服务”技术（如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 或 [Azure 应用程序配置](https://azure.microsoft.com/services/app-configuration/)）之前，没有有关“机密”的明确定义的概念。 你只能使用一组不同的配置设置，这些设置可以有效地充当我们现在所称的“机密”。 在应用服务器（如 JBoss EAP）中，这些机密位于许多不同的配置文件和配置存储中。 检查生产服务器上的所有属性和配置文件中是否有机密和密码。 请确保在 WAR 文件中检查 jboss-web.xml。 还可以在应用程序中查找包含密码或凭据的配置文件。 有关 Azure Key Vault 的其他信息，请访问 [Azure Key Vault 基本概念](../../../key-vault/general/basic-concepts.md)。

### <a name="inventory-all-certificates"></a>清点所有证书

记录用于公共 SSL 终结点的所有证书。 可以通过运行以下命令来查看生产服务器上的所有证书：

```cli
keytool -list -v -keystore <path to keystore>
```

### <a name="inventory-jndi-resources"></a>清点 JNDI 资源

- 清点所有 Java 命名和目录接口 (JNDI) 资源。 某些资源（如 Java 消息服务 (JMS) 消息代理）可能需要迁移或重新配置。

### <a name="insideyyour-application"></a>InsideyYour 应用程序 

检查 WEB-INF/jboss-web.xml 和/或 WEB-INF/web.xml 文件 。

### <a name="document-data-sources"></a>记录数据源

如果应用程序使用任何数据库，则需捕获以下信息：

* 数据源名称是什么？
* 连接池配置是什么？
* 在哪里可以找到 Java Database Connectivity (JDBC) 驱动程序 JAR 文件？

有关详细信息，请参阅 JBoss EAP 文档中的[关于 JBoss EAP 数据源](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.4/html/configuration_guide/datasource_management)。

### <a name="determine-whether-and-how-the-file-system-is-used"></a>确定是否使用以及如何使用文件系统

使用应用程序服务器上的文件系统需要重新配置，在极少数情况下需要体系结构更改。 文件系统可供 JBoss EAP 模块或应用程序代码使用。 可以标识以下部分所述的部分或全部方案。

**只读静态内容**

如果应用程序当前提供静态内容，则需为其提供一个备用位置。 可能需要考虑将静态内容移到 Azure Blob 存储，并添加 [Azure 内容分发网络 (CDN)](../../../cdn/index.yml)，方便用户在全球范围内快速下载。 有关详细信息，请参阅 [Azure 存储中的静态网站托管](../../../storage/blobs/storage-blob-static-website.md)和[快速入门：将 Azure 存储帐户与 Azure CDN 集成](../../../cdn/cdn-create-a-storage-account-with-cdn.md)。

**动态发布的静态内容**

如果应用程序允许那些通过应用程序上传/生成但在创建后不可变的静态内容，则可将上述 [Azure Blob 存储](../../../storage/blobs/index.yml)和 Azure CDN 与 [Azure 函数](../../../azure-functions/index.yml)配合使用，以便处理上传和 CDN 刷新操作。 我们提供了一个示例实现，用于[通过 Azure Functions 进行静态内容的上传和 CDN 预加载操作](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn)。

**动态或内部内容**

对于经常由应用程序写入和读取的文件（如临时数据文件），或者仅对应用程序可见的静态文件，可以将 [Azure 存储](../../../storage/index.yml)共享作为持久卷进行装载。 有关详细信息，请参阅[在 Azure Kubernetes 服务中动态创建永久性卷并将其用于 Azure 文件存储](../../../aks/azure-files-dynamic-pv.md)。

### <a name="determine-whether-a-connection-to-on-premises-is-needed"></a>确定是否需要连接到本地

如果应用程序需要访问任何本地服务，则需预配 Azure 的某个连接服务。 有关详细信息，请参阅[将本地网络连接到 Azure](/azure/architecture/reference-architectures/hybrid-networking/)。 或者，你需要重构应用程序，以便使用本地资源公开的公开可用的 API。

### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>确定 JMS 队列或主题是否正在使用中

如果应用程序使用 JMS 队列或主题，则需将其迁移到外部托管的 JMS 服务器。 Azure 服务总线和高级消息队列协议 (AMQP) 可成为那些使用 JMS 的项目的理想迁移策略。 有关详细信息，请访问[将 JMS 用于 Azure 服务总线和 AMQP 1.0](../../../service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp.md)，或[向 Azure 服务总线队列发送消息并从中接收消息 (Java)](../../../service-bus-messaging/service-bus-java-how-to-use-queues.md)

如果已配置 JMS 持久存储，则必须捕获其配置，并在迁移后应用它。

### <a name="determine-whether-your-application-is-composed-of-multiple-wars"></a>确定应用程序是否由多个 WAR 组成

如果应用程序由多个 WAR 组成，则应将这些 WAR 中的每一个都视为单独的应用程序，并通过本指南了解这其中的每个应用程序。

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>确定应用程序是否打包为 EAR

如果将应用程序打包为 EAR 文件，请务必检查 application.xml 文件并捕获配置。

### <a name="identify-all-outside-processes-and-daemons-running-on-the-production-servers"></a>确定在生产服务器上运行的所有外部进程和守护程序

如果在应用程序服务器外运行任何进程（如监视守护程序），则需消除它们或将它们迁移到其他位置。


## <a name="migration"></a>迁移

### <a name="provision-the-target-infrastructure"></a>预配目标基础设施

若要开始迁移，首先需要部署 JBoss EAP 基础设施。 可以使用多个选项部署

- [**Azure 虚拟机**](https://azure.microsoft.com/overview/what-is-a-virtual-machine/)
- [**Azure 虚拟机规模集**](../../../virtual-machine-scale-sets/overview.md)
- [**Azure 应用服务**](/azure/developer/java/ee/jboss-on-azure)
- [**适用于容器的 Azure Red Hat OpenShift (ARO)**](https://azure.microsoft.com/services/openshift)
- [**Azure 容器服务**](https://azure.microsoft.com/product-categories/containers/)

在构建环境之前，请参阅 Azure 市场入门部分以评估部署基础设施。

### <a name="perform-the-migration"></a>执行迁移

有一些工具可帮助进行迁移：

* [用于分析迁移应用程序的 Red Hat 应用程序迁移工具包](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#use_windup_to_analyze_applications_for_migration)。
* [用于迁移服务器配置的 JBoss 服务器迁移工具](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#migration_tool_server_migration_tool)

若要将服务器配置从较旧的 JBoss EAP 版本迁移到较新的 JBoss EAP 版本，你可以使用 [JBoss 服务器迁移工具](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#migrate_server_migration_tool_option)，也可以在[管理 CLI 迁移操作](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#migrate__migrate_operation_option)的帮助下执行手动迁移。

### <a name="run-red-hat-application-migration-toolkit"></a>运行 Red Hat 应用程序迁移工具包

可以[在交互模式下运行 JBoss 服务器迁移工具](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/using_the_jboss_server_migration_tool/index#migration_tool_server_run_interactive_mode)。 默认情况下，JBoss 服务器迁移工具以交互方式运行。 此模式允许你准确选择要迁移的服务器配置。

还可以[在非交互模式下运行 JBoss 服务器迁移工具](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/using_the_jboss_server_migration_tool/running_the_server_migration_tool#migration_tool_server_run_noninteractive_mode)。 此模式允许它在没有提示的情况下运行。

### <a name="review-the-result-of-jboss-server-migration-toolkit-execution"></a>查看 JBoss 服务器迁移工具包执行的结果

迁移完成后，查看 EAP_HOME/standalone/configuration/ 和 EAP_HOME/domain/configuration/ 目录中迁移的服务器配置文件。 有关详细信息，请访问[查看 JBoss 服务器迁移工具执行的结果](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/using_the_jboss_server_migration_tool/running_the_server_migration_tool#migration_tool_server_results)。

### <a name="expose-the-application"></a>公开应用程序

你可以使用以下适用于你的环境的方法公开应用程序。

* [创建公共 IP](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) 以访问服务器和应用程序。
* 在同一 VNet 中的不同子网（新子网）中，[在同一个虚拟网络 (VNet) 中创建一个跳板虚拟机](../../windows/quick-create-portal.md#create-virtual-machine)并通过跳板虚拟机访问服务器。 此跳板虚拟机可用于公开应用程序。
* 在不同的虚拟网络中[创建一个具有 VNet 对等互连的跳板虚拟机](../../windows/quick-create-portal.md#create-virtual-machine)，并使用[虚拟网络对等互连](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks)访问服务器和公开应用程序。
* 使用[应用程序网关](../../../application-gateway/quick-create-portal.md#create-an-application-gateway)公开应用程序
* 使用[外部负载均衡器](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources) (ELB) 公开应用程序。

## <a name="post-migration"></a>迁移后

实现在预迁移步骤中定义的迁移目标后，请执行某些端到端验收测试，验证一切是否按预期工作。 显然，迁移后增强功能的一些主题包括但不限于以下方面：

* 使用 Azure 存储提供装载到虚拟机的静态内容。 有关详细信息，请访问[对虚拟机执行附加或分离数据磁盘操作](../../../devtest-labs/devtest-lab-attach-detach-data-disk.md)
* 通过 Azure DevOps 将应用程序部署到已迁移的 JBoss 群集。 有关详细信息，请访问 [Azure DevOps 入门文档](/azure/devops/get-started)。
* 请考虑使用[应用程序网关](../../../application-gateway/index.yml)。
* 通过高级负载均衡服务增强网络拓扑。 有关详细信息，请访问[在 Azure 中使用负载均衡服务](../../../traffic-manager/traffic-manager-load-balancing-azure.md)。
* 利用 Azure 托管标识管理机密，并将基于角色的访问控制 (RBAC) 分配给 Azure 资源。 有关详细信息，请访问[什么是 Azure 资源的托管标识？](../../../active-directory/managed-identities-azure-resources/overview.md)
* 使用 Azure Key Vault 存储充当“机密”的任何信息。 有关详细信息，请访问 [Azure 密钥保管库基本概念](../../../key-vault/general/basic-concepts.md)。

## <a name="resource-links-and-support"></a>资源链接和支持

有关任何与支持相关的疑问、问题或自定义要求，请联系 [Red Hat 支持](https://access.redhat.com/support)或 [Microsoft Azure 支持](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

* 详细了解 [JBoss EAP](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/getting_started_with_jboss_eap_for_openshift_online/introduction)
* 详细了解 [Red Hat 订阅管理 (Cloud Access)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index)。
* 详细了解 [Azure 虚拟机](https://azure.microsoft.com/overview/what-is-a-virtual-machine/)
* 详细了解 [Azure 虚拟机规模集](../../../virtual-machine-scale-sets/overview.md)
* 详细了解 [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* 详细了解 [Linux 上的 Azure 应用服务](../../../app-service/overview.md#app-service-on-linux)
* 详细了解 [Azure 存储](../../../storage/common/storage-introduction.md)
* 详细了解 [Azure 网络](../../../networking/fundamentals/networking-overview.md)

## <a name="next-steps"></a>后续步骤
* [在 Azure 市场的 RHEL VM/VM 规模集上部署 JBoss EAP](https://aka.ms/AMP-JBoss-EAP)
* [为 Azure 应用服务配置 Java 应用](../../../app-service/configure-language-java.md)
* [如何将 JBoss EAP 部署到 Azure 应用服务](https://github.com/JasonFreeberg/jboss-on-app-service)教程
* [使用 Azure 应用服务迁移协助](https://azure.microsoft.com/services/app-service/migration-assistant/)
* [使用 Red Hat Migration Toolkit for Applications](https://developers.redhat.com/products/mta)
