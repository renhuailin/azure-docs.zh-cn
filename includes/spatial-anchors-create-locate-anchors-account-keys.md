---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993130"
---
## <a name="set-up-authentication"></a>设置身份验证

若要访问服务，需提供帐户密钥、访问令牌或 Azure Active Directory 身份验证令牌。 还可在[“身份验证概念”页](../articles/spatial-anchors/concepts/authentication.md)中了解此方面的详细信息。

### <a name="account-keys"></a>帐户密钥

帐户密钥是一种允许应用程序使用 Azure 空间定位点服务进行身份验证的凭据。 帐户密钥的预期用途是帮助你快速入门， 尤其是在应用程序与 Azure 空间定位点集成的开发阶段。 因此，可以这样使用帐户密钥：在开发过程中将它们嵌入客户端应用程序。 完成开发阶段以后，强烈建议迁移到一种属于生产级别、受访问令牌支持或者是 Azure Active Directory 用户身份验证的身份验证机制。 若要获取用于开发的帐户密钥，请访问 Azure 空间定位点帐户并导航到“密钥”选项卡。