---
title: 如何管理设置？ - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 了解如何在自定义翻译中管理设置、创建工作区、共享工作区和管理订阅密钥。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 49801eddd748a88109bb7f6d075def03cd798754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895775"
---
# <a name="how-to-manage-settings"></a>如何管理设置

在自定义翻译器设置页中，可以共享工作区，修改翻译器订阅密钥以及删除工作区。

访问设置页：

1. 登录到[自定义翻译](https://portal.customtranslator.azure.ai/)门户。
2. 在自定义翻译门户中，单击侧栏中的齿轮图标。

    ![“设置”链接](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>关联翻译器订阅

需要具有与工作区关联的翻译器订阅密钥，才能训练或部署模型。

如果没有订阅，请执行以下步骤：

1. 订阅以创建翻译器资源。 请按照[如何注册翻译器](../translator-how-to-signup.md)，订阅和获取翻译器密钥。
2. 记下翻译器订阅的密钥。 密钥 1 或密钥 2 均可接受。
3. 导航回到自定义翻译门户。

## <a name="create-a-new-workspace"></a>创建新的工作区

1. 单击“自定义翻译器”边栏中的“+ 创建工作区”按钮。

    ![创建新工作区](media/how-to/create-new-workspace.png)

2. 在对话框中，输入新工作区的名称。
3. 单击“下一步”。
4. 选择订阅类型。
5. 选择订阅区域。 创建翻译器资源密钥时，区域必须与所选区域匹配。
6. 输入翻译器订阅的密钥，然后单击“保存”按钮。

    ![“创建新工作区”对话框](media/how-to/create-new-workspace-dialog.png)

>[!Note]
>自定义翻译器不支持为以下资源创建工作区： 在[已启用的 VNET](../../../api-management/api-management-using-with-vnet.md) 内创建的翻译器文本 API 资源（又称为 Azure 订阅密钥）。

### <a name="modify-existing-key"></a>修改现有密钥

1. 导航到工作区的“设置”页。
2. 单击“更改密钥”

    ![如何添加订阅密钥](media/how-to/how-to-add-subscription-key.png)

3. 在对话框中，输入翻译器订阅的密钥，然后单击“保存”按钮。

    ![“如何添加订阅密钥”对话框](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>管理工作区

工作区是用于编写和构建自定义翻译系统的工作区域。 工作区可以包含多个项目、模型和文档。

如果需要将你的不同工作内容与不同的人员共享，创建多个工作区可能会有所帮助。

## <a name="share-your-workspace"></a>共享工作区

在自定义翻译中，如果需要将你的不同工作内容与不同的人员共享，可与其他人共享你的工作区。

1. 导航到工作区的“设置”页。
2. 在“共享设置”部分中，单击“添加人员”按钮。

    ![共享工作区](media/how-to/share-workspace.png)

3. 在对话框中，输入要与其共享此工作区的电子邮件地址的逗号分隔列表。 请确保与相应人员登录到自定义翻译时所用的电子邮件地址共享。 然后，选择适当的共享权限级别，然后单击“保存”按钮。

    ![“共享工作区”对话框](media/how-to/share-workspace-dialog.png)

4. 如果工作区仍采用默认名称“我的工作区”，则共享该工作区之前必须更改名称。
5. 单击“保存”。

## <a name="sharing-permissions"></a>共享权限

1. **读取者：** 工作区中的读取者可以查看工作区中的所有信息。

2. **编辑者：** 工作区中的编辑者可以添加文档、训练模型，以及删除文档和项目。 他们可以添加订阅密钥，但无法修改工作区的共享对象、删除工作区，或更改工作区名称。

3. **所有者：** 所有者对工作区拥有完全权限。

## <a name="change-sharing-permission"></a>更改共享权限

共享工作区后，“共享设置”部分将显示与之共享此工作区的所有电子邮件地址。 如果你对工作区拥有所有者访问权限，则可以更改每个电子邮件地址的现有共享权限。

1. 在每个电子邮件地址的“共享设置”部分，有一个下拉菜单会显示当前权限级别。

2. 单击该下拉菜单，然后选择要分配给该电子邮件地址的新权限级别。

    ![共享权限设置](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>固定工作区

默认情况下，系统会固定创建的首个工作区。 每次登录时，固定的工作区都会在站点加载时显示。 如果已创建多个工作区，并希望在登录时将其中一个工作区设为默认工作区，则需要将其固定。

1. 在侧边栏中，单击要固定的工作区的名称。
2. 导航到工作区的“设置”页面。
3. 单击“固定”图标。

    ![固定工作区](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>后续步骤

- 了解[如何创建工作区和项目](workspace-and-project.md)