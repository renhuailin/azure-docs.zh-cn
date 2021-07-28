---
title: 导出应用程序预配配置并回退到已知良好状态，以便在 Azure Active Directory 中进行灾难恢复
description: 了解如何导出应用程序预配配置并回退到已知良好状态，以便在 Azure Active Directory 中进行灾难恢复。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 202450e66fe9f91257364b6840f1056435299f55
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109782930"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>操作说明：导出预配配置并回退到已知良好状态

本文将指导如何进行以下操作：

- 从 Azure 门户导出和导入预配配置
- 使用 Microsoft Graph API 导出和导入预配配置

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>从 Azure 门户导出和导入预配配置

### <a name="export-your-provisioning-configuration"></a>导出预配配置

若要导出配置：

1. 在 [Azure 门户](https://portal.azure.com/)的左侧导航面板中，选择“Azure Active Directory”。
1. 在“Azure Active Directory”窗格中，选择“企业应用程序”，然后选择应用程序。 
1. 在左侧导航窗格中，选择“预配”。 从“预配配置”页中，单击“属性映射”，然后单击“显示高级选项”，最后单击“检查架构”。   这会转到架构编辑器。
1. 在页面顶部的命令栏中单击下载来下载架构。

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>灾难恢复 - 回退到已知良好状态

导出和保存配置，配置可以回退到以前的版本。 建议导出预配配置并保存，以便在以后对属性映射或范围筛选器进行更改时使用。 只需打开上面步骤中下载的 JSON 文件，复制 JSON 文件的全部内容，在架构编辑器中替换 JSON 有效负载的全部内容，然后保存。 如果有活动的预配周期，该周期完成后，下一个周期将使用更新后的架构。 下一个周期还将是一个初始周期，它根据新配置重新评估每个用户和组。 回退到以前的配置时，请考虑以下事项：

- 将再次评估用户以确定其是否应在范围内。 如果范围筛选器更改使用户不再在范围内，则将禁用这些筛选器。 虽然在大多数情况下这是需要的行为，但有时可能需要防止这种情况，并可以使用[跳过超出范围删除](./skip-out-of-scope-deletions.md)功能。 
- 更改预配配置将重新启动该服务，并触发[初始周期](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental)。

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>使用 Microsoft Graph API 导出和导入预配配置

可以使用 Microsoft Graph API 和 Microsoft Graph 浏览器将用户预配属性映射和架构导出到 JSON 文件并将其导回 Azure AD。 还可以使用此处获取的步骤来创建预配配置的备份。

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>步骤 1：检索预配应用服务主体 ID（对象 ID）

1. 启动 [Azure 门户](https://portal.azure.com)，并导航到预配应用程序的属性部分。 例如，如果要导出 *Workday 到 AD 用户预配应用程序* 映射，请导航到该应用的属性部分。
1. 在预配应用的“属性”部分中，复制与“对象 ID”字段关联的 GUID 值。 此值也称为应用的 ServicePrincipalId，它将用于 Microsoft Graph 浏览器操作。

   ![Workday 应用服务主体 ID](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>步骤 2：登录到 Microsoft Graph 浏览器

1. 启动 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)
1. 单击“使用 Microsoft 登录”按钮，然后使用 Azure AD 全局管理员或应用管理员凭据登录。

    ![Microsoft Graph 登录](./media/export-import-provisioning-configuration/wd_export_02.png)

1. 成功登录后，你将在左侧窗格中看到用户帐户详细信息。

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>步骤 3：检索预配应用的预配作业 ID

在 Microsoft Graph 浏览器中，运行以下 GET 查询，将 [servicePrincipalId] 替换为从[步骤 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 中提取的 ServicePrincipalId。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

你将获得如下所示的响应。 复制响应中显示的“ID 属性”。 此值为 ProvisioningJobId 且将用于检索基础架构元数据。

   [![预配作业 ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>步骤 4：下载预配架构

在 Microsoft Graph 浏览器中，运行以下 GET 查询，将 [servicePrincipalId] 和 [ProvisioningJobId] 替换为在前述步骤中检索到的 ServicePrincipalId 和 ProvisioningJobId。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

复制响应中的 JSON 对象，并将其保存到要用于创建架构备份的文件中。

### <a name="step-5-import-the-provisioning-schema"></a>步骤 5：导入预配架构

> [!CAUTION]
> 仅当需要修改无法使用 Azure 门户更改的配置架构，或者需要通过带有有效且正在工作的架构的先前备份文件还原配置时，才执行此步骤。

在 Microsoft Graph 浏览器中，配置以下 PUT 查询，将 [servicePrincipalId] 和 [ProvisioningJobId] 替换为在前述步骤中检索到的 ServicePrincipalId 和 ProvisioningJobId。

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

在“请求正文”选项卡中，复制 JSON 架构文件的内容。

   [![请求正文](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

在“请求标头”选项卡中，添加值为“application/json”的 Content-Type 标头属性

   [![请求标头](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

选择“运行查询”以导入新架构。