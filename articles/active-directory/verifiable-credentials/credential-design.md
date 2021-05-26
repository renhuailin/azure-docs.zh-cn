---
title: 如何自定义 Azure Active Directory 可验证凭据（预览）
description: 本文介绍如何创建你自己的自定义可验证凭据
services: active-directory
author: barclayn
manager: davba
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 0ecc7c2407798bed5b72211d3bdba114d3200d4c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466331"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>如何自定义可验证凭据（预览）

可验证凭据是由两个组件构成，即规则文件和显示文件。 规则文件确定用户在接收可验证凭据之前需要提供的内容。 显示文件用于控制凭据的品牌以及声明的样式。 在本指南中，我们将会说明如何修改这两个文件，以满足贵机构的需求。 

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="rules-file-requirements-from-the-user"></a>规则文件：用户的需求

规则文件是一个简单的 JSON 文件，用于描述可验证凭据的重要属性。 具体而言，它描述如何使用声明来填充可验证凭据。

目前，共有三种可以在规则文件中配置的输入类型。 可验证凭据颁发服务使用这些类型将声明插入到可验证凭据中，并以你的 DID 证明该信息。 下面是这三种类型及其说明。

- ID 令牌
- 通过可验证的呈交提供的可验证凭据。
- 自证声明

ID 令牌：样本应用和教程使用 ID 令牌。 如果配置了此选项，你需要提供 OpenID Connect 配置 URI，并包括应包括在 VC 中的声明。 系统会提示用户在 Authenticator 应用上“登录”以满足此要求，并从他们的帐户添加关联的声明。 

可验证凭据：颁发流程的最终结果是生成可验证凭据，但你也可以要求用户呈交可验证凭据，以便颁发凭据。 规则文件可以从呈交的可验证凭据获取特定的声明，并将这些声明包括在组织中新颁发的可验证凭据中。 

自证声明：选择此选项时，用户能够直接将类型信息输入到 Authenticator 中。 目前，字符串是唯一受支持的自证声明输入。 

![可验证凭据卡的详细视图](media/credential-design/issuance-doc.png) 

静态声明：此外，还可以在规则文件中声明静态声明，但此输入并非来自用户。 颁发者在规则文件中定义静态声明，该声明与可验证凭据中的任何其他声明类似。 只需在 vc.type 后面添加 credentialSubject，并声明属性和该声明。 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>输入类型：ID 令牌

若要获取 ID 令牌作为输入，规则文件需要配置 OIDC 兼容标识系统的已知终结点。 在该系统中，需要使用来自[颁发者服务通信示例](issuer-openid.md)的正确信息来注册应用程序。 此外，需在规则文件中指定 client_id，并且必须在 scope 参数中填写正确的作用域。 例如，如果要在 ID 令牌中返回电子邮件声明，则 Azure Active Directory 需要电子邮件作用域。
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| 属性 | 说明 |
| -------- | ----------- |
| `attestations.idTokens` | 支持的 OpenID Connect 标识提供者数组，用于获取用户信息。 |
| `...mapping` | 这是一个对象，用于描述如何将每个 ID 令牌中的声明映射到生成的可验证凭据中的属性。 |
| `...mapping.{attribute-name}` | 应在生成的可验证凭据中填充的属性。 |
| `...mapping.{attribute-name}.claim` | ID 令牌中的声明，该声明的值应当用于填充该属性。 |
| `...configuration` | 标识提供者的配置文档位置。 此 URL 必须遵循[标识提供者元数据的 OpenID Connect 标准](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata)。 配置文档中必须包含 `issuer`、`authorization_endpoint`、`token_endpoint` 和 `jwks_uri` 字段。 |
| `...client_id` | 在客户端注册过程中获取的客户端 ID。 |
| `...scope` | 这是以空格分隔的作用域列表，在这些作用域内，IDP 必须能够在 ID 令牌中返回正确的声明。 |
| `...redirect_uri` | 必须始终使用值 `vcclient://openid/`。 |
| `validityInterval` | 时间长度（以秒为单位），表示可验证凭据的生存期。 这段时间过后，该可验证凭据便不再有效。 如果省略此值，则意味着每个可验证凭据在明确撤销之前都会保持有效。 |
| `vc.type` | 这是一个字符串数组，指出可验证凭据所满足的架构。 有关详细信息，请参阅下一节。 |

