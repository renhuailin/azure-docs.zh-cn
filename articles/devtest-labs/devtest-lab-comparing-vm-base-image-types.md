---
title: 比较开发测试实验室中的自定义映像和公式 | Microsoft Docs
description: 了解自定义映像和公式（例如 VM 基项）之间的差异，以便确定哪一种最适合自己的环境。
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: f1fdb46decda2f72e64eff95cf57dba002168943
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634001"
---
# <a name="compare-custom-images-and-formulas-in-devtest-labs"></a>比较自定义映像和 DevTest 实验室中的公式
在[创建新的虚拟机](devtest-lab-add-vm.md)时，[自定义映像](devtest-lab-create-template.md) 和 [公式](devtest-lab-manage-formulas.md) 都可以用作基项。  自定义映像和公式之间的主要区别在于自定义映像只是基于 VHD 的映像，而公式不仅是基于 VHD 的映像，*还有* 预配置的设置 - 如 VM 大小、虚拟网络、子网和项目。 使用可在创建 VM 时重写的默认值，对这些预配置进行设置。 

本文介绍了使用自定义映像与使用公式的各自优缺点。  还可以阅读[如何通过 VM 创建自定义映像](devtest-lab-create-custom-image-from-vm-using-portal.md)和[常见问题解答](devtest-lab-faq.yml)。

## <a name="custom-image-benefits"></a>自定义映像的优点
自定义映像以静态、不可变的方式，从所需的环境中创建 VM。 

|优点|缺点|
|----|----|
|<li>从映像创建虚拟机后，一切都没有改变，因此预配速度很快。 换而言之，没有要应用的设置，因为自定义映像只是没有设置的映像。 <li>从单一的自定义映像创建的 VM 都是相同的。|<li>如果需要更新自定义映像的某些方面，则必须重新创建映像。 |

## <a name="formula-benefits"></a>公式的优点
  
公式提供了灵活的方式，从所需的配置或设置创建虚拟机。

|优点|缺点|
|----|----|
|<li>可以通过项目快速捕获环境中的更改。 例如，如果想安装拥有发布管道最新位的 VM 或登记存储库中的最新代码，只需指定部署最新位或登记公式中的最新代码及 <li>目标基本映像的项目。 只要此公式用于创建虚拟机，最新的位或代码就会部署/登记到 VM。  <li>公式可以定义默认设置，而自定义映像则不能提供 - 如 VM 大小和虚拟网络设置等。  <li>保存在公式中的设置显示如下所示的默认值，但创建 VM 时可以进行修改。 |<li> 相比从自定义映像创建虚拟机，从公式创建虚拟机可能需要更多的时间。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
