---
title: 如何设置专用终结点 - QnA Maker
description: 了解可在 QnA Maker managed 中实现的专用终结点创建。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 00a55b852158abee01692d3c4ccbf1209c937cf4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379504"
---
# <a name="private-endpoints"></a>专用终结点

Azure 专用终结点是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 现在，自定义问答支持创建 Azure 搜索服务专用终结点。

专用终结点由 [Azure 专用链接](../../private-link/private-link-overview.md)作为单独的服务提供。 有关费用的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/private-link/)。 

## <a name="prerequisites"></a>先决条件
> [!div class="checklist"]
> * 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。
> * 在 Azure 门户中创建的[文本分析资源](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)（具有自定义问答功能）。 请记住你在创建资源时选择的 Azure Active Directory ID、订阅和文本分析资源名称。

## <a name="steps-to-enable-private-endpoint"></a>启用专用终结点的步骤
1. 将“参与者”角色分配给 Azure 搜索服务实例中的文本分析服务。 此操作需要对订阅的所有者访问权限。 转到服务资源中的“标识”选项卡以获取标识。

> [!div class="mx-imgBorder"]
> ![文本分析标识](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoints-identity.png)

2. 通过转到 Azure 搜索服务 IAM 选项卡，将上述标识添加为参与者。

![托管服务 IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. 单击“添加角色分配”，添加标识，然后单击“保存” 。

![托管角色分配](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. 现在，转到 Azure 搜索服务实例中的“网络”选项卡，并将终结点连接数据从“公共”切换为“专用”  。 此操作是一个长时间运行的过程，可能需要长达 30 分钟的时间才能完成。 

![托管 Azure 搜索网络](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. 转到文本分析服务的“网络”选项卡，在“允许从以下位置访问”下选择“所选网络和专用终结点”选项，然后单击“保存”   。
 
> [!div class="mx-imgBorder"]
> ![文本分析网络](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-custom-qna.png)

这将在文本分析服务和 Azure 认知搜索服务实例之间建立专用终结点连接。 你可以在 Azure 认知搜索服务实例的“网络”选项卡上验证该专用终结点连接。 完成整个操作后，即可使用文本分析服务。 

![托管网络服务](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>支持详细信息
 * 当你启用对文本分析服务的专用访问权限后，我们便不支持对 Azure 认知搜索服务进行更改。 如果你在启用专用访问权限后通过“功能”选项卡更改 Azure 认知搜索服务，文本分析服务会变得不可用。
 * 建立专用终结点连接后，如果将 Azure 认知搜索服务网络切换到“公共”，则将无法使用文本分析服务。 Azure 搜索服务网络必须为“专用”，专用终结点连接才能正常工作
