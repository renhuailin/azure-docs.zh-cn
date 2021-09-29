---
title: 使用 Azure 开发测试实验室培训
description: 本文详细介绍了在 Azure 开发测试实验室中设置培训实验室可以遵循的步骤。
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: d72d87cf2108a5e6d372edbf698e6adc7b100618
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629249"
---
# <a name="use-azure-devtest-labs-for-training"></a>使用 Azure 开发测试实验室培训
除开发/测试外，Azure 开发测试实验室可用于执行许多关键方案。 这些方案之一是为培训设置实验室。 Azure 开发测试实验室允许创建实验室，可在其中提供自定义模板，每个学员都可使用该模板创建相同和隔绝的培训环境。 可应用策略以确保为每个学员提供培训环境（需要时）并包含培训所需的足够资源（例如虚拟机）。 最后，可轻松与学员共享实验室（学员通过单击即可访问）。

![使用开发测试实验室进行培训](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure 开发测试实验室满足在任意虚拟环境中进行培训所需的以下要求： 

* 学员无法看到由其他学员创建的 VM
* 每台培训计算机都应相同
* 学员可快速设置其培训环境
* 通过确保学员无法获取多于培训所需的资源和在不使用时关闭虚拟机来控制成本
* 轻松与学员共享培训实验室
* 反复重新使用培训实验室

将在本文中了解各种 Azure 开发测试实验室功能，这些功能可用于满足上述培训要求和设置实验室进行培训所需的详细步骤。  

## <a name="implementing-training-with-azure-devtest-labs"></a>使用 Azure 开发测试实验室执行培训
1. **创建实验室** 
   
    实验室是 Azure 开发测试实验室的起点。 创建实验室后，可执行多种任务，如将用户（学员）添加到实验室、设置策略来控制成本、定义 VM 映像（可快速创建）等。   
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [在 Azure 开发测试实验室中创建实验室](devtest-lab-create-lab.md) |了解如何在 Azure 门户中的 Azure 开发测试实验室中创建实验室。 |
2. **使用现成市场映像和自定义映像在几分钟内创建培训 VM** 
   
    可从 Azure 市场的多种映像中选择现成映像并将其提供给实验室中的学员。 如果现成的映像不能满足需求，可使用 Azure 市场中的现成映像创建实验室 VM、在培训所需的所有软件上安装和在实验室中将 VM 保存为自定义映像来创建自定义映像。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [配置 Azure 市场映像](devtest-lab-configure-marketplace-images.md) |了解如何允许 Azure 市场映像；同时仅提供对培训可用的映像以供选择。 |
   | [创建自定义映像](devtest-lab-create-template.md) |通过预先安装培训所需的软件创建自定义映像，让学员能够使用自定义映像快速创建 VM。 |
3. **为培训计算机创建可重复使用的模板** 
   
    Azure 开发测试实验室中的公式是用于创建 VM 的默认属性值的列表。 可通过选取映像、VM 大小（CPU 和 RAM 的组合）和虚拟网络在实验室中创建公式。 每个学员可在实验室中查看公式并将其用于创建 VM。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [管理用于创建 VM 的开发测试实验室公式](devtest-lab-manage-formulas.md) |了解如何通过选取映像、VM 大小（CPU 和 RAM 的组合）和虚拟网络创建公式。 |
4. **控制成本**
   
    Azure 开发测试实验室允许在实验室中设置策略以指定可在实验室中由学员创建的 VM 的最大数量。 
   
    如果要进行多天的培训，需要在一天的某个时间点停止所有 VM，并自动在第二天重新启动它们，则可通过在实验室中设置自动关机和自动启动策略轻松完成。 
   
    最后，完成培训后，可通过运行单一 PowerShell 脚本立即删除所有 VM。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [定义实验室策略](devtest-lab-set-lab-policy.md) |通过在实验室中设置策略来控制成本。 |
   | [使用 PowerShell 脚本删除所有实验室 VM](/azure/devtest-labs/devtest-lab-faq#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |培训完成后在一次操作中删除所有实验室。 |
5. **与每个学员共享实验室**
   
    可使用与学员共享的链接直接访问实验室。 学员甚至不需要具有 Azure 帐户，只要它们具有 [Microsoft 帐户](/azure/devtest-labs/devtest-lab-faq#what-is-a-microsoft-account)即可。 学员无法看到由其他学员创建的 VM。  
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [在 Azure 开发测试实验室中将学员添加到实验室](devtest-lab-add-devtest-user.md) |使用 Azure 门户将学员添加到培训实验室。 |
   | [使用 PowerShell 脚本将学员添加到实验室](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |使用 PowerShell 自动将学员添加到培训实验室。 |
   | [获取实验室的链接](/azure/devtest-labs/devtest-lab-faq#how-do-i-share-a-direct-link-to-my-lab) |了解如何通过超链接直接访问实验室。 |
6. **反复重新使用实验室** 
   
    可通过创建Resource Manager 模板自动化实验室创建（包括自定义设置）并将其用于反复创建相同实验室。 
   
    在以下表中单击链接可了解详细信息：
   
   | 任务 | 学习内容 |
   | --- | --- |
   | [使用 Resource Manager 模板创建实验室](/azure/devtest-labs/devtest-lab-faq#how-do-i-create-a-lab-from-a-resource-manager-template) |使用资源管理器模板在 Azure 开发测试实验室中创建实验室。 |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]