### <a name="vctype-choose-credential-types"></a>vc.type：选择凭据类型 

所有的可验证凭据都必须在其规则文件中声明其“类型”。 凭据类型可将你的可验证凭据与其他组织所颁发的凭据区分开，并可确保颁发者与验证者之间的互操作性。 若要指出凭据类型，你必须提供该凭据所满足的一种或多种凭据类型。 每一种类型都由唯一的字符串表示 - 通常使用 URI 来确保全局唯一性。 该 URI 不必可寻址；它会被视为字符串。 

例如，康托索大学所颁发的文凭凭据可能会声明以下类型：

| 类型 | 用途 |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | 声明康托索大学所颁发的文凭中包含由 schema.org 的 `EducationaCredential` 对象定义的属性。 |
| `https://schemas.ed.gov/universityDiploma2020` | 声明康托索大学所颁发的文凭中包含由美国教育部定义的属性。 |
| `https://schemas.contoso.edu/diploma2020` | 声明康托索大学所颁发的文凭中包含由康托索大学定义的属性。 |

通过声明全部这三种类型，康托索大学的文凭可用来满足验证者的不同请求。 银行可以请求用户提供一组 `EducationCredential`，文凭可用来满足该请求。 但是，康托索大学校友会可以请求提供 `https://schemas.contoso.edu/diploma2020` 类型的凭据，而文凭也可以满足该请求。

为确保凭据的互操作性，建议你与相关组织密切合作，以定义要在你所处的行业中使用的凭据类型、架构和 URI。 许多行业主体都会提供有关官方文档结构的指导，这些文档可改变用途，用来定义可验证凭据的内容。 你还应当与凭据验证者密切合作，了解其如何请求和使用你的可验证凭据。

## <a name="input-type-verifiable-credential"></a>输入类型：可验证凭据

>[!NOTE]
>请求提供可验证凭据的规则文件不使用呈交交换格式来请求凭据。 当颁发服务支持标准的凭据清单时，就会更新该凭据。 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| 属性 | 说明 |
| -------- | ----------- |
| `attestations.presentations` | 作为输入请求的可验证凭据的数组。 |
| `...mapping` | 这是一个对象，用于描述如何将呈交的每个可验证凭据中的声明映射到生成的可验证凭据中的属性。 |
| `...mapping.{attribute-name}` | 应在生成的可验证凭据中填充的属性。 |
| `...mapping.{attribute-name}.claim` | 可验证凭据中的声明，它的值应该用于填充该属性。 |
| `...mapping.{attribute-name}.indexed` | 对于每个要保存以待撤销的可验证凭据，只能启用一项。 有关详细信息，请参阅[有关如何撤销凭据的文章](how-to-issuer-revoke.md)。 |
| `credentialType` | 要请求用户呈交的可验证凭据的 credentialType。 |
| `contracts` | 可验证凭据服务门户中的协定 URI。 |
| `issuers.iss` | 所要求的用户可验证凭据的颁发者 DID。 |
| `validityInterval` | 时间长度（以秒为单位），表示可验证凭据的生存期。 这段时间过后，该可验证凭据便不再有效。 如果省略此值，则意味着每个可验证凭据在明确撤销之前都会保持有效。 |
| `vc.type` | 这是一个字符串数组，指出可验证凭据所满足的架构。 |


## <a name="input-type-self-attested-claims"></a>输入类型：自证声明

