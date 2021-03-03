---
title: 如何设置专用终结点-QnA Maker
description: 了解 QnA Maker 托管中提供的专用终结点创建。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710543"
---
# <a name="private-endpoints"></a>专用终结点

Azure 专用终结点是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 现在，QnA Maker 提供了为 Azure 搜索服务创建专用终结点的支持。 此功能在 QnA Maker 管理的中提供。 

专用终结点由 [Azure 专用链接](../../private-link/private-link-overview.md)作为单独的服务提供。 有关成本的详细信息，请参阅 [定价页。](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>先决条件
> [!div class="checklist"]
> * 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。
> * 在 Azure 门户中创建 QnA Maker [托管资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅、QnA 资源名称。

## <a name="steps-to-enable-private-endpoint"></a>启用专用终结点的步骤
1. 将 *参与者* 角色分配给 Azure 搜索服务实例中 QnA Maker 托管服务。 此操作需要 *所有者* 访问订阅。 转到服务资源中的 "标识" 选项卡以获取标识。
![托管服务标识](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. 转到 "Azure 搜索服务 IAM" 选项卡，将以上标识添加为 *参与者* 。 ![托管服务 IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. 单击 " *添加角色分配*"，添加标识，然后单击 " *保存*"。
![托管角色分配](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. 现在，转到 Azure 搜索服务实例中的 " *网络* " 选项卡，并将终结点连接数据从 " *公共* " 切换到 " *专用*"。 此操作是一个长时间运行的过程，可能需要长达30分钟才能完成。 
![托管 Azure 搜索网络](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. 在 QnA Maker 托管服务 "的" *网络* "选项卡中，在" *允许访问*"下，选择" *所选网络和专用终结点* "选项，然后单击" *保存*"。 
![托管 QnA maker newtorking](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

这将在 QnA maker 服务和 Azure 认知搜索服务实例之间建立专用终结点连接。 可以在 Azure 搜索服务实例的 " *网络* " 选项卡上验证专用终结点连接。 整个操作完成后，就可以使用 QnA Maker 服务了。 
![托管网络服务](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>支持详细信息
 * 一旦你启用对 QnAMaker 服务的私有访问，我们不支持更改 Azure 搜索服务。 如果在启用了私有访问后通过 "配置" 选项卡更改了 Azure 搜索服务，则 QnAMaker 服务将无法使用。
 * 建立专用终结点连接后，如果将 Azure 搜索服务的网络切换到 "公共"，则将无法使用 QnAMaker 服务。 Azure 搜索服务网络必须是 "专用"，才能使专用终结点连接工作