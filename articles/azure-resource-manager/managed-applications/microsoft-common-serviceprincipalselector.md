---
title: ServicePrincipalSelector UI 元素
description: 介绍 Azure 门户的 Microsoft.Common.ServicePrincipalSelector UI 元素。 提供一个用于选择应用程序的控件，并提供一个用于输入密码或证书指纹的文本框。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184444"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft.Common.ServicePrincipalSelector UI 元素

一个可让用户选择现有[服务主体](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)或注册新应用程序的控件。 选择“新建”时，需执行注册新应用程序的步骤。 选择现有应用程序时，此控件将提供一个文本框，用于输入密码或证书指纹。

## <a name="ui-samples"></a>UI 示例

可以使用默认应用程序、可以新建应用程序，还可以使用现有应用程序。

### <a name="use-default-application-or-create-new"></a>使用默认应用程序或新建应用程序

默认视图由 `defaultValue` 属性中的值确定，“服务主体类型”设置为“新建”。 如果 `principalId` 属性包含有效的全局唯一标识符 (GUID)，则此控件会搜索应用程序的 `objectId`。 如果用户没有从控件中进行选择，则会应用默认值。

若要注册新的应用程序，请选择“更改选择”，此时会显示“注册应用程序”对话框。 输入 **名称**、**支持的帐户类型**，然后选择“注册”按钮。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Microsoft.Common.ServicePrincipalSelector 初始视图。":::

注册新应用程序后，请使用“身份验证类型”输入密码或证书指纹。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Microsoft.Common.ServicePrincipalSelector 身份验证。":::

### <a name="use-existing-application"></a>使用现有应用程序

若要使用现有应用程序，请选择“选择现有”，然后选择“进行选择”。 使用“选择应用程序”对话框搜索应用程序的名称。 从结果中选择应用程序，然后单击“选择”按钮。 选择应用程序后，控件会显示“身份验证类型”，此时可以输入密码或证书指纹。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft.Common.ServicePrincipalSelector 选择现有应用程序。":::

## <a name="schema"></a>架构

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>备注

- 必需的属性如下所示：
  - `name`
  - `type`
  - `label`
  - `defaultValue`：指定默认的 `principalId` 和 `name`。

- 可选属性如下所示：
  - `toolTip`：将工具提示 `infoBalloon` 附加到每个标签。
  - `visible`：隐藏或显示控件。
  - `options`：指定是否应使证书指纹选项可用。
  - `constraints`：用于密码验证的正则表达式约束。

## <a name="example"></a>示例

下面是 `Microsoft.Common.ServicePrincipalSelector` 控件的一个示例。 `defaultValue` 属性将 `principalId` 设置为 `<default guid>`（默认应用程序标识符 GUID 的占位符）。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>示例输出

`appId` 是所选的或所创建的应用程序注册的 ID。 `objectId` 是为所选应用程序注册配置的服务主体的 objectId 数组。

未从控件中进行选择时，`newOrExisting` 属性值为“new”：

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

从控件中选择了“新建”或现有应用程序时，`newOrExisting` 属性值为“existing”：

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>后续步骤

- 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
- 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。