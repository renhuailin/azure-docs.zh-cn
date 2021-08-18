---
title: Microsoft 安全代码分析加入指南
description: 了解如何加入和安装 Microsoft 安全代码分析扩展。 请参阅先决条件并查看其他资源。
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e5d9f30f2c5fad33f597ea3b977996ee75d4d1a1
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115941"
---
# <a name="onboarding-and-installing"></a>加入和安装

> [!Note]
> Microsoft 安全代码分析 (MSCA) 扩展将将于 2022 年 3 月 1 日停用。 在 2022 年 3 月 1 日之前，现有 MSCA 客户仍可访问 MSCA。 请参阅 [OWASP 源代码分析工具](https://owasp.org/www-community/Source_Code_Analysis_Tools)了解 Azure DevOps 中的替代选项。 对于计划迁移到 GitHub 的客户，可以查看 [Github 高级安全](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)。

Microsoft 安全代码分析入门先决条件：

- 一项符合条件的 Microsoft 统一支持产品/服务，如以下部分所述。
- Azure DevOps 组织。
- 将扩展安装到 Azure DevOps 组织的权限。
- 可同步到云托管的 Azure DevOps 管道的源代码。

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>加入 Microsoft 安全代码分析扩展

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>对购买 Microsoft 安全代码分析扩展感兴趣？

如果你有以下支持产品/服务之一，则若要购买或交换访问该扩展所需的现有小时数，请联系你的技术客户经理：

- 统一支持的“高级”层
- 统一支持的“性能”层
- 开发人员顶级支持
- 合作伙伴顶级支持
- 企业顶级支持

如果你没有上述支持协议中的一种，则可从我们的一个合作伙伴处购买该扩展。

**后续步骤：**

如果你满足上述资格，请联系以下列表中的某个合作伙伴，以购买 Microsoft 安全代码分析扩展。 否则，请联系 [Microsoft 安全代码分析支持](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)。

>**合作伙伴：**

- Zones - 联系人详细信息：cloudsupport@zones.com
- Wortell - 联系人详细信息：info@wortell.nl
- Logicalis - 联系人详细信息：logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>成为合作伙伴

Microsoft 安全代码分析团队寻求加入签署了合作伙伴顶级支持协议的合作伙伴。 合作伙伴需要帮助 Azure DevOps 客户以更安全的方式进行开发，方法是向那些希望购买扩展但未与 Microsoft 签署企业支持协议的客户销售扩展。 感兴趣的合作伙伴可在[此处](http://www.microsoftpartnersupport.com/msrd/opin)注册。

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>安装 Microsoft 安全代码分析扩展

1. 将扩展与 Azure DevOps 组织共享后，请转到 Azure DevOps 组织页。 `https://dev.azure.com/contoso` 是此类页的一个示例 URL。
1. 选择你的名称旁边右上角的购物袋图标，然后选择“管理扩展”。
1. 选择“共享”。
1. 选择 Microsoft 安全代码分析扩展，然后选择“安装”。
1. 从下拉列表中，选择要在其上安装扩展的 Azure DevOps 组织。
1. 选择“安装”  。 安装完成后，就可以开始使用扩展。

>[!NOTE]
> 即使你没有安装该扩展所需的访问权限，也可以继续执行安装步骤。 你可以在安装过程中从 Azure DevOps 组织管理员处请求访问权限。

安装该扩展后，安全开发生成任务会变得可见，并且可以添加到 Azure Pipelines 中。

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>将特定生成任务添加到 Azure DevOps 管道

1. 在 Azure DevOps 组织中打开团队项目。
1. 选择“管道” > “生成”。
1. 选择要向其中添加扩展生成任务的管道：
   - 新管道：选择“新建”，然后按照详细介绍的步骤创建新管道。
   - 编辑管道：选择现有管道，然后选择“编辑”开始编辑管道。
1. 选择“+”，转到“添加任务”窗格。
1. 通过列表或搜索框找到要添加的生成任务。 选择 **添加** 。
1. 指定任务所需的参数。
1. 将新的生成排队。
   >[!NOTE]
   >文件和文件夹路径是相对于源存储库根目录的路径。 如果你将输出文件和文件夹指定为参数，系统会将它们替换为我们已在生成代理上定义的常见位置。

> [!TIP]
>
> - 若要在你的生成之后运行分析，请将 Microsoft 安全代码分析生成任务置于你的生成的“发布生成项目”步骤之后。 这样一来，你的生成就可以在运行静态分析工具之前完成并发布结果。
> - 对于安全开发生成任务，请始终选择“出错时继续”。 即使某个工具出现故障，其他工具也能运行。 工具之间没有任何相互依赖关系。
> - 仅当工具无法成功运行时，Microsoft 安全代码分析生成任务才会失败。 但即使工具在代码中发现问题，这些任务仍会成功。 使用后期分析生成任务，可以将生成配置为在工具发现代码中的问题后失败。
> - 通过发布管道运行时，某些 Azure DevOps 生成任务不受支持。 更具体地说，Azure DevOps 不支持那些在发布管道内发布项目的任务。
> - 有关 Azure DevOps Team Build 中可以指定为参数的预定义变量的列表，请参阅 [Azure DevOps 生成变量](/azure/devops/pipelines/build/variables?tabs=batch)。

## <a name="next-steps"></a>后续步骤

若要详细了解如何配置生成任务，请参阅我们的[配置指南](security-code-analysis-customize.md)或 [YAML 配置指南](yaml-configuration.md)。

对于所提供的扩展和工具，如果仍有疑问，请查看我们的[常见问题解答页](security-code-analysis-faq.yml)。