---
title: 将服务总线命名空间移到另一个区域 | Microsoft Docs
description: 本文介绍如何将 Azure 服务总线命名空间从当前区域移到另一个区域。
ms.topic: how-to
ms.date: 06/08/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: 9a752e845cf4b819e327bc922fc13199b79ab8d3
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111811430"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>将 Azure 服务总线命名空间移到另一个区域
在很多情况下，可能需要将现有服务总线命名空间从一个区域移到另一个区域。 例如，可能需要创建一个具有相同配置的命名空间，以便进行测试。 此外，还可能需要在另一个区域中再创建一个命名空间，作为[灾难恢复计划](service-bus-geo-dr.md)的一部分。

下面是概要步骤：

1. 将当前区域中的服务总线命名空间导出到 Azure 资源管理器模板。 
1. 更新模板中资源的位置。 此外，从模板中删除默认订阅筛选器，因为用户无法创建默认规则，系统会自动创建。 
1. 使用该模板将服务总线命名空间部署到目标区域。 
1. 验证部署，确保已在目标区域中创建命名空间、队列、主题和主题订阅。 
1. 处理完所有消息后，从源区域中删除命名空间，完成移动。 

## <a name="prerequisites"></a>先决条件
确保目标区域支持帐户使用的 Azure 服务总线和功能。
 
## <a name="prepare"></a>准备
若要开始，请导出资源管理器模板。 此模板包含描述服务总线命名空间的设置。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“所有资源”，然后选择你的服务总线命名空间。
3. 在“服务总线命名空间”页上，选择左侧菜单中的“自动化”下的“导出模版”。 
4. 选择“导出模板”页中的“下载”。 

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="下载资源管理器模板":::
5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。 此 zip 文件包含模板和参数 JSON 文件。 
1. 在解压缩的文件夹中打开 template.json 文件。 
1. 搜索 `location`，并将该属性的值替换为区域或位置的新名称。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。
1. 删除以下类型的资源定义：`Microsoft.ServiceBus/namespaces/topics/subscriptions/rules`。 别忘了删除此部分前面的逗号 (`,`) 字符，以确保 JSON 有效。  

    > [!NOTE]
    > 不能使用资源管理器模板为订阅创建默认规则。 在目标区域中创建订阅时，系统将自动创建默认规则。 

## <a name="move"></a>移动
部署模板以在目标区域中创建服务总线命名空间。 

1. 在 Azure 门户中，选择“创建资源”。
2. 在“搜索市场”中，键入“模板部署”作为搜索文本，选择“模板部署（使用自定义模板进行部署）”，然后按 ENTER。

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="新的模板部署":::    
1. 在“模板部署”页面上，选择“创建”。

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="新建模板部署 -“创建”按钮":::        
1. 在“自定义部署”页上，选择“在编辑器中生成自己的模板”。

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="在编辑器中生成自己的模板 - 链接":::            
1. 在“编辑模板”页的工具栏上选择“加载文件”，然后按照说明加载你在上一部分下载的 **template.json** 文件。

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="选择模板":::                
1. 选择“保存”以保存该模板。 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="保存模板":::                    
1. 在“自定义部署”页上执行以下步骤： 
    1. 选择 Azure 订阅。 
    2. 选择现有的 **资源组** 或创建一个资源组。 
    3. 选择目标位置或区域。 如果选择了现有资源组，则此设置为只读。 
    4. 输入新的 **命名空间名称**。
    1. 选择“查看 + 创建”。 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="部署资源管理器模板":::
    1. 在“查看 + 创建”页上，选择页面底部的“创建”。 
    
## <a name="verify"></a>验证
1. 在部署成功后，选择“转到资源组”。

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="“转到资源组”链接":::    
1. 在“资源组”页上，选择“服务总线命名空间”。 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="选择“服务总线命名空间”":::    
1. 在“服务总线命名空间”页上，验证是否可以看到源区域中的队列、主题和订阅。 
    1. 在右侧窗格底部的命名空间中可以看到“队列”。         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="命名空间中的“队列”":::
    2. 切换到“主题”选项卡，查看命名空间中的主题
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="命名空间中的“主题”":::
    3. 选择主题，验证是否已创建订阅。 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="主题订阅":::      
    
    

## <a name="discard-or-clean-up"></a>丢弃或清理
部署后，如果要重新开始，则可以删除目标服务总线命名空间，然后重复执行本文“[准备](#prepare)”和“[移动](#move)”部分所述的步骤。

要提交更改并完成命名空间的移动，请删除源服务总线命名空间。 确保处理完所有消息后，再删除该命名空间。 

使用 Azure 门户删除源或目标服务总线命名空间：

1. 在 Azure 门户顶部的搜索窗口中，键入“服务总线”，然后从搜索结果中选择“服务总线”。 此时会在列表中看到服务总线命名空间。
2. 选择要删除的目标命名空间，并从工具栏中选择“删除”。 

    ![删除命名空间 - 按钮](./media/move-across-regions/delete-namespace-button.png)
3. 在“删除资源”页上，验证所选资源，并键入“是”确认删除，然后选择“删除”。 

    另一种方法是删除包含该服务总线命名空间的资源组。 在“资源组”页上，选择工具栏上的“删除资源组”，然后确认删除。 

## <a name="next-steps"></a>后续步骤

在本教程中，我们将 Azure 服务总线命名空间从一个区域移到了另一个区域，并清理了源资源。  若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：

- [将资源移到新资源组或订阅中](../azure-resource-manager/management/move-resource-group-and-subscription.md)
