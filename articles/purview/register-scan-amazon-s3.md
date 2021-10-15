---
title: 用于 Azure Purview 的 Amazon S3 多云扫描连接器
description: 本操作指南详细介绍了如何在 Azure Purview 中扫描 Amazon S3 Bucket。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.custom: references_regions
ms.openlocfilehash: b57e8f4725e1f6d97cd86406f95c26fbbf7b47e7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358600"
---
# <a name="amazon-s3-multi-cloud-scanning-connector-for-azure-purview"></a>用于 Azure Purview 的 Amazon S3 多云扫描连接器

用于 Azure Purview 的多云扫描连接器可跨云服务提供商（包括 Amazon Web Services 和 Azure 存储服务）浏览组织数据。

本文介绍如何使用 Azure Purview 扫描当前存储在 Amazon S3 标准 Bucket 中的非结构化数据，并发现数据中存在的敏感信息类型。 本操作指南还介绍了如何确定当前存储数据的 Amazon S3 存储桶，以便于保护信息并实现数据合规性。

对于此服务，请使用 Purview 为 Microsoft 帐户提供对 AWS（用于 Azure Purview 的多云扫描连接器将在其中运行）的安全访问。 用于 Azure Purview 的多云扫描连接器使用针对 Amazon S3 Bucket 的此访问权限读取数据，然后将扫描结果（其中仅包括元数据和分类）报告给 Azure。 请使用 Purview 分类和标记报告来分析和查看数据扫描结果。

> [!IMPORTANT]
> 用于 Azure Purview 的多云扫描连接器是 Azure Purview 的单独加载项。 用于 Azure Purview 的多云扫描连接器的条款和条件包含在你获得 Microsoft Azure 服务所签署的协议中。 有关详细信息，请参阅 https://azure.microsoft.com/support/legal/ 中的“Microsoft Azure 法律信息”。
>

## <a name="purview-scope-for-amazon-s3"></a>适用于 Amazon S3 的 Purview 范围

如需了解 Purview 限制，请参阅：

- [管理和增加 Azure Purview 资源的配额](how-to-manage-quotas.md)
- [Azure Purview 中支持的数据源和文件类型](sources-and-scans.md)
- [为 Purview 帐户使用专用终结点](catalog-private-link.md)

### <a name="storage-and-scanning-regions"></a>存储和扫描区域

Amazon S3 服务的 Purview 连接器当前仅在特定区域部署。 下表将存储数据的区域映射到 Azure Purview 将扫描的区域。

> [!IMPORTANT]
> 客户将需根据其存储桶所在的区域支付所有相关的数据传输费用。
>

| 存储区域 | 扫描区域 |
| ------------------------------- | ------------------------------------- |
| 美国东部（俄亥俄州）                  | 美国东部（俄亥俄州）                        |
| 美国东部（北 弗吉尼亚）           | 美国东部（北 弗吉尼亚）                       |
| 美国西部（北 加利福尼亚州）         | 美国西部（北 加利福尼亚州）                        |
| 美国西部（俄勒冈州）                | 美国西部（俄勒冈州）                      |
| 非洲（开普敦）              | 欧洲（法兰克福）                    |
| 亚太（香港特别行政区）        | 亚太（东京）                |
| 亚太（孟买）           | 亚太（新加坡）                |
| 亚太（大阪-本地）      | 亚太（东京）                 |
| 亚太（首尔）            | 亚太（东京）                 |
| 亚太（新加坡）        | 亚太（新加坡）                 |
| 亚太（悉尼）           | 亚太（悉尼）                  |
| 亚太（东京）            | 亚太（东京）                |
| 加拿大（中部）                | 美国东部（俄亥俄州）                        |
| 中国（北京）                 | 不支持                    |
| 中国（宁夏）                 | 不支持                   |
| 欧洲（法兰克福）              | 欧洲（法兰克福）                    |
| 欧洲（爱尔兰）                | 欧洲（爱尔兰）                   |
| 欧洲（伦敦）                 | 欧洲（伦敦）                 |
| 欧洲（米兰）                  | 欧洲（巴黎）                    |
| 欧洲（巴黎）                  | 欧洲（巴黎）                   |
| 欧洲（斯德哥尔摩）              | 欧洲（法兰克福）                    |
| 中东（巴林）           | 欧洲（法兰克福）                    |
| 南美洲（圣保罗）       | 美国东部（俄亥俄州）                        |
| | |