在颁发流程中，可以要求用户输入一些自证信息。 目前，唯一的输入类型为“字符串”。 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| 属性 | 说明 |
| -------- | ----------- |
| `attestations.selfIssued` | 需要用户提供输入的自行颁发声明数组。 |
| `...mapping` | 这是一个对象，用于描述如何将自行颁发声明映射到生成的可验证凭据中的属性。 |
| `...mapping.alias` | 应在生成的可验证凭据中填充的属性。 |
| `...mapping.alias.claim` | 可验证凭据中的声明，它的值应该用于填充该属性。 |
| `validityInterval` | 时间长度（以秒为单位），表示可验证凭据的生存期。 这段时间过后，该可验证凭据便不再有效。 如果省略此值，则意味着每个可验证凭据在明确撤销之前都会保持有效。 |
| `vc.type` | 这是一个字符串数组，指出可验证凭据所满足的架构。 |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>显示文件：Microsoft Authenticator 中的可验证凭据

可验证凭据提供一组有限的选项，这些选项可用来反映你的品牌。 本文提供有关如何自定义凭据的说明，以及有关设计凭据的最佳实践，这些凭据在颁发给用户后看起来很不错。

颁发给用户的可验证凭据在 Microsoft Authenticator 中显示为卡片。 作为管理员，你可以选择与贵机构的品牌相符的卡片颜色、图标和文本字符串。

![颁发文档](media/credential-design/detailed-view.png) 

卡片还包含可自定义的字段，这些字段可以让用户知道卡片的用途、其中包含的属性等。

## <a name="create-a-credential-display-file"></a>创建凭据显示文件

与规则文件非常相似，显示文件是一个简单的 JSON 文件，用于描述在 Microsoft Authenticator 应用中显示可验证凭据内容的方式。 

>[!NOTE]
> 目前，此显示模型仅由 Microsoft Authenticator 使用。

显示文件的结构如下。

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| 属性 | 说明 |
| -------- | ----------- |
| `locale` | 可验证凭据的语言。 保留供将来使用。 | 
| `card.title` | 向用户显示凭据类型。 建议的最大长度为 25 个字符。 | 
| `card.issuedBy` | 向用户显示颁发机构的名称。 建议的最大长度为 40 个字符。 |
| `card.backgroundColor` | 确定卡片的背景色（十六进制格式）。 对所有卡片应用精细的渐变。 |
| `card.textColor` | 确定卡片的文本颜色（十六进制格式）。 建议使用黑色或白色。 |
| `card.logo` | 要在卡片上显示的徽标。 提供的 URL 必须可公开访问。 建议的最大高度为 36 像素，最大宽度为 100 像素，而不考虑电话大小。 建议使用背景透明的 PNG。 | 
| `card.description` | 要随每张卡片一起显示的补充文本， 可以用于任何用途。 建议的最大长度为 100 个字符。 |
| `consent.title` | 颁发卡片时显示的补充文本， 用于提供有关颁发过程的详细信息。 建议长度为 100 个字符。 |
| `consent.instructions` | 颁发卡片时显示的补充文本， 用于提供有关颁发过程的详细信息。 建议长度为 100 个字符。 |
| `claims` | 允许你为每个凭据中包括的属性提供标签。 |
| `claims.{attribute}` | 指示该标签所应用于的凭据属性。 |
| `claims.{attribute}.type` | 指示属性类型。 目前，只支持“字符串”。 |
| `claims.{attribute}.label` | 应该用作属性标签的值，该值会显示在 Authenticator 中。 可能与规则文件中使用的标签不同。 建议的最大长度为 40 个字符。 |

>[!NOTE]
  >如果某一声明包括在规则文件中，但在显示文件中省略，则会有两种不同类型的体验。 在 iOS 上，该声明不会显示在上图中显示的详细信息部分中，而在 Android 上该声明则会显示。  

## <a name="next-steps"></a>后续步骤

现在，你对可验证凭据的设计有了更好的了解，并已了解如何创建自己的凭据来满足你的需要。

- [颁发者服务通信示例](issuer-openid.md)
