---
title: 什么是 FHIR 服务？
description: FHIR 服务支持通过 FHIR Api 快速进行数据交换。 使用托管的云服务引入、管理和保存受保护健康信息 (PHI)。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 08/03/2021
ms.author: chrupa
ms.openlocfilehash: 5ddbef4874a5efd9f63a099f8da52204065b6b4a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779059"
---
# <a name="what-is-fhirreg-service"></a>什么是 FHIR &reg; 服务？

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Azure 医疗保健 Api (特此称为 FHIR service) 中的 FHIR 服务可通过快速医疗保健互操作性资源（ (FHIR®) Api，云中的托管平台即服务 (PaaS) 提供程序来快速交换数据。 借助于它，使用健康数据的任何人都可以更轻松地在云中引入、管理和保存受保护健康信息 ([PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html))： 

- 托管的 FHIR 服务，可在数分钟内在云中进行预配 
- 用于数据访问的 Azure 中基于 FHIR 的基于的终结点和 FHIR 格式的存储 Enterprise
- 高性能、低延迟
- 在兼容的云环境中安全管理受保护健康信息 (PHI)
- 用于移动和 Web 实现的 SMART on FHIR
- 使用基于角色的访问控制 (RBAC) 大规模控制自己的数据
- 对每个数据存储中的访问、创建、修改和读取进行审核日志跟踪

FHIR 服务允许你在几分钟内创建和部署 FHIR 服务器，以利用云的弹性缩放。 无论所管理的数据集是什么样的，Azure 服务支持 FHIR 服务的功能都可实现快速性能。

使用 FHIR API 和兼容的数据存储，可以安全地连接任何使用 FHIR API 的系统，并与之进行交互。  Microsoft 承担 PaaS 产品/服务中的操作、维护、更新和合规性要求，使你可以释放自己的操作和开发资源。 

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>使用 FHIR 发挥数据功能

医疗保健行业正迅速将健康数据转换为 [FHIR&reg;](https://hl7.org/fhir)（快速医疗保健互操作性资源）这一新兴标准。 FHIR 实现了具有标准化语义和数据交换的强大可扩展数据模型，可让使用 FHIR 的所有系统协同工作。  通过将数据转换为 FHIR，可以快速连接现有数据源，如电子健康记录系统或研究数据库。 FHIR 还支持在移动和 Web 开发的新式实现中快速交换数据。 最重要的是，FHIR 可以通过分析和机器学习工具简化数据引入并加速开发。  

### <a name="securely-manage-health-data-in-the-cloud"></a>在云中安全地管理健康数据

FHIR 服务允许基于 HL7 FHIR 规范通过一致的 RESTful、FHIR Api 来交换数据。 在 Azure 中的托管 PaaS 产品/服务的支持下，它还提供了可缩放的和安全的环境，用于以本机 FHIR 格式管理和存储受保护健康信息 (PHI) 数据。  

### <a name="free-up-your-resources-to-innovate"></a>释放资源进行创新

你可以投资构建并运行自己的 FHIR 服务器，但在 Azure 医疗保健 Api 中使用 FHIR 服务时，Microsoft 会考虑操作、维护、更新和合规性要求的工作负荷，使你能够释放你自己的操作和开发资源。

### <a name="enable-interoperability-with-fhir"></a>实现与 FHIR 的互操作性

使用 FHIR 服务可以将使用 FHIR Api 的任何系统连接到读、写、搜索和其他函数。 它可以用作一种功能强大的工具，利用来自电子健康记录、临床医生和患者仪表板、远程监控程序的临床数据或系统外部具有 FHIR API 的数据库来整合、规范化和应用机器学习。

### <a name="control-data-access-at-scale"></a>大规模控制数据访问

你可以控制你的数据。 使用基于角色的访问控制 (RBAC)，可管理存储和访问数据的方式。 为了提高安全性并减少管理工作负载，可以根据为环境创建的角色定义来确定谁有权访问创建的数据集。  

### <a name="secure-your-data"></a>保护数据

通过无与伦比的安全智能保护 PHI。 数据被隔离到每个 API 实例的唯一数据库中，并通过多区域故障转移进行保护。 FHIR 服务为你的数据实现分层的深度防御和高级威胁防护。  

## <a name="applications-for-the-fhir-service"></a>FHIR 服务的应用程序

FHIR 服务器是实现健康数据互操作性的关键工具。 FHIR 服务被设计为可快速创建、部署和开始使用的 API 和服务。 随着 FHIR 标准在医疗保健中的扩展，用例将继续增长，但 FHIR 服务有用的一些初始客户应用程序如下： 

- **启动/IoT 和应用开发：**  (移动或 web) 开发患者或以提供者为中心的应用的客户可以利用 FHIR 服务作为完全托管的后端服务。 FHIR 服务提供了一项重要的资源，客户可以在为运行状况数据设计的安全云环境中管理数据和交换数据、利用 SMART on FHIR 实现准则，并使所有提供程序系统都能使用其技术 (例如，大多数 EHRs 已启用 FHIR 读取 Api) 。   

- **医疗保健生态系统：** 尽管 EHR 在许多临床环境中作为主要的“真实数据来源”而存在，但提供者拥有多个未相互连接的数据库或以不同格式存储数据的情况并不少见。  使用 FHIR service 作为服务，该服务位于这些系统的顶层，使你能够以 FHIR 格式对数据进行标准化。  这有助于以一致的数据格式实现跨多个系统的数据交换。 

- **研究：** 医疗保健研究人员一般会发现 FHIR 标准，并且 FHIR 服务非常有用，因为它会在公共 FHIR 数据模型的周围规范化数据，并减少机器学习和数据共享的工作负荷。
通过 FHIR 服务 Exchange 数据提供审核日志和访问控制，可帮助控制数据流以及谁有权访问哪些数据类型。 

## <a name="fhir-from-microsoft"></a>来自 Microsoft 的 FHIR

Microsoft 提供的 FHIR 功能有三种配置：

* Azure 医疗保健 Api 中的 FHIR 服务-Azure 中的 PaaS 产品，在 Azure 门户中轻松预配，由 Microsoft 管理。 包括在同一工作区中预配其他数据集（如 DICOM）的功能。 此功能在公共预览版中提供。 
* 适用于 FHIR 的 azure API-Azure 中的 PaaS 产品，在 Azure 门户中轻松预配，由 Microsoft 管理。 此实现仅包含 FHIR 数据，是一种 GA 产品。 
* 适用于 Azure 的 FHIR 服务器 - 可部署到 Azure 订阅中的开放源代码项目，可在 GitHub 上的 https://github.com/Microsoft/fhir-server 获得。

对于需要扩展或自定义 FHIR 服务器或需要访问基础服务（例如数据库）而无需通过 FHIR API 的用例，开发人员应选择适用于 Azure 的开放源代码 FHIR 服务器。  为了实现启用密钥、生产就绪的 FHIR API 和后端服务（其中，只应通过 FHIR API 访问持久数据），开发人员应选择 FHIR 服务。

## <a name="next-steps"></a>后续步骤

若要开始使用 FHIR 服务，请遵循5分钟的快速入门来部署 FHIR 服务。

>[!div class="nextstepaction"]
>[部署 FHIR 服务](fhir-portal-quickstart.md)
