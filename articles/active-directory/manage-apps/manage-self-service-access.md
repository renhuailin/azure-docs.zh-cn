---
title: 如何配置自助应用程序分配 | Microsoft Docs
description: 启用自助服务应用程序访问以允许用户查找自己的应用程序
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: phsignor
ms.openlocfilehash: 98848b4ee117a0c2e34e21b30a920fe22c8e02be
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749119"
---
# <a name="how-to-configure-self-service-application-assignment"></a>如何配置自助应用程序分配

在你的用户能够从“我的应用”中自行发现应用程序之前，你需要先对你希望其允许用户自行发现并请求访问权限的任何应用程序启用“自助应用程序访问”。 此功能可用于从 [Azure AD 库](./add-application-portal.md)、[Azure AD 应用程序代理](../app-proxy/application-proxy.md)或通过[用户或管理员同意](../develop/application-consent-experience.md)添加的应用程序。

对于 IT 组，此功能是节省时间和金钱的绝佳方式，我们强烈建议将其作为 Azure Active Directory 的新式应用程序部署的一部分。

使用此功能，可以：

- 允许用户从[我的应用](https://myapps.microsoft.com/)中自行发现应用程序，而无需求助 IT 组。

- 将这些用户添加到预配置组，以便查看请求访问的用户、删除访问权限以及管理分配给用户的角色。

- （可选）允许业务审批人批准应用程序访问请求，从而为 IT 组省去麻烦。

- （可选）最多配置 10 个可以批准访问此应用程序的人员。

- （可选）允许业务审批者直接从其[我的应用](https://myapps.microsoft.com/)设置用户可用于登录应用程序的密码。

- （可选）自动将自助服务分配的用户直接分配到应用程序角色。

> [!NOTE]
> 必须具备 Azure Active Directory Premium（P1 或 P2）许可证，用户才能请求加入自助应用，所有者才能批准或拒绝请求。 如果没有 Azure Active Directory Premium 许可证，用户将无法添加自助应用。

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>启用自助服务应用程序访问以允许用户查找自己的应用程序

自助应用程序访问是帮助用户自行发现应用程序的绝佳方式，可选择性地允许业务组批准对这些应用程序的访问。 对于密码单一登录应用程序，可允许业务组从自己的“我的应用”访问面板管理分配给这些用户的凭据。

若要启用应用程序的自助应用程序访问，请执行以下步骤：

1. 以全局管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“Azure Active Directory”。 在左侧导航菜单中，选择“企业应用程序”。

3. 从列表中选择应用。 如果看不到该应用程序，请在搜索框中键入其名称。 或者使用筛选控件选择应用程序类型、状态或可见性，然后选择“应用”。

4. 在左侧导航菜单中，选择“自助服务”。

5. 要对此应用程序启用自助应用程序访问，请将“允许用户请求对此应用程序的访问权限?”切换到“是”。

6. 在“应向哪个组添加分配的用户?”旁，单击“选择组”。 选择一个组，然后单击“选择”。 用户请求获得批准后，他们会被添加到此组。 查看此组的成员身份时，可以通过自助访问查看已被授予对应用程序的访问权限的用户。
  
    > [!NOTE]
    > 此设置不支持从本地同步的组。

7. 可选：如果希望在获得业务批准之后才允许用户访问，请将“需要获得批准才能授权访问此应用程序?”切换到“是”。

8. 可选：对于仅使用密码单一登录的应用程序，如果要允许业务审批者为已批准的用户指定发送到此应用程序的密码，请将“允许审批者设置此应用程序的用户密码?”切换到“是”。

9. 可选：若要指定有权批准对此应用程序的访问的业务审批者，请在“谁有权批准对此应用程序的访问?”旁单击“选择审批者”，最多可选择 10 个单独业务审批者。 然后单击“选择”。

    >[!NOTE]
    >不支持组。 最多可以选择 10 个单独的业务审批者。 如果指定多个审批者，则任何一个审批者都可以批准访问请求。

10. 可选：对于公开角色的应用程序，如果要向角色分配已被批准使用自助服务的用户，请在“在此应用程序中应向哪个角色分配用户?”旁单击“选择角色”，然后选择要向其分配这些用户的角色。 然后单击“选择”。

11. 单击窗格顶部的“保存”按钮以完成操作。

完成自助应用程序配置后，用户可以导航到[我的应用](https://myapps.microsoft.com/)，单击“添加自助应用”按钮以查找已启用自助访问的应用。 业务审批者还会在[我的应用](https://myapps.microsoft.com/)中看到通知。 可以启用电子邮件，在用户请求需要审批人批准的应用程序的访问权限时，向审批人发送电子邮件通知。

## <a name="next-steps"></a>后续步骤

[为自助组管理设置 Azure Active Directory](../enterprise-users/groups-self-service-management.md)
