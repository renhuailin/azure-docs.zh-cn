---
title: Azure 上的 Red Hat JBoss EAP 最佳做法
description: 本指南提供了有关在 Microsoft Azure 中使用 Red Hat JBoss Enterprise Application Platform 的最佳做法的信息。
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 195a0bfa-dff1-429b-b030-19ca95ee6abe
ms.date: 06/08/2021
ms.openlocfilehash: 7c61422a301c32f6ccb9c24779a9f304991a6c7b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772805"
---
# <a name="red-hat-jboss-eap-on-azure-best-practices"></a>Azure 上的 Red Hat JBoss EAP 最佳做法

《Azure 上的 Red Hat JBoss EAP 最佳做法》指南，介绍了在 Microsoft Azure 上使用 Red Hat JBoss Enterprise Application Platform (EAP)。 JBoss EAP 可与 Microsoft Azure 平台结合使用，前提是在特定的受支持配置中使用，以便能够在 Azure 中运行 JBoss EAP。 如果要手动配置群集 JBoss EAP 环境，请应用在 Azure 中使用 JBoss EAP 群集功能所需的特定配置。 本指南详细介绍了在 Microsoft Azure 中使用 JBoss EAP 时受支持的配置。

JBoss EAP 是兼容 Jakarta Enterprise Edition (EE) 8 的实现，符合 Web Profile 和 Full Platform 规范。 它还是 Java EE 8 规范的经认证实现。 当社区项目达到所需的功能完整性级别时，JBoss EAP 的主版本将在特定时间点从 WildFly 社区项目分叉。 在此之后，会发生一段较长的测试和产品化，在这一过程中，JBoss EAP 逐渐稳定、获得认证，并针对生产用途进行了增强。 在 JBoss EAP 主版本的生存期内，可能会挑拣选择的功能，并从社区项目反向移植。 然后，可以在同一主版本系列中的一系列增强功能的次要版本中提供这些功能。

## <a name="supported-and-unsupported-configurations-of-jboss-eap-on-azure"></a>Azure 上的 JBoss EAP 受支持和不受支持的配置

