---
title: 设置 AWS 与 Azure 成本管理的集成
description: 本文逐步讲解如何设置并配置 AWS 成本和使用情况报表与成本管理的集成。
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 6c8c03f93e811e622daa93515740e1cc25e65434
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706163"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>设置并配置 AWS 成本和使用情况报表集成

使用 Amazon Web Services (AWS) 成本和使用情况报表 (CUR) 集成，可以在成本管理中监视和控制 AWS 支出。 借助这种集成，可以在 Azure 门户中的单个位置监视和控制 Azure 与 AWS 的支出。 本文介绍如何设置并配置集成，以便可以使用成本管理功能来分析成本和查看预算。

成本管理使用你的 AWS 访问凭据来处理 S3 桶中存储的 AWS 成本和使用情况报表，以获取报表定义并下载报表 GZIP CSV 文件。

观看视频 [How to set up Connectors for AWS in Cost Management](https://www.youtube.com/watch?v=Jg5KC1cx5cA)（如何在成本管理中设置 AWS 的连接器），详细了解如何设置 AWS 报表集成。 若要观看其他视频，请访问[成本管理 YouTube 频道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>在 AWS 中创建成本和使用情况报表

AWS 建议使用成本和使用情况报表来收集和处理 AWS 成本。 成本管理跨云连接器支持在管理（整合）帐户级别配置的成本和使用情况报告。 有关详细信息，请参阅 [AWS 成本和使用情况报表](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)文档。

执行以下步骤，使用 AWS 中计费和成本管理控制台的“成本和使用情况报表”页来创建成本和使用情况报表： 

1. 登录到 AWS 管理控制台，并打开[计费和成本管理控制台](https://console.aws.amazon.com/billing)。
2. 在导航窗格中，选择“成本和使用情况报表”。 
3. 选择“创建报表”。 
4. 在“报表名称”中输入报表的名称。 
5. 在“其他报表详细信息”下，选择“包括资源 ID”。  
6. 对于“数据刷新设置”，请选择在最终完成帐单后，如果 AWS 向帐户应用退款、额度或支持费，你是否希望刷新 AWS 成本和使用情况报表。  刷新报表时，会将一份新报表上传到 Amazon S3。 建议将该项设置保持选中状态。
7. 选择“**下一页**”。
8. 对于“S3 桶”，请选择“配置”。  
9. 在“配置 S3 桶”对话框中输入桶名称以及要在其中创建新桶的区域，然后选择“下一步”。
10. 选择“我已确认此策略正确”，然后单击“保存”。  
11. （可选）对于“报表路径前缀”，请输入要附加在报表名称前面的报表路径前缀。
如果不指定前缀，则默认前缀是为报表指定的名称。 日期范围采用 `/report-name/date-range/` 格式。
12. 对于“时间单位”，请选择“小时”。  
13. 对于“报表版本控制”，请选择是希望报表的每个版本覆盖以前的版本，还是要添加额外的新报表。 
14. 对于“为以下对象启用数据集成”，无需做出任何选择。 
15. 对于“压缩”，请选择“GZIP”。  
16. 选择“**下一页**”。
17. 查看报表的设置后，选择“查看并完成”。 

    请记下报表名称， 因为在稍后的步骤中需要用到。

AWS 最长可能需要在 24 小时后才开始将报表传送到 Amazon S3 桶中。 传送开始后，AWS 将每日至少更新 AWS 成本和使用情况报表文件一次。 你可以继续配置 AWS 环境，而无需等待传送开始。

> [!NOTE]
> 当前不支持在成员（链接）帐户级别配置的成本和使用情况报告。

## <a name="create-a-role-and-policy-in-aws"></a>在 AWS 中创建角色和策略

成本管理每天会多次访问成本和使用情况报表所在的 S3 桶。 服务需要访问凭据来检查新数据。 请在 AWS 中创建角色和策略，使成本管理能够访问凭据。

若要在成本管理中启用对 AWS 帐户的基于角色的访问，请在 AWS 控制台中创建角色。 需要通过 AWS 控制台获取角色 ARN 和外部 ID。   稍后，将在成本管理中的“创建 AWS 连接器”页上使用这些信息。 

使用“创建新角色”向导：

1. 登录到 AWS 控制台并选择“服务”。 
2. 在服务列表中选择“IAM”。 
3. 依次选择“角色”、“创建角色”。  
4. 在下一页上，选择“另一个 AWS 帐户”。 
5. 在“帐户 ID”中输入 **432263259397**。 
6. 在“选项”中，选择“需要外部 ID (当第三方充当此角色时的最佳做法)”。  
7. 在“外部 ID”中，输入充当 AWS 角色与成本管理之间的共享密码的外部 ID。 在成本管理中的“新建连接器”页上，也要使用与此相同的外部 ID。  Microsoft 建议在输入外部 ID 时使用强密码策略。
    > [!NOTE]
    > 不要更改“需要 MFA”选项。  应将其保持清除状态。
8. 在完成时选择“下一步:  权限”。
9. 选择“创建策略”。  此时会打开新浏览器标签。 将在此位置创建策略。
10. 选择“选择服务”。 

配置成本和使用情况报表的权限：

1. 输入“成本和使用情况报表”。 
2. 选择“访问级别” > “读取” > “DescribeReportDefinitions”。    成本管理通过此步骤读取定义的 CUR 报表，并确定它们是否符合报表定义先决条件。
3. 选择“添加其他权限”。 

配置 S3 桶和对象的权限：

1. 选择“选择服务”。 
2. 输入 **S3**。
3. 选择“访问级别” > “列出” > “ListBucket”。    此操作获取 S3 桶中的对象列表。
4. 选择“访问级别” > “读取” > “GetObject”。    使用此操作可以下载计费文件。
5. 选择“资源”。 
6. 选择“桶 – 添加 ARN”。 
7. 在“桶名称”中，输入用于存储 CUR 文件的桶。 
8. 选择“对象 – 添加 ARN”。 
9. 在“桶名称”中，输入用于存储 CUR 文件的桶。 
10. 在“对象名称”中，选择“任何”。  
11. 选择“添加其他权限”。 

配置成本资源管理器的权限：

1. 选择“选择服务”。 
2. 输入“成本资源管理器服务”。 
3. 选择“所有成本资源管理器服务操作(ce:\*)”。  此操作将验证集合是否正确。
4. 选择“添加其他权限”。 

添加 AWS 组织的权限：

1. 输入“组织”。 
2. 选择“访问级别” > “列出” > “ListAccounts”。    此操作获取帐户的名称。
3. 在“查看策略”中，输入新策略的名称。  检查输入的信息是否正确，然后选择“创建策略”。 
4. 返回上一个标签页并刷新浏览器的网页。 在搜索栏中搜索新策略。
5. 在完成时选择“下一步:  查看”。
6. 输入新角色的名称。 检查输入的信息是否正确，然后选择“创建角色”。 

    请记下在上述步骤中创建角色时使用的角色 ARN 和外部 ID。 稍后在设置成本管理连接器时，需要用到这些信息。

策略 JSON 应类似于以下示例。 将 _bucketname_ 替换为 S3 桶的名称。

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
             "ce:*",
             "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>在 Azure 中为 AWS 设置新连接器

使用以下信息创建 AWS 连接器并开始监视 AWS 成本。

### <a name="prerequisites"></a>先决条件

- 请确保至少启用了一个管理组。 需要一个管理组才能将你的订阅链接到 AWS 服务。 有关创建管理组的详细信息，请参阅[在 Azure 中创建管理组](../../governance/management-groups/create-management-group-portal.md)。 
- 确保你是订阅的管理员。
- 按照在[在 AWS 中创建成本和使用情况报表](#create-a-cost-and-usage-report-in-aws)部分所述，完成新 AWS 连接器所需的设置。


### <a name="create-a-new-connector"></a>创建新连接器

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 如有必要，导航到“成本管理 + 计费”并选择一个计费范围。
1. 选择“成本分析”，然后选择“设置”。 
1. 选择“AWS 连接器”。
1. 选择“添加连接器”。
1. 在“创建连接器”页上的“显示名称”中，输入连接器的名称 。  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="用于创建 AWS 连接器的页面示例" :::
1. （可选）选择默认的管理组。 此组将存储所有已发现的链接帐户。 稍后可对其进行设置。
1. 在“计费”部分中，如果要确保连续操作，请将“自动续订”选择为“打开”  。 如果选择自动选项，则必须选择计费订阅。
1. 对于“角色 ARN”，请输入在 AWS 中设置角色时使用的值。 
1. 对于“外部 ID”，请输入在 AWS 中设置角色时使用的值。 
1. 对于“报表名称”，请输入在 AWS 中创建的名称。 
1. 依次选择“下一步”、“创建”。  

新的 AWS 范围、AWS 合并帐户、AWS 链接帐户及其成本数据可能需要在几小时后才会显示。

创建连接器后，我们建议为其分配访问控制。 将为用户分配对新发现的范围的权限：AWS 合并帐户和 AWS 链接帐户。 创建连接器的用户是该连接器、合并帐户和所有链接帐户的所有者。

在发现之后向用户分配连接器权限不会为现有的 AWS 范围分配权限， 而只为新的链接帐户分配权限。

## <a name="take-additional-steps"></a>其他步骤

- [设置管理组](../../governance/management-groups/overview.md#initial-setup-of-management-groups)（如果尚未这样做）。
- 检查新范围是否已添加到范围选取器。 选择“刷新”查看最新数据。 
- 在“云连接器”页上选择你的连接器，然后选择“转到计费帐户”，以将链接帐户分配到管理组。  

> [!NOTE]
> 管理组目前不受 Microsoft 客户协议 (MCA) 客户支持。 MCA 客户可以创建连接器并查看其 AWS 数据。 但是，MCA 客户无法在管理组下同时查看其 Azure 成本和 AWS 成本。

## <a name="manage-aws-connectors"></a>管理 AWS 连接器

在“AWS 连接器”页上选择某个连接器时，可以：

- 选择“转到计费帐户”来查看 AWS 合并帐户的信息。 
- 选择“访问控制”来管理连接器的角色分配。 
- 选择“编辑”来更新连接器。  无法更改 AWS 帐号，因为它显示在角色 ARN 中。 但可以创建新的连接器。
- 选择“验证”来重新运行验证测试，以确保成本管理能够使用连接器设置收集数据。 

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="AWS 连接器详细信息示例" :::

## <a name="set-up-azure-management-groups"></a>设置 Azure 管理组

将 Azure 订阅和 AWS 链接帐户放在同一个管理组中，以便可以在单个位置查看跨云提供商的信息。 如果尚未配置包含管理组的 Azure 环境，请参阅[管理组的初始设置](../../governance/management-groups/overview.md#initial-setup-of-management-groups)。

若要区分成本，可以创建一个只用于保存 AWS 链接帐户的管理组。

## <a name="set-up-an-aws-consolidated-account"></a>设置 AWS 合并帐户

AWS 合并帐户可以合并多个 AWS 帐户的计费和付款。 它还充当 AWS 链接帐户。 可以使用 AWS 连接器页上的链接查看 AWS 合并帐户的详细信息。 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="AWS 合并帐户的示例详细信息" :::

在该页中，可以：

- 选择“更新”，以批量更新 AWS 链接帐户与管理组的关联。 
- 选择“访问控制”，以设置范围的角色分配。 

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS 合并帐户的权限

默认情况下，AWS 合并帐户的权限是在创建帐户时根据 AWS 连接器权限设置的。 连接器创建者即为所有者。

可以使用 AWS 合并帐户的“访问级别”页管理访问级别。  但是，AWS 链接帐户不会继承 AWS 合并帐户的权限。

## <a name="set-up-an-aws-linked-account"></a>设置 AWS 链接帐户

AWS 链接帐户是创建和管理 AWS 资源的位置。 链接帐户还充当安全边界。

在此页中，可以：

- 选择“更新”，以更新 AWS 链接帐户与管理组的关联。 
- 选择“访问控制”，以设置范围的角色分配。 

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="AWS 链接帐户页的示例" :::

### <a name="permissions-for-an-aws-linked-account"></a>AWS 链接帐户的权限

默认情况下，AWS 链接帐户的权限是在创建时根据 AWS 连接器权限设置的。 连接器创建者即为所有者。 可以使用 AWS 链接帐户的“访问级别”页管理访问级别。  AWS 链接帐户不会从 AWS 合并帐户继承权限。

AWS 链接帐户始终从其所属的管理组继承权限。

## <a name="next-steps"></a>后续步骤

- 设置并配置 AWS 成本和使用情况报表集成后，请继续了解如何[管理 AWS 成本和使用情况](aws-integration-manage.md)。
- 如果你不熟悉成本分析，请参阅[使用成本分析浏览和分析成本](quick-acm-cost-analysis.md)快速入门。
- 如果你不熟悉 Azure 中的预算，请参阅[创建和管理 Azure 预算](tutorial-acm-create-budgets.md)。
