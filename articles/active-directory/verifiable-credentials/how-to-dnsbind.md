---
title: 将域链接到分散式身份识别 (DID)（预览版）— Azure Active Directory 可验证凭据
description: 了解如何进行 DNS 绑定？
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: ad5bb6e45479b4cccfa0b002427066439135e468
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588439"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>将域链接到分散式身份识别 (DID)

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文内容：
> [!div class="checklist"]
> * 为什么需要将 DID 链接到域？
> * 如何链接 DID 和域？
> * 域链接流程的工作原理是什么？
> * 验证/未验证域逻辑的工作原理是什么？

## <a name="prerequisites"></a>必备条件

若要将 DID 链接到域，需要完成以下操作。

- 完成[入门](get-started-verifiable-credentials.md)和后续[教程集](enable-your-tenant-verifiable-credentials.md)。

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>为什么需要将 DID 链接到域？

DID 作为未定位到现有系统的标识符启动。 DID 的作用在于可以由用户或组织拥有并控制。 若与组织交互的实体不知晓 DID 所属的用户，则 DID 不具备上述作用。

将 DID 链接到域允许任何实体对 DID 和域之间的关系进行加密验证，从而可解决初始信任问题。

## <a name="how-do-we-link-dids-and-domains"></a>如何链接 DID 和域？

我们通过实施由分散式身份识别基金会编写的开放标准（[已知 DID 配置）](https://identity.foundation/.well-known/resources/did-configuration/)在域和 DID 之间建立链接。 Azure Active Directory (Azure AD) 中的可验证凭据服务通过包含 DID 中提供的域信息并生成已知配置文件，从而帮助组织在 DID 和域之间建立链接：

1. Azure AD 使用组织设置过程中用户提供的域信息在 DID 文档中编写服务终结点。 所有与用户 DID 交互的参与方均可看到用户 DID 声明要关联的域。  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. Azure AD 中的可验证凭据服务将生成可托管在用户域的相容已知配置资源。 配置文件包含自行颁发的可验证凭据 credentialType“DomainLinkageCredential”，该凭据使用来自用户域的 DID 签署。 下面是存储在根域 URL 中的配置文档示例。


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

获取已知配置文件后，需要使用在启用可验证凭据 AAD 时指定的域名使文件可用。

* 在域的根目录托管已知 DID 配置文件。
* 不使用重定向。
* 使用 https 分发配置文件。

>[!IMPORTANT]
>Microsoft Authenticator 不接受重定向，指定的 URL 必须是最终的目标 URL。

## <a name="user-experience"></a>用户体验 

用户在操作颁发流程或提供可验证凭据时，应了解组织及其 DID 的相关信息。 若域即可验证凭据钱包，Microsoft Authenticator 将验证 DID 与 DID 文档中的域的关系，并根据结果为用户提供两种不同的体验。

## <a name="verified-domain"></a>已验证的域

在 Microsoft Authenticator 显示“已验证”图标之前，以下内容需要为 true：

* 签署自行颁发的开放 ID (SIOP) 请求的 DID 必须具有已链接域的服务终结点。
* 根域不使用重定向，而是使用 https。
* DID 文档中所列的域具有可解析的已知资源。
* 签署已知资源的可验证凭据使用的 DID 与签署 Microsoft Authenticator 用于启动流程的 SIOP 所使用 DID 相同。

若上述内容均为 true，Microsoft Authenticator 将显示已验证的页面，其中包括已验证的域。

![新权限请求](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>未验证的域

若上述内容并非全部为 true，Microsoft Authenticator 将向用户显示一个整页的警告，提示域未验证、用户操作存在风险以及继续操作需要谨慎。 我们选择此路由的原因如下：

* DID 未定位到域。
* 配置未正确设置。
* 与用户交互的 DID 为恶意 DID，且实际上无法证明用户拥有域（事实上用户没有域）。 由于存在最后这个问题，必须将 DID 链接到用户熟悉的域，以免传播警告消息。

![添加凭据屏幕中的未验证域警告](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>分发已知配置

1. 导航到可验证凭据中的设置页，然后选择“验证此域”

   ![在设置中验证此域](media/how-to-dnsbind/settings-verify.png) 

2. 下载下图所示 did-configuration.json 文件。

   ![下载已知配置](media/how-to-dnsbind/verify-download.png) 

3. 复制 JWT，打开 [jwt.ms](https://www.jwt.ms) 并验证域是否正确。

4. 复制 DID 并打开 [ION 网络资源管理器](https://identity.foundation/ion/explorer)，验证 DID 文档中是否包含相同的域。 

5. 在指定位置托管已知配置资源。 示例： `https://www.example.com/.well-known/did-configuration.json`

6. 尝试使用 Microsoft Authenticator 颁发或演示以进行验证。 请确保 Authenticator“警告不安全应用”中的设置已打开。

>[!NOTE]
>默认情况下，“警告不安全应用”处于打开状态。

恭喜，现在已引导 web 信任你的 DID！

## <a name="next-steps"></a>后续步骤

若加载期间输入的域信息不正确并要进行更改，需要[选择退出](how-to-opt-out.md)。目前，我们不支持更新 DID 文档。 选择退出并选择后退会创建全新 DID。
