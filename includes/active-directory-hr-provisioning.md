---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93134891"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>云 HR 应用程序到 Azure Active Directory 的用户预配

过去，IT 人员一直依靠手动方法来创建、更新和删除员工。 他们使用上传 CSV 文件或自定义脚本等方法来同步员工数据。 这些预配过程容易出错、不安全且难以管理。

为了管理员工、供应商或临时工作人员的标识生命周期，[Azure Active Directory (Azure AD) 用户预配服务](../articles/active-directory/app-provisioning/user-provisioning.md)提供了与基于云的人力资源 (HR) 应用程序的集成。 这些应用程序包括 Workday 或 SuccessFactors。

Azure AD 使用此集成来启用以下云 HR 应用程序（应用）工作流：

- **将用户预配到 Active Directory：** 将选定的用户集从云 HR 应用预配到一个或多个 Active Directory 域中。
- **将仅限云的用户预配到 Azure AD：** 在未使用 Active Directory 的情况下，将用户直接从云 HR 应用预配到 Azure AD。
- **写回云 HR 应用：** 将电子邮件地址和用户名属性从 Azure AD 写回云 HR 应用。


## <a name="enabled-hr-scenarios"></a>支持的 HR 场景

Azure AD 用户预配服务可实现以下基于 HR 的标识生命周期管理方案的自动化：

- **招聘新员工：** 将新员工添加到云 HR 应用后，会在 Active Directory 和 Azure AD 中自动创建用户帐户，并可以选择将电子邮件地址和用户名属性写回云 HR 应用。
- **员工属性和个人资料更新：** 在云 HR 应用中更新员工记录（如姓名、职称或上司）后，Active Directory 和 Azure AD 中会自动更新相应员工的用户帐户。
- **员工离职：** 当员工在云 HR 中为离职状态时，Active Directory 和 Azure AD 中会自动禁用相应员工的用户帐户。
- **员工重新雇用：** 当员工在云 HR 中的状态为重新雇用时，其旧帐户可以自动重新激活或重新预配到 Active Directory 和 Azure AD。

## <a name="who-is-this-integration-best-suited-for"></a>谁最适合使用此集成？

云 HR 应用与 Azure AD 用户预配的集成非常适合满足以下条件的组织：

- 想要一个基于云的预构建解决方案，以进行云 HR 用户预配。
- 要求直接将用户从云 HR 应用预配到 Active Directory 或 Azure AD。
- 要求使用从云 HR 应用获取的数据预配用户。
- 要求仅基于云 HR 应用中检测到的更改信息，将加入、移动和离开的用户同步到一个或多个 Active Directory 林、域和 OU。
- 使用 Office 365 发送电子邮件。


### <a name="key-benefits"></a>主要优点

HR 驱动的 IT 预配的这一功能提供了以下重要的业务优势：

- **提高工作效率：** 现在你可以自动分配用户帐户和 Office 365 许可证，并提供对密钥组的访问权限。 自动分配使新员工可以立即访问其作业工具并提高工作效率。
- **管理风险：** 可以使用从云 HR 应用流入的数据，根据员工状态或组成员身份自动进行更改，从而提高安全性。 自动化更改可确保在用户转岗或从组织离职时，用户标识以及用户对关键应用的访问权限会自动更新。
- **解决合规性和管理问题：** Azure AD 支持由源系统和目标系统的应用执行的用户预配请求的原生审核日志。 通过审核，可以在单个屏幕上跟踪谁有权访问该应用。
- **管理成本：** 自动预配可以避免与手动预配相关的低效和人为错误，从而降低成本。 不再需要像使用旧版和过时的平台一样需要随时构建自定义开发的用户预配解决方案。
