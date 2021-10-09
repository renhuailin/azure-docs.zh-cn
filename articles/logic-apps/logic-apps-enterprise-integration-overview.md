---
title: B2B 企业集成工作流
description: 了解如何使用 Azure 逻辑应用和 Enterprise Integration Pack 为企业集成生成自动化 B2B 工作流。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: overview
ms.date: 09/14/2021
ms.openlocfilehash: 91ea9eb37e7c4e2e97513a1496a52d2521e9f4b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652125"
---
# <a name="b2b-enterprise-integration-workflows-with-azure-logic-apps-and-enterprise-integration-pack"></a>使用 Azure 逻辑应用和 Enterprise Integration Pack 的 B2B 企业集成工作流

若要生成企业到企业 (B2B) 解决方案并在组织之间无缝通信，可以结合使用 [Azure 逻辑应用](logic-apps-overview.md)和 Enterprise Integration Pack (EIP) 来生成自动化的可缩放企业集成工作流。

## <a name="what-is-the-enterprise-integration-pack"></a>什么是 Enterprise Integration Pack？

如果你熟悉 Microsoft BizTalk Server 或 Azure BizTalk 服务，则也会熟悉 EIP 的概念，因此可以轻松地使用 B2B 功能。 但是，EIP 有一个主要区别，即在体系结构上基于集成帐户。 这些帐户是 Azure 中基于云的容器，可简化存储、管理和使用 B2B 项目（包括[贸易合作伙伴](logic-apps-enterprise-integration-partners.md)、[协议](logic-apps-enterprise-integration-agreements.md)、[映射](logic-apps-enterprise-integration-maps.md)、[架构](logic-apps-enterprise-integration-schemas.md)、[证书](logic-apps-enterprise-integration-certificates.md)等）进行 B2B 通信的方式。

借助这些项目，你可以通过将 Azure 逻辑应用与 [400 多个内置操作和托管连接器](/connectors/connector-reference/connector-reference-logicapps-connectors)结合使用，来生成 B2B 工作流和集成解决方案，其中包括云服务（如 Azure、Microsoft 和其他软件即服务 (SaaS) 应用）、本地系统和自定义应用。 例如，你可以使用内置代码执行操作和 Azure Functions 通过工作流来创建和运行自定义代码。 还可以使用支持以下行业标准的[企业集成连接器](../connectors/managed.md#enterprise-connectors)：

* 电子数据交换 (EDI)
* 企业应用程序集成 (EAI)

尽管各个组织使用不同的协议和格式进行 B2B 通信，但你仍然可以与其他组织以电子方式交换消息。 你可以使用支持行业标准协议（包括 [AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、[EDIFACT](logic-apps-enterprise-integration-edifact.md) 和 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)）的 EIP，将这些不同的格式转换为组织系统可以处理的格式。 还可以通过同时使用加密和数字签名来提高消息的安全性。

## <a name="what-do-i-need-to-get-started"></a>我需要哪些内容才能开始使用？

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)，用于存储你定义并想使用的 B2B 项目。

* B2B 项目，例如[贸易合作伙伴](logic-apps-enterprise-integration-partners.md)、[协议](logic-apps-enterprise-integration-agreements.md)、[映射](logic-apps-enterprise-integration-maps.md)、[架构](logic-apps-enterprise-integration-schemas.md)、[证书](logic-apps-enterprise-integration-certificates.md)等。

* 若要创建映射和架构，可以使用 [Microsoft Azure 逻辑应用企业集成工具扩展](https://aka.ms/vsenterpriseintegrationtools)和 Visual Studio 2019。 如果使用的是 Visual Studio 2015，则可以使用[适用于 Visual Studio 2015 2.0 的 Microsoft Azure 逻辑应用企业集成工具](https://aka.ms/vsmapsandschemas)扩展。

   > [!IMPORTANT]
   > 不要将此扩展与 BizTalk Server 扩展一起安装。 同时具有这两个扩展可能会导致意外行为。 请确保只安装其中一个扩展。

   > [!NOTE]
   > 在高分辨率监视器上，可能会在 Visual Studio 中遇到[地图设计器显示问题](/visualstudio/designers/disable-dpi-awareness)。 若要解决此显示问题，请[在 DPI 无感知模式下重启 Visual Studio](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process) 或添加 [DPIUNAWARE 注册表值](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry)。

创建集成帐户并添加项目后，可以通过创建逻辑应用资源开始生成 B2B 工作流。 如果你不熟悉逻辑应用，请尝试[创建一个基本逻辑应用工作流示例](quickstart-create-first-logic-app-workflow.md)。 还可以使用 [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)、[Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) 或 [PowerShell](/powershell/module/az.logicapp) 来创建、管理和部署逻辑应用。

> [!IMPORTANT]
> 如果使用“逻辑应用(消耗)”资源类型，必须将集成帐户链接到逻辑应用资源，然后才能选择要在工作流中使用的 B2B 项目。 但是，若要定义这些项目并将其添加到集成帐户，你还不需要用到逻辑应用资源。
>
> 如果使用“逻辑应用(标准)”资源类型，可以将架构和映射直接添加到逻辑应用资源，并在同一逻辑应用资源内的多个工作流中使用这些项目。 
> 你仍然需要一个集成帐户来存储其他项目，例如合作伙伴和协议，但不必再进行链接，因此此功能不存在。 有关这些资源类型的详细信息，请查看[什么是 Azure 逻辑应用 - 资源类型和主机环境](logic-apps-overview.md#resource-type-and-host-environment-differences)。

下图显示了开始生成 B2B 逻辑应用工作流的大致步骤：

![概念图显示了创建 B2B 逻辑应用工作流的必需步骤。](media/logic-apps-enterprise-integration-overview/overview.png)

## <a name="try-now"></a>立即试用

[部署一个用于发送和接收 AS2 消息的完全正常运行的示例逻辑应用](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/logic-app-as2-send-receive)

## <a name="next-steps"></a>后续步骤

* [创建贸易合作伙伴](logic-apps-enterprise-integration-partners.md)
* [创建协议](logic-apps-enterprise-integration-agreements.md)
* [添加架构](logic-apps-enterprise-integration-schemas.md)
* [添加映射](logic-apps-enterprise-integration-maps.md)
* [从 BizTalk 服务迁移](logic-apps-move-from-mabs.md)
