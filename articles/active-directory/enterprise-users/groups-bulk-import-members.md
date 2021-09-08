---
title: 批量上传以添加或创建组成员 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 管理中心批量添加组成员。
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 09/02/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f3f083f2cae8b1cfb33c20c09395a2d8fd4a6b2
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437339"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>在 Azure Active Directory 中批量添加组成员

在 Azure Active Directory (Azure AD) 门户中，可使用逗号分隔值 (CSV) 文件批量导入组成员，从而将大量成员添加到组中。

## <a name="understand-the-csv-template"></a>了解 CSV 模板

要成功地批量添加 Azure AD 组成员，请下载并填写批量上传 CSV 模板。 CSV 模板可能如下例所示：

![用于上传和调用的电子表格，说明了每一行和每一列的用途和值](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>CSV 模板结构

下载的 CSV 模板中的行如下所示：

- **版本号**：包含版本号的第一行必须包含在上传的 CSV 中。
- 列标题：列标题的格式为：&lt;项名称&gt; [PropertyName] &lt;必需或空白&gt;。 例如，`Member object ID or user principal name [memberObjectIdOrUpn] Required`。 模板的一些较旧版本可能略有不同。 对于组成员身份更改，你可选择要使用的标识符：成员对象 ID 还是用户主体名称。
- **示例行**：我们已经在模板中包含了一行示例，展示了每个列的可接受值。 必须删除示例行并将其替换为你自己的项。

### <a name="additional-guidance"></a>其他指南

- 不得删除或修改上传模板的前两行，否则无法处理上传。
- 所需的列会先列出。
- 建议不要将新列添加到模板。 所添加的任何其他列都会被忽略，不进行处理。
- 建议尽可能频繁地下载 CSV 模板的最新版本。
- 添加至少两个用户的 UPN 或对象 ID 以成功上传文件。

## <a name="to-bulk-import-group-members"></a>批量导入组成员

1. 使用组织中的用户管理员帐户登录到 [Azure 门户](https://portal.azure.com)。 组所有者还可批量导入其拥有的组的成员。
1. 在 Azure AD 中，选择“组” > “所有组” 。
1. 打开要向其添加成员的组，然后选择“成员”。
1. 在“成员”页面上，选择“导入成员” 。
1. 在“批量导入组成员”页面上，选择“下载”以获取具有所需组成员属性的 CSV 文件模板 。

    ![“导入成员”命令位于组的配置文件页上](./media/groups-bulk-import-members/import-panel.png)

1. 打开 CSV 文件，并为要导入到组中的每个组成员添加一行（所需值为“成员对象 ID”或“用户主体名称”） 。 然后保存文件。

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="CSV 文件包含要导入的成员的姓名和 ID":::

1. 在“批量导入组成员”页面上的“上传 csv 文件”下，浏览到该文件 。 选择 CSV 文件后立即开始对它的验证。
1. 验证文件内容后，批量导入页将显示“已成功上传文件”。 如果有错误，必须修正错误，然后才能提交作业。
1. 当文件通过验证时，选择“提交”，开始将组成员导入到组的 Azure 批量操作。
1. 导入操作完成后，会显示一条通知，指出批量操作成功。

## <a name="check-import-status"></a>查看导入状态

可在“批量操作结果”页面中查看所有挂起的批量请求的状态。

[![在“批量操作结果”页面中查看状态。](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

要详细了解批量操作中每个行项，请选择“成功数”、“失败数”或“请求总数”列下的值  。 如果失败，则会列出失败原因。

## <a name="bulk-import-service-limits"></a>“批量导入”服务限制

导入组成员列表的每个批量活动最多可运行一小时。 这样可导入至多包含 40000 名成员的列表。

## <a name="next-steps"></a>后续步骤

- [批量删除组成员](groups-bulk-remove-members.md)
- [下载组成员](groups-bulk-download-members.md)
- [下载所有组的列表](groups-bulk-download.md)