有关 EAP 可用于的操作系统 (OS)、Java 平台和其他受支持平台的详细信息，请参阅 [JBoss EAP 支持的配置](https://access.redhat.com/articles/2026253)文档。

Red Hat Cloud Access 计划支持使用 JBoss EAP 订阅在 Azure 虚拟机上安装 JBoss EAP，这是 Microsoft Azure 市场的按需即用即付 (PAYG) 操作系统。 在这种情况下，虚拟机操作系统订阅 Red Hat Enterprise Linux (RHEL) 与 JBoss EAP 订阅分开。 Red Hat Cloud Access 是一项 Red Hat 订阅功能，用于在 Red Hat 认证的云基础结构提供程序（如 Microsoft Azure）上提供对 JBoss EAP 的支持。 Red Hat Cloud Access 可用于以简单且经济高效的方式在传统的本地服务器和基于公共云的资源之间移动订阅。

你可以在[客户门户中找到有关 Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 的详细信息。 提醒：对于 Azure 市场上的任何 PAYG 产品/服务，无需使用 Red Hat Cloud Access。 

每个 Red Hat JBoss EAP 版本都在各种市场领先的操作系统、Java 虚拟机 (JVM) 和数据库组合下进行了测试并且均受支持。 Red Hat 根据你的订阅协议为受支持的配置和测试的集成提供生产和开发支持。 支持适用于物理环境和虚拟环境。 除了上述操作系统限制之外，请检查 [JBoss EAP 受支持的配置](https://access.redhat.com/articles/2026253)，例如，受支持的 Java 开发工具包 (JDK) 供应商和版本。 它提供了有关各种 JBoss EAP 版本（如 7.0、7.1、7.2 和7.3）受支持的配置的信息。 查看 [Microsoft Azure 的产品/配置矩阵](https://access.redhat.com/articles/product-configuration-for-azure)，以了解受支持的 RHEL 操作系统、虚拟机最低容量要求以及有关其他受支持的 Red Hat 产品的信息。 查看[经过认证云服务提供商/Microsoft Azure](https://access.redhat.com/ecosystem/cloud-provider/2068823) 以了解经过认证在 Microsoft Azure 中运行的 Red Hat 软件产品。

在 Microsoft Azure 环境中使用 JBoss EAP 时，有一些不受支持的功能，包括：

- 托管域 - 允许从单个控制点管理多个 JBoss EAP 实例。 目前 Microsoft Azure 不支持 JBoss EAP 托管域。 仅支持独立的 JBoss EAP 服务器实例。 在 Azure 中，支持使用独立 JBoss EAP 服务器配置 JBoss EAP 群集。

- 使用共享存储的 ActiveMQ Artemis 高可用性 (HA) - Microsoft Azure 中不支持使用 Artemis 共享存储的 JBoss EAP 消息 HA。

- `mod_custer` 播发 - 不能使用 JBoss EAP 作为 Undertow `mod_cluster` 代理负载均衡器。由于 Azure 用户数据报协议 (UDP) 多播限制，不支持 mod_cluster 播发功能。

## <a name="other-features-of-jboss-eap-on-azure"></a>Azure 上 JBoss EAP 的其他特性

JBoss EAP 为 HA 群集、消息和分布式缓存等功能提供预先配置的选项。 它还允许用户使用 JBoss EAP 提供的各种 API 和服务来编写、部署和运行应用程序。

- 兼容 Jakarta EE - 兼容 Jakarta EE 8，符合 Web Profile 和 Full Platform 规范。

- 符合 JAVA EE 标准 - 经过 Java EE 8 认证，符合 Web Profile 和 Full Platform 规范。

- 管理控制台和管理 CLI - 适用于独立服务器管理接口。 管理 CLI 还包括批处理模式，可以编写脚本并自动执行管理任务。 不建议直接编辑 JBoss EAP XML 配置文件。

- 简化的目录布局 - 模块目录包含所有应用程序服务器模块。 独立目录包含独立部署的工件和配置文件。

- 模块化类加载机制 - 模块按需加载和卸载。 模块化类加载机制可提高性能、提高安全性并降低启动和重新启动时间。

- 简化的数据源管理 - 与其他服务一样，部署数据库驱动程序。 此外，将使用管理控制台和管理 CLI 创建和管理数据源。

- 统一的安全框架 - Elytron 提供了一个统一的框架，可管理和配置独立服务器的访问权限。 还可用于配置部署到 JBoss EAP 服务器上的应用程序的安全访问。

## <a name="creating-your-microsoft-azure-environment"></a>创建 Microsoft Azure 环境

创建将在 Microsoft Azure 环境中托管 JBoss EAP 实例的虚拟机。 使用 Standard_A2 或更高规格的 Azure 虚拟机大小。 可以使用 Azure 按需 PAYG 高级映像创建虚拟机，也可手动创建自己的虚拟机。 例如，可以按如下所示部署 RHEL 虚拟机：

* 在 Azure 中使用按需 Marketplace RHEL 映像 - Azure 市场中提供了多个产品/服务，可在其中选择要设置 JBoss EAP 的 RHEL 虚拟机。 请访问[从 Azure 市场部署 RHEL 8 虚拟机](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/assembly_deploying-a-rhel-image-as-a-virtual-machine-on-microsoft-azure_cloud-content)。 在 Azure 市场中，可以通过两种方式来选择 RHEL 操作系统许可。 通过 Red Hat 黄金映像模型，选择“PAYG”或“自带订阅 (BYOS)”。 请注意，如果已使用 PAYG 计划部署 RHEL 虚拟机，则仅使用 JBoss EAP 订阅详细信息来向 Red Hat 订阅中订阅生成的部署。

* [手动创建和预配 Azure 的 RHEL 映像](https://access.redhat.com/articles/uploading-rhel-image-to-azure)。 使用 RHEL 每个主版本的最新次要版本。

对于 Microsoft Windows Server 虚拟机，请参阅有关在 Azure 中创建 Windows Server 虚拟机的 [Microsoft Azure 文档](../../windows/overview.md)。

## <a name="jboss-eap-installation"></a>JBoss EAP 安装

> [!NOTE]
>  如果在通过 Azure 市场来使用 RHEL 上的 JBoss EAP 产品/服务，则会自动为 Azure 环境安装和配置 JBoss EAP。

如果要将 EAP 手动部署到 Microsoft Azure 上部署的 RHEL 虚拟机，可遵循以下步骤。

设置虚拟机后，便可以安装 JBoss EAP。 用户需要访问 [Red Hat 客户门户](https://access.redhat.com)，这是一个集中式平台，包含了 Red hat 知识库 (KB) 和订阅资源。 如果没有 EAP 订阅，请通过[适用于个人的 Red Hat 开发人员订阅](https://developers.redhat.com/register)注册免费的开发人员订阅。 注册后，请在 [Red Hat 客户门户](https://access.redhat.com/management/)的“订阅”部分中查找凭据（池 ID）。 请注意，此订阅不适用于生产用途。

我们使用变量 EAP_HOME 来表示 JBoss EAP 的安装路径。 将此变量替换为 JBoss EAP 安装的实际路径。

> [!IMPORTANT]
> 可以通过多种不同的方式来安装 JBoss EAP。 在特定情况下，适用的最佳方法各不相同。 如果使用 Microsoft Azure 市场中的 RHEL 按需虚拟机，请使用 ZIP 或安装程序方法安装 JBoss EAP。 **不要向 Red Hat 订阅管理 (RHSM) 注册 RHEL 按需虚拟机，因为该虚拟机使用的是 PAYG 计费方法，这样会针对该虚拟机向你收费两次。

* **ZIP 安装** - ZIP 存档适用于在所有受支持的操作系统上安装。 如果希望手动提取实例，则应使用 ZIP 安装方法。 ZIP 安装将提供 JBoss EAP 的默认安装，以及要在安装之后完成的所有配置。 如果计划使用 JBoss Operations Network(ON) 服务器部署和安装 JBoss EAP 补丁，则应该使用 ZIP 安装方法安装目标 JBoss EAP 实例。 有关更多详细信息，请查看 [ZIP 安装](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#zip_installation)。

* **JAR 安装程序** - JAR 安装程序可以在控制台中运行，也可以作为图形向导运行。 这两个选项都提供了安装和配置服务器实例的分步说明。 JAR 安装程序是在所有受支持的平台上安装 JBoss EAP 的首选方法。 有关检查 [JAR 安装程序安装](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#installer_installation)的详细信息。

* **RPM 安装** - 在 RHEL6、RHEL7 和 RHEL8 受支持的安装上，可以使用 RPM 包来安装 JBoss EAP。 计划在 Azure 上的 RHEL 虚拟机上自动安装 EAP 时，RPM 安装方法最适合。 JBoss EAP 的 RPM 安装会安装作为服务运行 JBoss EAP 所需的所有内容。 有关检查 [RPM 安装](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#rpm_installation)的详细信息。

## <a name="other-offers-by-azure-and-red-hat"></a>Azure 和 Red Hat 的其他产品/服务

Red Hat 和 Microsoft 进行了合作，将一组 Azure 解决方案模板引入 Azure 市场，为迁移到 Azure 提供坚实的基础。 请参阅文档，了解产品/服务列表，并选择适当的环境。

### <a name="azure-marketplace-offers"></a>Azure 市场产品/服务

可以从 [Azure 市场](https://aka.ms/AMP-JBoss-EAP)访问这些产品/服务。

这种市场产品/服务包括 JBoss EAP 和 RHEL 版本与灵活支持订阅模型的各种组合。 JBoss EAP 始终为 BYOS，但对于 RHEL 操作系统，你可以在 BYOS 或 PAYG 之间进行选择。 Azure 市场产品/服务包括作为独立虚拟机、群集虚拟机和群集虚拟机规模集的 RHEL 上的 JBoss EAP 计划选项。 

这六个计划包括：

- RHEL 8.3 独立虚拟机上的 JBoss EAP 7.3 (PAYG)
- RHEL 8.3 独立虚拟机上的 JBoss EAP 7.3 (BYOS)
- RHEL 8.3 群集虚拟机上的 JBoss EAP 7.3 (PAYG)
- RHEL 8.3 群集虚拟机上的 JBoss EAP 7.3 (BYOS)
- RHEL 8.3 群集虚拟机规模集上的 JBoss EAP 7.3 (PAYG)
- RHEL 8.3 群集虚拟机规模集上的 JBoss EAP 7.3 (BYOS)

:::image type="content" source="./media/red-hat-marketplace-image-1.png" alt-text="图像显示了使用 GitHub 部署链接部署 Red Hat 映像的选项。":::

### <a name="azure-quickstart-templates"></a>Azure 快速入门模板

除了 Azure 市场产品/服务，还提供了快速启动模板，让你在 Azure 上体验 EAP。 这些快速入门包括预建的 ARM 模板和脚本，用于在 Azure 上以各种配置和版本组合部署 JBoss EAP。 

解决方案体系结构包括：

* RHEL 独立虚拟机上的 JBoss EAP
* RHEL 群集虚拟机上的 JBoss EAP
* RHEL 群集虚拟机规模集上的 JBoss EAP

    :::image type="content" source="./media/red-hat-marketplace-image.png" alt-text="图像通过 Azure 市场提供的 Red Hat 产品/服务。":::

可以在 RHEL 7.7 和 8.0 与 JBoss EAP 7.2 和 7.3 之间选择。 可以选择以下组合之一进行部署：

- RHEL 7.7 上的 JBoss EAP 7.2
- RHEL 8.0 上的 JBoss EAP 7.2
- RHEL 8.0 上的 JBoss EAP 7.3

若要开始，请在满足你的部署目标的 RHEL 上选择具有匹配 JBoss EAP 的快启动模板。 下面是可用快速启动模板的列表。

* [RHEL 独立虚拟机上的 JBoss EAP](https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/) - Azure 模板在运行于 RHEL（版本 7.7 或 8.0）虚拟机的 JBoss EAP（版本 7.2 或 7.3）上的 Azure 上部署了一个名为 JBoss-EAP 的 Web 应用程序。

* [RHEL 群集虚拟机上的 JBoss EAP](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/) - Azure 模板在运行于 n 号 RHEL 虚拟机的 JBoss EAP 群集上部署了一个名为 eap-session-replication 的 Web 应用程序。 “n”是指你在开始时设置虚拟机的起始编号。 所有虚拟机都将添加到负载均衡器的后端池。

* [RHEL 群集虚拟机规模集上的 JBoss EAP](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-vmss-rhel/) - Azure 模板在运行于 RHEL 7.7 或 8.0 虚拟机规模集实例的 JBoss EAP7.2 或 7.3 集群上部署一个名为 eap-session-replication 的 Web 应用程序。

## <a name="configuring-jboss-eap-to-work-on-cloud-platforms"></a>配置 JBoss EAP 以在云平台上工作

在虚拟机中安装了 JBoss EAP 之后，便可以将 JBoss EAP 配置为作为服务运行。 如何将 JBoss EAP 配置为作为服务运行取决于 JBoss EAP 的安装方法和虚拟机 OS 类型。 请注意，JBoss EAP 的 RPM 安装会安装作为服务运行 JBoss EAP 所需的所有内容。 有关详细信息，请参阅[将 JBoss EAP 配置为作为服务运行](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#configuring_jboss_eap_to_run_as_a_service)。

### <a name="starting-and-stopping-jboss-eap"></a>启动和停止 JBoss EAP

#### <a name="starting-jboss-eap"></a>启动 JBoss EAP

RHEL 和 Windows Server 上均支持 JBoss EAP，并且仅在独立服务器操作模式下运行。 用于启动 JBoss EAP 的具体命令取决于基础平台。 服务器最初以挂起状态启动，在启动所有必需的服务之前，不会接受任何请求。 此时，服务器处于正常运行状态，可以开始接受请求。 下面是将 JBoss EAP 作为独立服务器启动的命令：

- 在 RHEL 虚拟机中作为独立服务器启动 JBoss EAP（通过 ZIP 或安装程序安装）的命令：
    ```
    $EAP_HOME/bin/standalone.sh
    ```
- 对于 Windows Server，请使用 `EAP_HOME\bin\standalone.bat` 脚本作为独立服务器来启动 JBoss EAP。
- 与 ZIP 或 JAR 安装程序安装相比，针对 RPM 安装，JBoss EAP 的启动方式有所不同。 例如，对于 RHEL 7 和更高版本，请使用以下命令：
    ```
    systemctl start eap7-standalone.service
    ```
用于启动 JBoss EAP（通过 ZIP 或安装程序方法进行安装）的启动脚本使用 `EAP_HOME/bin/standalone.conf` 文件，对于 Windows Server，则使用 `standalone.conf.bat` 来设置一些默认首选项，如 JVM 选项。 自定义此文件中的设置。 JBoss EAP 默认使用 `standalone.xml` 配置文件，但也可以使用其他配置文件启动。 要更改用于启动通过 RPM 方法安装的 JBoss EAP 的默认配置文件，请使用 `/etc/opt/rh/eap7/wildfly/eap7-standalone.conf`。 使用相同的 eap7-standalone.conf 文件进行其他配置更改，例如 WildFly 绑定地址。

有关可用的独立配置文件及其使用方法的详细信息，请查看[独立服务器配置文件](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#standalone_server_configuration_files)。

若要使用不同的配置启动 JBoss EAP，请使用 --server-config 参数。 有关所有可用启动脚本参数及其用途的完整列表，请使用 --help 参数或查看[服务器运行时参数](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#reference_of_switches_and_arguments_to_pass_at_server_runtime)

#### <a name="stopping-jboss-eap"></a>停止 JBoss EAP

停止 JBoss EAP 的方式取决于其启动方式。 在 JBoss EAP 启动的终端中按下 `Ctrl+C` 以停止 JBoss EAP 的交互式实例。 要停止 JBoss EAP 的后台实例，请使用管理 CLI 连接到正在运行的实例并关闭服务器。 有关更多详细信息，请查看[停止 JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#stopping_jboss_eap)。

与 ZIP 或安装程序安装相比，针对 RPM 安装，JBoss EAP 的停止方式有所不同。 用于停止 JBoss EAP 的 RPM 安装的命令取决于要启动的操作模式以及正在运行的 RHEL 版本。 独立模式仅在 Azure 中受支持。 

- 例如，对于 RHEL 7 和更高版本，请使用以下命令：
    ```
    systemctl stop eap7-standalone.service
    ```
还可以以宽限方式挂起或关闭 JBoss EAP，允许正常完成活动请求，但不接受任何新请求。 服务器挂起后，可以将其关闭、返回运行状态或保持挂起状态以执行维护。 服务器挂起时，仍会处理管理请求。 可以使用管理控制台或管理 CLI 挂起并恢复服务器。

### <a name="configuring-jboss-eap-subsystems-to-work-on-cloud-platforms"></a>配置 JBoss EAP 子系统以在云平台上工作

向部署到 JBoss EAP 的应用程序公开的许多 API 和功能都组织到子系统中。 管理员可以将这些子系统配置为提供不同的行为，具体取决于应用程序的目标。 有关子系统的更多详细信息，请查看 [JBoss EAP 子系统](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#jboss_eap_subsystems)。

某些 JBoss EAP 子系统需要特定配置才能在云平台上正常运行。 由于 JBoss EAP 服务器绑定到云虚拟机的专用 IP 地址，因此需要某种配置。 专用 IP 仅在云平台中可见。 对于某些子系统，专用 IP 地址需要映射到服务器的公共 IP 地址，可以从云外部看到这些公共 IP 地址。 要详细了解如何修改这些子系统，请查看[配置 JBoss EAP 子系统以在云平台上运行](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#configuring_subsystems_for_cloud_platforms)

## <a name="using-jboss-eap-high-availability-in-microsoft-azure"></a>在 Microsoft Azure 中使用 JBoss EAP 高可用性

Azure 不支持基于 UDP 多播的 JGroups 发现协议。 默认情况下，JGroup 使用 UDP 堆栈，请确保将堆栈更改为 TCP，因为 Azure 不支持 UDP。 尽管可以使用 TCPPING、JDBC_PING 等其他 JGroups 发现协议，但建议使用为 Azure 开发的共享文件发现协议，也就是 *Azure_PING*。

*AZURE_PING* 在 Microsoft Azure 存储帐户中使用通用 Blob 容器。 如果您还没有 AZURE_PING 可以使用的 Blob 容器，请创建一个虚拟机可以访问的 Blob 容器。 有关更多信息，请查看[在 Microsoft Azure 中配置 JBoss EAP 高可用性](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#using_jboss_eap_high_availability_in_microsoft_azure)。

为 JBoss EAP 配置负载均衡环境。 确保所有均衡器和工作程序都绑定到内部 Microsoft Azure 虚拟网络 (VNet) 中的可访问 IP 地址。 有关负载均衡配置的详细信息，请查看[在 Microsoft Azure 中安装和配置 Red Hat Enterprise Linux 7.4（和更高版本）高可用性群集](https://access.redhat.com/articles/3252491)。

## <a name="other-best-practices"></a>其他最佳做法

- 作为虚拟机上 JBoss EAP 设置的管理员，确保虚拟机安全非常重要。 这将显著降低来宾和主机操作系统被恶意软件感染的风险。 保护虚拟机可减少对 JBoss EAP 的攻击，并减少 JBoss EAP 上托管的应用程序故障。 使用[基于 Azure 角色的访问控制 (RBAC)]/azure/role-based-access-control/overview) 中的 [Azure Policy](https://azure.microsoft.com/services/azure-policy/) 和 [Azure 内置角色](../../../role-based-access-control/built-in-roles.md)等功能来控制对 Azure 虚拟机的访问。 通过安装 Microsoft Antimalware 或 Microsoft 合作伙伴的端点保护解决方案，并将防恶意软件解决方案与 [Azure Security Center](https://azure.microsoft.com/services/security-center/) 集成，以监视您的保护状态，从而保护你的虚拟机免受恶意软件的攻击。 在 RHEL 虚拟机中，可以通过阻止端口转发和阻止根登录（可在 */ssh/sshd_config* 中禁用）来防范这种情况。

- 使用环境变量，你可以在 Azure 虚拟机上轻松且流畅地体验 JBoss EAP。 例如，可以使用 EAP_HOME 来表示 JBoss EAP 安装的路径，该路径将多次使用。 在这种情况下，环境变量会十分方便。 环境变量也是配置服务和处理 Web 应用程序机密的常用方法。 使用 export 命令从 shell 设置某个环境变量时，用户的会话结束时，这个环境变量也随之结束。 如果我们需要变量跨会话持久保存时，这会造成问题。 为了让用户的环境持久化，我们从用户的配置文件脚本中导出变量。 对于要在 bash_profile 中持久化的每个环境变量，添加 export 命令。 如果要为有权访问虚拟机的所有用户设置永久全局环境变量，可以将其添加到默认配置文件。 建议将全局环境变量存储在名为 `/etc/profile.d` 的目录中。 目录包含用于为整个系统设置环境变量的文件列表。 如果在 Windows Server 命令提示符下使用 set 命令设置系统环境变量，不会永久设置环境变量。 使用 *setx* 命令或者控制面板中的“系统接口”。

- 管理虚拟机更新和升级。 使用 Azure 自动化中的[更新管理](../../../automation/update-management/overview.md)解决方案，为部署在 Azure 中的 Windows 和 Linux 计算机管理操作系统更新。 快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。 更新虚拟机软件可确保重要的 Microsoft Azure、虚拟机监控程序驱动程序和软件包保持最新状态。 次要版本可以就地升级。 例如，从 RHEL 6.9 升级到 RHEL 6.10，或从 RHEL 7.3 升级到 RHEL 7.4。 可以通过运行 yum update 命令完成就地升级。 Microsoft Azure 不支持将主版本就地升级，例如，从 RHEL 6 升级到 RHEL 7。

- 使用 [Azure Monitor](../../../azure-monitor/data-platform.md) 来洞察资源的运行状况。 Azure Monitor 包括[资源诊断日志文件](../../../azure-monitor/essentials/platform-logs-overview.md)。 用于监视 VM 资源并识别可能会损害性能与可用性的潜在问题。 [Azure 诊断扩展](../../../azure-monitor/agents/diagnostics-extension-overview.md)可以在 Windows 虚拟机上提供监视和诊断功能。 在 Azure 资源管理器模板中包含该扩展来启用这些功能。 启用启动诊断，这是在虚拟机无法启动的情况下进行故障排除时要使用的重要工具。 在解决启动问题时，控制台输出和启动日志可以为 Red Hat 技术支持人员提供极大帮助。 在创建虚拟机或在现有虚拟机中，在 Microsoft Azure 门户中启用引导诊断。 启用后，可以查看虚拟机的控制台输出，并可下载启动日志以进行故障排除。

- 要确保安全通信，另一种方法是在[虚拟网络 (VNet)]/azure/virtual-network/virtual-networks-overview) 和[虚拟专用网络 (VPN)](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) 中使用专用终结点。 开放网络可供外部世界访问，因此容易受到恶意用户的攻击。 VNet 和 VPN 将仅限所选用户进行访问。 VNet 使用专用 IP 在同一范围内的服务器之间建立隔离的通信通道。 隔离通信允许同一帐户下的多个服务器交换信息和数据，而不会泄露到公共空间。 如果通过专用网络在本地执行此操作，则需连接到远程服务器。 有多种不同方法来连接到远程服务器，比如，在应用程序服务器所在的同一个 VNet 中使用 JumpVM/JumpBox，或者使用 [Azure 虚拟网络对等](../../../virtual-network/virtual-network-peering-overview.md)、[Azure 应用程序网关](../../../application-gateway/overview.md)、[Azure Bastion](https://azure.microsoft.com/services/azure-bastion) 等。 所有这些方法都可实现完全安全的专用连接，并可以连接多个远程服务器。

- 使用 [Azure 网络安全组 (NSG)](../../../virtual-network/network-security-groups-overview.md) 过滤进出 Azure VNet 中应用服务器的网络流量。 NSG 包含安全规则，这些规则可允许或拒绝多种 Azure 资源的入站和出站网络流量。 可以为每项规则指定源和目标、端口以及协议。 通过使用这些 NSG 规则保护 JBoss EAP 上的应用程序，并阻止或允许到 Internet 的端口。

- 为了改善在 Azure 上的 JBoss EAP 上运行的应用程序的功能，可以使用 Azure 中提供的 HA 功能。 可以使用 Azure 资源（如负载均衡器、应用程序网关或[虚拟机规模集](../../../virtual-machine-scale-sets/overview.md)）在 Azure 中实现 HA。 这些 HA 方法将提供冗余和性能改进，这允许你轻松地执行维护或更新应用程序实例，方法是将负载分发到另一个可用的应用程序实例。 为了满足客户的额外需求，可能需要增加运行应用程序的应用程序实例的数量。 虚拟机规模集还具有自动缩放功能，让应用程序能够随着需求的变化自动增加或减少。

## <a name="optimizing-the-jboss-eap-server-configuration"></a>优化 JBoss EAP 服务器配置

安装 JBoss EAP 服务器并创建管理用户后，可以优化服务器配置。 请确保在[性能优化指南](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/performance_tuning_guide/index)中查看有关如何优化服务器配置并避免在生产环境中部署应用程序时出现的常见问题的信息

## <a name="resource-links-and-support"></a>资源链接和支持

有关任何与支持相关的疑问、问题或自定义要求，请联系 [Red Hat 支持](https://access.redhat.com/support)或 [Microsoft Azure 支持](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

* 详细了解 [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/getting_started_with_jboss_eap_for_openshift_online/index)
* Red Hat 订阅管理器 (RHSM) [Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index)
* [Azure Red Hat OpenShift (ARO)](https://azure.microsoft.com/services/openshift/)
* [Azure 上的 Red Hat](./overview.md) 的 Microsoft Docs
* [Azure 中的 RHEL BYOS 黄金映像](./byos.md)
* Azure 上的 JBoss EAP [快速入门视频教程](https://www.youtube.com/watch?v=3DgpVwnQ3V4) 

## <a name="next-steps"></a>后续步骤

* [迁移到 Azure 上的 JBoss EAP 查询](https://aka.ms/JavaCloud)
* 在 [Azure 应用服务](/azure/developer/java/ee/jboss-on-azure)中运行 JBoss EAP
* 在 [Azure 市场](https://aka.ms/AMP-JBoss-EAP)的 RHEL VM/VM 规模集上部署 JBoss EAP
* 在 [Azure 快速入门](https://aka.ms/Quickstart-JBoss-EAP)的 RHEL VM/VM 规模集上部署 JBoss EAP
* 使用 Azure [应用服务迁移协助](https://azure.microsoft.com/services/app-service/migration-assistant/)
* 使用 Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta)
