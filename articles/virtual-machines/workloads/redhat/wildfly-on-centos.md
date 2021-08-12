---
title: 快速入门 - CentOS 上的 WildFly
description: 将 Java 应用程序部署到 CentOS VM 上的 WildFly
author: Theresa-Nguyen
ms.author: bicnguy
ms.date: 10/23/2020
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: 1f7bc4bcefe7535e531fcd8288a6dce0e93696e0
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113486114"
---
# <a name="quickstart-wildfly-on-centos-8"></a>快速入门：CentOS 8 上的 WildFly

本快速入门介绍如何部署 CentOS 8 虚拟机 (VM) 的 WildFly 的独立节点。 它非常适合用于在 Azure 上开发和测试企业 Java 应用程序。 部署此快速入门不需要应用程序服务器订阅。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 如果还没有 Azure 订阅，可激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或[创建免费帐户](https://azure.microsoft.com/pricing/free-trial)。

## <a name="use-case"></a>使用案例

WildFly 是在 Azure 上开发和测试企业 Java 应用程序的理想选择。 有关 WildFly 18 服务器配置文件中可用技术的列表，可参阅 [WildFly 入门指南](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly)。

可根据你的用例在独立模式或群集模式下使用 WildFly。 可在节点群集上通过 WildFly 确保关键的 Jakarta EE 应用程序的高可用性。 进行少量的应用程序配置更改，然后将应用程序部署到群集中。 若要了解相关详细信息，请查看 [WildFly 高可用性指南](https://docs.wildfly.org/18/High_Availability_Guide.html)。

## <a name="configuration-choice"></a>配置选择

可在“独立服务器”模式下启动 WildFly - 独立服务器实例是一个独立的进程，与 JBoss 应用程序服务器 (AS) 3、4、5 或 6 实例非常类似。 独立实例可通过 standalone.sh 或 standalone.bat 启动脚本来启动。 对于多个独立实例，由用户负责协调跨服务器进行的多服务器管理。

你还可使用配置文件夹中提供的配置文件，通过备用配置启动 WildFly 实例。

下面是独立服务器配置文件：

- standalone.xml（默认）- 此配置是用于启动 WildFly 实例的默认文件。 它包含带有所需技术的 Jakarta Web Profile 认证配置。
   
- standalone-ha.xml - 具有高可用性的 Jakarta EE Web Profile 8 认证配置（针对 Web 应用程序）。
   
- standalone-full.xml - Jakarta EE Platform 8 认证配置，包括托管 Jakarta EE 应用程序所需的全部技术。

- standalone-full-ha.xml - 具有高可用性的 Jakarta EE Platform 8 认证配置，用于托管 Jakarta EE 应用程序。

若要使用所提供的其他配置来启动独立 WildFly 服务器，请将 --server-config 参数与 server-config 文件一起使用。

例如，若要将 Jakarta EE Platform 8 与群集功能搭配，请使用以下命令：

```
./standalone.sh --server-config=standalone-full-ha.xml
```

若要详细了解相关配置，请参阅 [WildFly 入门指南](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations)。

## <a name="licensing-support-and-subscription-notes"></a>许可、支持和订阅说明

Azure CentOS 8 映像是即用即付 (PAYG) VM 映像，不要求用户获取许可证。 首次启动 VM 时，将自动激活 VM 的许可操作系统，并按小时费率收费。 这是对 Microsoft Linux 每小时 VM 费率的补充。 有关详细信息，请单击 [Linux VM 定价](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux)。 无需具备 Red Hat 订阅或许可证，即可免费下载和使用 WildFly。

## <a name="how-to-consume"></a>如何使用

可通过以下三种方式部署模板：

- 使用 PowerShell - 请运行以下命令来部署模板：（若要了解如何安装和配置 Azure PowerShell，请参阅 [Azure PowerShell](/powershell/azure/)）。

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/wildfly/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- 使用 Azure CLI - 请运行以下命令来部署模板：（若要详细了解如何安装和配置 Azure 跨平台命令行接口，请参阅 [Azure 跨平台命令行](/cli/azure/install-azure-cli)）。

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/wildfly/wildfly-standalone-centos8/azuredeploy.json
    ```

- 使用 Azure 门户 - 单击<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fwildfly%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">此处</a>部署模板，并登录到 Azure 门户。

## <a name="arm-template"></a>ARM 模板

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/wildfly/wildfly-standalone-centos8" target="_blank"> CentOS 8（独立 VM）上的 WildFly 18</a> - 这是一个快速入门模板，用于在资源组 (RG) 中的 CentOS 8 VM 上创建 WildFly 18.0.1.Final 的独立节点，其中该节点包含 VM 的专用 IP、虚拟网络和诊断存储帐户。 它还在 WildFly 的 Azure 上部署一个名为 JBoss-EAP 的示例 Java 应用程序。

## <a name="resource-links"></a>资源链接

* 详细了解 [WildFly 18](https://docs.wildfly.org/18/)
* 详细了解 [Azure 上的 Linux 发行版](../../linux/endorsed-distros.md)
* [Azure for Java 开发人员文档](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>后续步骤

对于生产环境，请查看 Red Hat JBoss EAP Azure 快速入门 ARM 模板：

包含示例应用程序的独立 RHEL 虚拟机：

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/jboss/jboss-eap-standalone-rhel" target="_blank"> RHEL 上的 JBoss EAP（独立 VM）</a>

包含示例应用程序的群集 RHEL 虚拟机：

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/jboss/jboss-eap-clustered-multivm-rhel" target="_blank"> RHEL 上的 JBoss EAP（群集 VM）</a>

包含示例应用程序的群集 RHEL 虚拟机规模集：

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/jboss/jboss-eap-clustered-vmss-rhel" target="_blank"> RHEL 上的 JBoss EAP（群集虚拟机规模集）</a>
