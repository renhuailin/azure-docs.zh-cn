---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/04/2021
ms.author: trbye
ms.openlocfilehash: 2ffcb79bb2bb192566e7f6b7e0f933cacce84a77
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114593813"
---
若要完成意向识别快速入门，需要使用 LUIS 预览门户创建 LUIS 帐户和项目。 本快速入门要求在[意向识别可用的区域](/azure/cognitive-services/speech-service/regions#intent-recognition)的 LUIS 订阅。 无需语音服务订阅。

你需要做的第一件事是使用 LUIS 预览门户创建 LUIS 帐户和应用。 创建的 LUIS 应用会将预生成域用于主自动化，提供意向、实体和示例言语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点，可使用语音 SDK 进行调用。 

可以按照这些说明创建 LUIS 应用：

* <a href="/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">快速入门：构建预生成的域应用 </a>

完成后，需要以下四项信息：

* 在打开“语音启动”的情况下重新发布
* 你的 LUIS **主密钥**
* 你的 LUIS **位置**
* 你的 LUIS **应用 ID**

可以在 [LUIS 预览门户](https://preview.luis.ai/)中从以下位置找到此信息：

1. 在LUIS 预览门户中，选择你的应用，然后选择“发布”按钮。

2. 选择“生产”槽，如果使用的是 `en-US`，请选择“更改设置”，然后将“语音启动”选项切换到“开”位置   。 然后选择“发布”按钮。

    > [!IMPORTANT]
    > 强烈建议使用“语音启动”，因为它会提高语音识别的准确性。

    > [!div class="mx-imgBorder"]
    > ![将 LUIS 发布到终结点](../../../media/luis/publish-app-popup.png)

3. 在 LUIS 预览版门户中，选择“管理”，然后选择“Azure 资源”。 在此页面上，可以找到 LUIS 预测资源的 LUIS 密钥和位置（有时也称为“区域”）。

   > [!div class="mx-imgBorder"]
   > ![LUIS 密钥和位置](../../../media/luis/luis-key-region.png)

4. 获取密钥和位置后，需要应用 ID。 选择“设置”。 此页面上提供应用 ID。

   > [!div class="mx-imgBorder"]
   > ![LUIS 应用 ID](../../../media/luis/luis-app-id.png)