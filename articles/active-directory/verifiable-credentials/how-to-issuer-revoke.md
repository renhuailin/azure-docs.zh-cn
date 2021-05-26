---
title: 颁发者如何撤销可验证凭据 — Azure Active Directory 可验证凭据
description: 了解如何撤消已颁发的可验证凭据
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 86a1683751b76b0b6e08b71fdeccaae47d88ffc3
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466122"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>撤消之前颁发的可验证凭据（预览版）

使用可验证凭据 (VC) 时，不仅要颁发凭据，还要撤销凭据。 本文详细介绍了 VC 规范的“状态”属性部分，同时进一步讲解撤销可验证凭据的方法和原因以及相关数据和隐私问题。

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="status-property-in-verifiable-credentials-specification"></a>可验证凭据规范中的状态属性

了解撤销可验证凭据的影响之前，应知晓“状态检查”的定义及其工作原理。

[W3C 可验证凭据规范](https://www.w3.org/TR/vc-data-model/)引用第[4.9](https://www.w3.org/TR/vc-data-model/#status)节中的“状态”属性：

“本规范定义了以下‘credentialStatus’属性，可用来查询有关可验证凭据当前状态的信息，例如凭据是否已被挂起或撤销。”

但是，W3C 规范未定义执行“状态检查”的格式。

“本规范不定义状态架构的数据模型、格式和协议。 要检查可验证凭据状态，可通过可验证凭据扩展注册表 [VC-EXTENSION-REGISTRY] 进行，其中包含可用状态方案。”

>[!NOTE]
>目前，Microsoft 的状态检查实现是专有的，但我们正在积极与 DID 社区合作，以形成统一的标准。

## <a name="how-does-the-status-property-work"></a>“状态”属性的工作原理是什么？

在 Microsoft 颁发的每个可验证凭据中，都有一个名为 credentialStatus 的属性。 该属性使用 Microsoft 代你管理的状态 API 进行填充。 状态 API 示例如下：

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

开源可验证凭据 SDK 负责调用状态 API 并提供必要数据。

调用 API 并提供正确的信息后，API 将返回“True”或“False”。 “True”表示可验证凭据仍对颁发者有效，“False”表示颁发者已主动撤销可验证凭据。

## <a name="why-you-may-want-to-revoke-a-vc"></a>撤销可验证凭据的原因是什么？

客户会因为不同的原因撤销可验证凭据，一些常见原因如下。 

- 学生 ID：学生已不是大学里的在读学生。
- 员工 ID：员工已不是单位里的在职员工。
- 州驾照：司机已不在该州居住。

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>如何设置可撤消的可验证凭据

Microsoft 默认不会存储任何可验证凭据数据。 因此，我们没有任何可参考的数据来撤消特定的可验证凭据 ID。 颁发者需要在可验证凭据属性中指定一个特定字段，以便 Microsoft 编制索引并随后进行加盐和哈希。

>[!NOTE]
>哈希是一种单向加密运算，可将名为 ```preimage``` 的输入转换为具有固定长度的名为哈希的输出。 目前无法通过计算来撤销哈希运算。

可告知 Microsoft 要为可验证凭据的哪个属性编制索引。 编制索引的意义在于：可以使用索引值搜索要撤销的可验证凭据。

示例：Alice 是 Woodgrove 的一名员工。 Alice 离开 Woodgrove 去了 Contoso 工作。 Woodgrove 的 IT 管理员 Jane 在可验证凭据撤销搜索查询中搜索 Alice 的电子邮件。 在此示例中，Jane 为 Woodgrove 已验证员工凭据的电子邮件字段编制索引。 

请参阅下方示例，了解如何将规则文件修改为包含索引。

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>仅可在规则文件中为一个属性编制索引。  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>如何撤销可验证凭据

设置索引声明并向用户颁发可验证凭据后，即可了解如何在 VC 边栏选项卡中撤消可验证的凭据。

1. 导航至 Azure Active Directory 中的可验证凭据边栏选项卡。
1. 选择之前设置索引声明并向用户颁发可验证凭据的可验证凭据。 =
1. 在左侧菜单中，选择“撤消凭据”
   ![Revoke a credential](media/how-to-issuer-revoke/settings-revoke.png) 
1. 搜索要撤消的用户的索引属性。 

   ![查找要撤销的凭据](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >我们仅存储来自可验证凭据的索引声明哈希，因此只有完全匹配才会填充搜索结果。 我们采用 IT 管理员搜索的输入，并使用相同的哈希算法来查看数据库中是否有哈希匹配。
    
1. 找到匹配项后，选择要撤销的凭据右侧的“撤销”选项。

   ![出现一条警告消息，告知撤销之后用户仍具有凭据](media/how-to-issuer-revoke/warning.png) 

1. 成功撤销之后会显示状态更新，页面顶部会出现一个绿色横幅。 
   ![在设置中验证此域](media/how-to-issuer-revoke/revoke-successful.png) 

现在，每当信赖方调用以检查这个特定可验证凭据的状态时，Microsoft 的状态 API（代表该租户）将返回“false”响应。

## <a name="next-steps"></a>后续步骤

通过测试凭据测试相关功能，以便熟悉操作流程。 如需了解如何配置租户以颁发可验证凭据，请[观看我们的教程](get-started-verifiable-credentials.md)。