## <a name="prerequisites"></a>先决条件

请首先确保已满足以下先决条件，然后再添加 Amazon S3 存储桶作为 Purview 数据源并扫描 S3 数据。

> [!div class="checklist"]
> * 你需要是 Azure Purview 数据源管理员。
> * [创建 Purview 帐户](#create-a-purview-account)（如果没有）
> * [创建在 Purview 中使用的新 AWS 角色](#create-a-new-aws-role-for-purview)
> * [创建 Purview 凭据以用于扫描 AWS 存储桶](#create-a-purview-credential-for-your-aws-s3-scan)
> * [为已加密的 Amazon S3 桶配置扫描](#configure-scanning-for-encrypted-amazon-s3-buckets)（如果相关）
> * 添加存储桶作为 Purview 资源时，需要 [AWS ARN](#retrieve-your-new-role-arn)、[存储桶名称](#retrieve-your-amazon-s3-bucket-name)的值，有时还会需要 [AWS 帐户 ID](#locate-your-aws-account-id) 的值。

### <a name="create-a-purview-account"></a>创建 Purview 帐户

- 如果你已经有一个 Purview 帐户，可以继续设置获取 AWS S3 支持所需的配置。 首先[创建 Purview 凭据以用于扫描 AWS 存储桶](#create-a-purview-credential-for-your-aws-s3-scan)。

- 如果需要创建 Purview 帐户，请按照[创建 Azure Purview 帐户实例](create-catalog-portal.md)中的说明操作。 创建完帐户后，返回到此处完成配置，并开始使用用于 Amazon S3 的 Purview 连接器。

### <a name="create-a-new-aws-role-for-purview"></a>为 Purview 创建新的 AWS 角色

本过程介绍如何在 Purview 中查找 Azure 帐户 ID 和外部 ID 的值，创建 AWS 角色，然后输入角色 ARN 的值。

查找 Microsoft 帐户 ID 和外部 ID：

1. 在 Purview 中，转到“管理中心” > “安全性和访问” > “凭据”  。

1. 选择“新建”以创建新凭据。

    在右侧显示的“新建凭据”窗格中，从“身份验证方法”下拉列表中选择“角色 ARN”  。 
    
    然后将“Microsoft 帐户 ID”和“外部 ID”值复制到单独的文件中，或保留这两个 ID 以便将其粘贴到 AWS 中的相关字段 。 例如：

    [![查找 Microsoft 帐户 ID 和外部 ID 值。](./media/register-scan-amazon-s3/locate-account-id-external-id.png)](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


为 Purview 创建 AWS 角色：

1.  打开“Amazon Web Services”控制台，在“安全性、标识与合规性”下选择“IAM”。

1. 依次选择“角色”和“创建角色” 。

1. 选择“另一个 AWS 帐户”，然后输入以下值：

    |字段  |说明  |
    |---------|---------|
    |**帐户 ID**     |    输入你的 Microsoft 帐户 ID。 例如：`181328463391`     |
    |**外部 ID**     |   在“选项”下，选择“需要外部 ID...”，然后在指定字段中输入外部 ID。 <br>例如：`e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    例如：

    ![将 Microsoft 帐户 ID 添加到 AWS 帐户。](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. 在“创建角色”>“附加权限策略”区域中，筛选所显示的针对“S3”的权限。 选择“AmazonS3ReadOnlyAccess”，然后选择“下一步: 标签”。

    ![为新的 Amazon S3 扫描角色选择 ReadOnlyAccess 策略。](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > AmazonS3ReadOnlyAccess 策略提供扫描 S3 桶所需的最低权限，也可能包括其他权限。
    >
    >若要仅应用扫描桶所需的最低权限，请根据你是要扫描帐户中的单个桶还是所有桶，使用 [AWS 策略的最低权限](#minimum-permissions-for-your-aws-policy)中列出的权限创建新策略。 
    >
    >将新策略应用于角色而不是 AmazonS3ReadOnlyAccess。

1. 在“添加标签(可选)”区域中，可以选择为此新角色创建一个有意义的标签。 通过这些有用的标签，可以整理、跟踪和控制所创建的每个角色的访问权限。

    根据需要为标签输入新密钥和值。 完成此步骤后，或者根据需要跳过此步骤后，选择“下一步: 查看”，可查看角色详细信息并完成角色创建。

    ![添加一个有意义的标签，以便于整理、跟踪或控制新角色的访问权限。](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. 在“查看”区域中，执行以下操作：

    - 在“角色名称”字段中，为角色输入一个有意义的名称
    - 在“角色说明”框中，输入描述以标识该角色的用途（可选）
    - 在“策略”部分中，确认已将正确的策略 (AmazonS3ReadOnlyAccess) 附加到角色。

    然后选择“创建角色”以完成此过程。

    例如：

    ![在创建角色之前查看详细信息。](./media/register-scan-amazon-s3/review-role.png)


### <a name="create-a-purview-credential-for-your-aws-s3-scan"></a>创建用于 AWS S3 扫描的 Purview 凭据

此过程介绍了如何创建新的 Purview 凭据以在扫描 AWS 存储桶时使用。

> [!TIP]
> 如果你是直接从[为 Purview 创建新的 AWS 角色](#create-a-new-aws-role-for-purview)继续操作的，则可能已在 Purview 中打开了“新建凭据”窗格。
>
> 你也可以在[配置扫描](#create-a-scan-for-one-or-more-amazon-s3-buckets)过程中创建新凭据。 在这种情况下，请在“凭据”字段中选择“新建”。
>

1. 在 Purview 中，转到“管理中心”，然后在“安全性和访问”下选择“凭据”  。

1. 选择“新建”，然后在右侧显示的“新建凭据”窗格中，使用以下字段创建 Purview 凭据：

    |字段 |说明  |
    |---------|---------|
    |**名称**     |为此凭据输入一个有意义的名称。        |
    |**说明**     |为此凭据输入一段说明（可选），例如 `Used to scan the tutorial S3 buckets`         |
    |**身份验证方法**     |选择“角色 ARN”，因为你将使用角色 ARN 访问存储桶。         |
    |**角色 ARN**     | [创建 Amazon IAM 角色](#create-a-new-aws-role-for-purview)后，请在 AWS IAM 区域中导航到你的角色，复制“角色 ARN”值，并在此处输入该值。 例如：`arn:aws:iam::181328463391:role/S3Role`。 <br><br>有关详细信息，请参阅[检索新的角色 ARN](#retrieve-your-new-role-arn)。 |
    | | |
    
    [在 AWS 中创建角色 ARN](#create-a-new-aws-role-for-purview) 时，将使用 Microsoft 帐户 ID 和外部 ID 值 。

1. 完成上述操作后，选择“创建”以完成创建凭据的过程。

有关 Purview 凭据的详细信息，请参阅 [Azure Purview 中用于源身份验证的凭据](manage-credentials.md)。


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>为加密 Amazon S3 存储桶配置扫描

AWS 存储桶支持多种加密类型。 对于使用 AWS-KMS 加密的存储桶，需要完成特殊配置才能启用扫描。

> [!NOTE]
> 对于未使用加密、使用 AES-256 或 AWS S3 加密的存储桶，请跳过此部分，继续[检索 Amazon S3 存储桶名称](#retrieve-your-amazon-s3-bucket-name)。
>

**查看 Amazon S3 存储桶使用的加密类型：**

1. 在 AWS 中，导航到“存储” > “S3” ，然后从左侧菜单中选择“存储桶”。

    ![选择 Amazon S3“存储桶”选项卡。](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. 选择要查看的存储桶。 在相应存储桶的详细信息页上，选择“属性”选项卡，然后向下滚动到“默认加密”区域。

    - 如果所选存储桶配置了除 AWS-KMS 以外的任何加密设置，包括禁用了存储桶的默认加密，请跳过此过程的其余部分，继续[检索 Amazon S3 存储桶名称](#retrieve-your-amazon-s3-bucket-name)。

    - 如果所选存储桶配置了“AWS-KMS”加密，请按照下面的说明继续添加一个新策略，允许扫描使用自定义 AWS-KMS 加密的存储桶。

    例如：

    ![查看配置了 AWS-KMS 加密的 Amazon S3 存储桶](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**添加新策略以允许扫描使用自定义 AWS-KMS 加密的存储桶：**

1. 在 AWS 中，导航到“服务” >  “IAM” >  “策略”，然后选择“创建策略”。

1. 在“创建策略” > “可视化编辑器”选项卡上，使用以下值定义策略：

    |字段  |说明  |
    |---------|---------|
    |**服务**     |  输入“KMS”并选中它。       |
    |**操作**     | 在“访问级别”下，选择“写入”以展开“写入”部分。<br>展开后，仅选择“解密”选项。        |
    |**资源**     |选择特定资源，或选择“所有资源”。         |
    | | |

    完成后，选择“查看策略”，以继续操作。

    ![创建策略以扫描使用 AWS-KMS 加密的存储桶。](./media/register-scan-amazon-s3/create-policy-kms.png)

1. 在“查看策略”页上，为策略输入一个有意义的名称和一段描述（可选），然后选择“创建策略”。

    新创建的策略将添加到策略列表中。

1. 将新策略附加到所添加的扫描角色。

    1. 导航回“IAM” > “角色”页，选择在[上文](#create-a-new-aws-role-for-purview)添加的角色。

    1. 在“权限”选项卡上，选择“附加策略”。

        ![在角色的“权限”选项卡上，选择“附加策略”。](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. 在“附加权限”页上，搜索并选择在上文中创建的新策略。 选择“附加策略”，将策略附加到角色。

        “摘要”页将更新，其中包含附加到角色的新策略。

        ![查看更新的“摘要”页，其中包含附加到角色的新策略。](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>检索新的角色 ARN

你将需要记录 AWS 角色 ARN，并在[创建 Amazon S3 存储桶扫描](#create-a-scan-for-one-or-more-amazon-s3-buckets)时将其复制到 Purview。

**检索角色 ARN：**

1. 在 AWS“标识和访问管理(IAM)” > “角色”区域中，搜索并选择[为 Purview 创建](#create-a-purview-credential-for-your-aws-s3-scan)的新角色。

1. 在角色的“摘要”页上，选择“角色 ARN”值右侧的“复制到剪贴板”按钮。

    ![将角色 ARN 值复制到剪贴板。](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

在 Purview 中，可以编辑 AWS S3 的凭据，然后将检索到的角色粘贴到“角色 ARN”字段中。 有关详细信息，请参阅[创建一个或多个 Amazon S3 Bucket 的扫描](#create-a-scan-for-one-or-more-amazon-s3-buckets)。

### <a name="retrieve-your-amazon-s3-bucket-name"></a>检索 Amazon S3 存储桶名称

你需要有 Amazon S3 存储桶的名称，以便在 [创建 Amazon S3 存储桶扫描](#create-a-scan-for-one-or-more-amazon-s3-buckets) 时将该名称复制到 Purview

**检索存储桶名称：**

1. 在 AWS 中，导航到“存储” > “S3” ，然后从左侧菜单中选择“存储桶”。

    ![查看 Amazon S3“存储桶”选项卡。](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. 搜索并选择你的存储桶，以查看该存储桶的详细信息页，然后将存储桶名称复制到剪贴板。

    例如：

    ![检索并复制 S3 存储桶 URL。](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    将存储桶名称粘贴到一个安全的文件中，并为该文件添加一个 `s3://` 前缀，以创建将存储桶配置为 Purview 资源时需要输入的值。

    例如：`s3://purview-tutorial-bucket`

> [!TIP]
> 只支持将存储桶的根级别作为 Purview 数据源。 例如，下面的 URL 不受支持，因为其中包括一个子文件夹：`s3://purview-tutorial-bucket/view-data`
>
> 但是，如果为特定 S3 Bucket 配置扫描，可以选择一个或多个特定文件夹进行扫描。 有关详细信息，请查看用于[确定扫描范围](#create-a-scan-for-one-or-more-amazon-s3-buckets)的步骤。
>

### <a name="locate-your-aws-account-id"></a>找到 AWS 帐户 ID

你需要使用 AWS 帐户 ID 将 AWS 帐户及其所有存储桶注册为 Purview 数据源。

AWS 帐户 ID 是用于登录到 AWS 控制台的那个 ID。 在你登录后，也可以在 IAM 仪表板左侧导航选项下找到该 ID，它在仪表板顶部也显示为你的登录 URL 的数字部分：

例如：

![检索 AWS 帐户 ID。](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>添加一个 Amazon S3 存储桶作为 Purview 资源

如果你只有一个 S3 存储桶，并且想要将它注册到 Purview 作为数据源，或者你的 AWS 帐户中有多个存储桶，但你并不想将这些存储桶全部都注册到 Purview，请使用此过程。

添加 Bucket：

1. 在 Azure Purview 中，转到“数据映射”页，然后选择“注册”![注册图标](./media/register-scan-amazon-s3/register-button.png) 。 > “Amazon S3” > “继续”。

    ![添加一个 Amazon AWS 存储桶作为 Purview 数据源。](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > 如果你有多个[集合](manage-data-sources.md#manage-collections)，并且想要将 Amazon S3 添加到特定集合，请在右上方选择“映射视图”，然后在相应集合中选择“注册”![“注册”图标](./media/register-scan-amazon-s3/register-button.png) 按钮。
    >

1. 在打开的“注册源(Amazon S3)”窗格中，输入以下详细信息：

    |字段  |说明  |
    |---------|---------|
    |**名称**     |输入一个有意义的名称，或者使用提供的默认值。         |
    |**存储桶 URL**     | 使用下面的语法输入 AWS 存储桶 URL：`s3://<bucketName>`     <br><br>注意：请务必只使用 Bucket 的根级别。 有关详细信息，请参阅[检索 Amazon S3 存储桶名称](#retrieve-your-amazon-s3-bucket-name)。 |
    |**选择一个集合** |如果已选择注册某个集合内的数据源，则该集合已列出。 <br><br>根据需要选择其他集合，选择“无”表示不指定任何集合，也可选择“新建”来立即创建新集合。 <br><br>有关 Purview 集合的详细信息，请参阅[管理 Azure Purview 中的数据源](manage-data-sources.md#manage-collections)。|
    | | |

    完成后，选择“完成”以完成注册。

继续执行[创建一个或多个 Amazon S3 Bucket 的扫描](#create-a-scan-for-one-or-more-amazon-s3-buckets)。

## <a name="add-an-aws-account-as-a-purview-resource"></a>添加一个 AWS 帐户作为 Purview 源

如果你的 Amazon 帐户中有多个 S3 Bucket，并且你想要将所有 Bucket 都注册为 Purview 数据源，请使用此过程。

[配置扫描](#create-a-scan-for-one-or-more-amazon-s3-buckets)时，如果不希望同时扫描所有 Bucket，则可以选择要扫描的特定 Bucket。

添加 Amazon 帐户：

1. 在 Azure Purview 中，转到“数据映射”页，然后选择“注册”![注册图标](./media/register-scan-amazon-s3/register-button.png) 。 > “Amazon 帐户” > “继续”。

    ![添加一个 Amazon 帐户作为 Purview 数据源。](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > 如果你有多个[集合](manage-data-sources.md#manage-collections)，并且想要将 Amazon S3 添加到特定集合，请在右上方选择“映射视图”，然后在相应集合中选择“注册”![“注册”图标](./media/register-scan-amazon-s3/register-button.png) 按钮。
    >

1. 在打开的“注册源(Amazon S3)”窗格中，输入以下详细信息：

    |字段  |说明  |
    |---------|---------|
    |**名称**     |输入一个有意义的名称，或者使用提供的默认值。         |
    |**AWS 帐户 ID**     | 输入你的 AWS 帐户 ID。 有关详细信息，请参阅 [找到 AWS 帐户 ID](#locate-your-aws-account-id)|
    |**选择一个集合** |如果已选择注册某个集合内的数据源，则该集合已列出。 <br><br>根据需要选择其他集合，选择“无”表示不指定任何集合，也可选择“新建”来立即创建新集合。 <br><br>有关 Purview 集合的详细信息，请参阅[管理 Azure Purview 中的数据源](manage-data-sources.md#manage-collections)。|
    | | |

    完成后，选择“完成”以完成注册。

继续执行[创建一个或多个 Amazon S3 Bucket 的扫描](#create-a-scan-for-one-or-more-amazon-s3-buckets)。

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>创建一个或多个 Amazon S3 Bucket 的扫描

当你添加存储桶作为 Purview 数据源后，可以配置一个按计划的时间间隔运行或立即运行的扫描。

1. 在 [Purview Studio](https://web.purview.azure.com/resource/) 的左侧窗格中选择“数据映射”选项卡，然后执行以下操作之一：

    - 在“映射视图”中，从数据源框中选择“新建扫描” ![“新建扫描”图标](./media/register-scan-amazon-s3/new-scan-button.png) 。
    - 在“列表视图”中，将鼠标悬停在数据源行上，然后选择“新建扫描”![“新建扫描”图标](./media/register-scan-amazon-s3/new-scan-button.png)。

1. 在右侧打开的“扫描...”窗格上，定义以下字段，然后选择“继续”：

    |字段  |说明  |
    |---------|---------|
    |**名称**     |  为扫描输入一个有意义的名称，或者使用默认值。       |
    |类型 |只有当你添加了 AWS 帐户并且其中包含所有存储桶时，此字段才会显示。 <br><br>当前选项只有“所有” > “Amazon S3”。 随着 Purview 支持矩阵扩大，敬请期待更多可选选项。 |
    |**凭据**     |  为角色 ARN 选择 Purview 凭据。 <br><br>提示：如果你要立即创建新凭据，请选择“新建”。 有关详细信息，请参阅[创建 Purview 凭据以用于扫描 AWS 存储桶](#create-a-purview-credential-for-your-aws-s3-scan)。     |
    | **Amazon S3**    |   只有当你添加了 AWS 帐户并且其中包含所有存储桶时，此字段才会显示。 <br><br>选择一个或多个 Bucket 进行扫描，或选择“全选”以扫描帐户中的所有 Bucket。      |
    | | |

    Purview 会自动检查角色 ARN 是否有效，以及 Bucket 及其中的对象是否可供访问，如果连接成功，则继续操作。

    > [!TIP]
    > 在继续操作前，若要输入其他值并自行测试连接，请先在底部选择“测试连接”，然后再选择“继续”。
    >

1. <a name="scope-your-scan"></a>在“确定扫描范围”窗格中，选择要包括在扫描中的特定 Bucket 或文件夹。

    为整个 AWS 帐户创建扫描时，可以选择要扫描的特定 Bucket。 为特定 AWS S3 Bucket 创建扫描时，可以选择要扫描的特定文件夹。

1. 在“选择扫描规则集”窗格上，选择默认规则集“AmazonS3”，或选择“新建扫描规则集”来创建新的自定义规则集。 选择规则集后，选择“继续”。

    如果你选择创建新的自定义扫描规则集，请使用向导来定义以下设置：

    |窗格  |说明  |
    |---------|---------|
    |**新建扫描规则集** /<br>**扫描规则说明**    |   为规则集输入一个有意义的名称和一段说明（可选）      |
    |**选择文件类型**     | 选择你要包含在扫描范围中的所有文件类型，然后选择“继续”。<br><br>若要添加新的文件类型，请选择“新建文件类型”，并定义以下内容： <br>- 要添加的文件扩展名 <br>- 一段说明（可选）  <br>- 文件内容是具有自定义分隔符还是属于系统文件类型。 然后，输入自定义分隔符，或选择系统文件类型。 <br><br>选择“创建”，创建自定义文件类型。     |
    |**选择分类规则**     |   导航到你要在数据集上运行的分类规则，并选择这些规则。      |
    |     |         |

    完成后选择“创建”，以创建规则集。

1. 在“设置扫描触发器”窗格上，选择以下选项之一，然后选择“继续”：

    - “定期”：将配置定期扫描计划
    - “一次”：将配置一个会立即启动的扫描

1. 在“查看扫描”窗格上，检查扫描详细信息以确认信息正确无误，然后选择“保存”，如果你在上一个窗格中选择了“一次”，则选择“保存并运行”。

    > [!NOTE]
    > 启动扫描后，最长可能需要 24 小时才能完成。 启动扫描 24 小时后，你将能够查看“见解报告”，并可在目录中进行搜索。
    >

有关详细信息，请参阅[浏览 Purview 扫描结果](#explore-purview-scanning-results)。

## <a name="explore-purview-scanning-results"></a>浏览 Purview 扫描结果

对 Amazon S3 存储桶的 Purview 扫描完成后，请在 Purview 的“数据映射”区域中向下钻取，查看扫描历史记录。

选择数据源以查看其详细信息，然后选择“扫描”选项卡，查看当前正在运行或已完成的任何扫描。
如果你已添加包含多个存储桶的 AWS 帐户，此帐户下将显示每个存储桶的扫描历史记录。

例如：

![AWS 帐户源下显示 AWS S3 存储桶扫描。](./media/register-scan-amazon-s3/account-scan-history.png)

在 Purview 的其他区域中，可以详细了解数据资产中的内容（包括 Amazon S3 存储桶）：

- 在 Purview 数据目录中搜索，筛选特定存储桶。 例如：

    ![在目录中搜索 AWS S3 资产。](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- 查看见解报告，了解关于分类、敏感度标签、文件类型的统计信息，以及有关你的内容的更多细节。

    所有 Purview 见解报告都包括 Amazon S3 扫描结果，以及来自 Azure 数据源的其余结果。 报告筛选选项中也可能会额外添加一个“Amazon S3”资产类型（如果具有相关性）。

    有关详细信息，请参阅[了解 Azure Purview 中的见解](concept-insights.md)。

## <a name="minimum-permissions-for-your-aws-policy"></a>AWS 策略的最低权限

扫描 S3 桶时要使用的、用于[为 Purview 创建 AWS 角色](#create-a-new-aws-role-for-purview)的默认过程使用 AmazonS3ReadOnlyAccess 策略。

AmazonS3ReadOnlyAccess 策略提供扫描 S3 桶所需的最低权限，也可能包括其他权限。

若要仅应用扫描桶所需的最低权限，请根据你是要扫描帐户中的单个桶还是所有桶，使用以下部分中列出的权限创建新策略。

将新策略应用于角色而不是 AmazonS3ReadOnlyAccess。

### <a name="individual-buckets"></a>单个桶

扫描单个 S3 桶时，最低 AWS 权限包括：

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

请确保使用特定的桶名称定义资源。 例如：

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>帐户中的所有桶

扫描 AWS 帐户中的所有桶时，最低 AWS 权限包括：

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

请确保使用通配符定义资源。 例如：

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

详细了解 Azure Purview 见解报告：

> [!div class="nextstepaction"]
> [了解 Azure Purview 中的见解](concept-insights.md)
