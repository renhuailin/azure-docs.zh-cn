---
title: 如何扫描 Amazon S3 存储桶
description: 本操作方法指南介绍了如何扫描 Amazon S3 存储桶的详细信息。
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/19/2021
ms.custom: references_regions
ms.openlocfilehash: 7d3fd0b1ffb87a84772000702b958c52ed1cc47c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679192"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>用于 Amazon S3 的 Azure 监控范围连接器

本操作方法指南说明了如何使用 Azure 监控范围扫描当前存储在 Amazon S3 标准存储桶中的非结构化数据，并发现数据中存在哪些类型的敏感信息。 本操作方法指南还介绍了如何确定当前存储数据的 Amazon S3 存储桶，以方便信息保护和数据符合性。

对于此服务，请使用监控范围提供对 AWS （监控范围扫描程序将在其中运行）的安全访问的 Microsoft 帐户。 监控范围扫描程序使用此访问 Amazon S3 存储桶来读取数据，然后将扫描结果（仅包括元数据和分类）报告回 Azure。 使用监控范围分类和标签报表分析和查看数据扫描结果。

在本操作方法指南中，你将了解如何将 Amazon S3 存储桶添加为监控范围资源，并为 Amazon S3 数据创建扫描。

## <a name="purview-scope-for-amazon-s3"></a>Amazon S3 的监控范围范围

以下作用域特定于将 Amazon S3 存储桶注册为监控范围数据源。

|范围  |说明  |
|---------|---------|
|**数据限制**     |    监控范围 scanner 服务目前支持扫描 Amazon S3 存储桶，每个租户最多 100 GB 数据。     |
|**文件类型**     | 监控范围 scanner 服务目前支持以下文件类型： <br><br>avro、.csv、.doc、. docm、. .docx，.dot，. json，.pptx，. parquet，.pdf，. .pot，. ppsx，. psv，. ssv，. xlc，. .xlsb，.xls，. xlsm，.xml，.，.xlsx，. .xml，. .xml，.，.xml，...。        |
|**区域**     | Amazon S3 服务的监控范围连接器目前仅部署在 **AWS 美国东部 (俄亥俄州)** 和 **欧洲 (法兰克福)** 区域。 <br><br>有关详细信息，请参阅 [存储和扫描区域](#storage-and-scanning-regions)。   |
|     |         |

有关详细信息，请参阅：

- [利用 Azure 监控范围管理和提高资源配额](how-to-manage-quotas.md)
- [Azure 监控范围中受支持的数据源和文件类型](sources-and-scans.md)
### <a name="storage-and-scanning-regions"></a>存储和扫描区域

下表映射了将数据存储到 Azure 监控范围扫描区域的区域。

> [!IMPORTANT]
> 根据存储桶的区域，将向客户收取所有相关的数据传输费用。
>

| 存储区域 | 扫描区域 |
| ------------------------------- | ------------------------------------- |
| 美国东部 (俄亥俄州)                   | 美国东部 (俄亥俄州)                         |
| 美国东部 (N。 弗吉尼亚州)            | 美国东部 (俄亥俄州)                         |
| 美国西部 (N。 加利福尼亚)          | 美国东部 (俄亥俄州)                         |
| 美国西部（俄勒冈州）                | 美国东部 (俄亥俄州)                         |
| 非洲 (佛得角镇)               | 欧洲 (法兰克福)                     |
| 亚太 (香港特别行政区)         | 欧洲 (法兰克福)                     |
| 亚太 (孟买)            | 欧洲 (法兰克福)                     |
| 亚太 (大阪-本地)       | 欧洲 (法兰克福)                     |
| 亚太 (首尔)             | 欧洲 (法兰克福)                     |
| 亚太（新加坡）        | 欧洲 (法兰克福)                     |
| 亚太（悉尼）           | 欧洲 (法兰克福)                     |
| 亚太（东京）            | 欧洲 (法兰克福)                     |
| 加拿大 (中部)                 | 美国东部 (俄亥俄州)                         |
| 中国 (北京)                  | 欧洲 (法兰克福)                     |
| 中国 (Ningxia)                  | 欧洲 (法兰克福)                     |
| 欧洲 (法兰克福)               | 欧洲 (法兰克福)                     |
| 欧洲 (爱尔兰)                 | 欧洲 (法兰克福)                     |
| 欧洲 (伦敦)                  | 欧洲 (法兰克福)                     |
| 欧洲 (Milan)                   | 欧洲 (法兰克福)                     |
| 欧洲 (巴黎)                   | 欧洲 (法兰克福)                     |
| 欧洲 (斯德哥尔摩)               | 欧洲 (法兰克福)                     |
| 中东 (巴林)            | 欧洲 (法兰克福)                     |
| 南美 (圣保罗)        | 美国东部 (俄亥俄州)                         |
| | |
## <a name="prerequisites"></a>先决条件

在将 Amazon S3 存储桶添加为监控范围数据源并扫描 S3 数据之前，请确保已完成以下先决条件。

- 你需要是 Azure 监控范围数据源管理员。

- 将 bucket 作为监控范围资源添加时，需要 [AWS ARN](#retrieve-your-new-role-arn)、 [bucket 名称](#retrieve-your-amazon-s3-bucket-name)，有时还需要 [AWS 帐户 ID](#locate-your-aws-account-id)的值。

### <a name="create-a-purview-account"></a>创建监控范围帐户

- **如果已经有监控范围帐户，** 可以继续使用 AWS S3 支持所需的配置。 首先， [为 AWS bucket 扫描创建监控范围凭据](#create-a-purview-credential-for-your-aws-bucket-scan)。

- **如果需要创建监控范围帐户，** 请按照 [创建 Azure 监控范围帐户实例](create-catalog-portal.md)中的说明进行操作。 创建帐户后，返回到此处完成配置，并开始使用适用于 Amazon S3 的监控范围连接器。

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>为 AWS bucket 扫描创建监控范围凭据

此过程介绍如何创建新的监控范围凭据，以在扫描 AWS bucket 时使用。

> [!TIP]
> 你还可以在过程中创建新凭据，同时 [配置扫描](#create-a-scan-for-your-amazon-s3-bucket)。 在这种情况下，在 " **凭据** " 字段中选择 " **新建**"。
>

1. 在监控范围中，导航到 **管理中心**，然后在 " **安全和访问**" 下选择 " **凭据**"。

1. 选择 " **新建**"，然后在右侧显示的 " **新建凭据** " 窗格中，使用以下字段创建监控范围凭据：

    |字段 |说明  |
    |---------|---------|
    |**Name**     |为此凭据输入有意义的名称，或使用默认值。        |
    |**说明**     |为此凭据输入可选描述，如 `Used to scan the tutorial S3 buckets`         |
    |**身份验证方法**     |选择 **ROLE ARN**，因为使用角色 ARN 访问存储桶。         |
    |**Microsoft 帐户 ID**     |单击此值可将此值复制到剪贴板。 [在 AWS 中创建角色 ARN](#create-a-new-aws-role-for-purview)时，请使用此值作为 **Microsoft 帐户 ID** 。           |
    |**外部 ID**     |单击此值可将此值复制到剪贴板。 [在 AWS 中创建角色 ARN](#create-a-new-aws-role-for-purview)时，请使用此值作为 **外部 ID** 。        |
    |**角色 ARN**     | [创建 AMAZON IAM 角色](#create-a-new-aws-role-for-purview)后，请导航到 "IAM" 区域中的角色，复制 "**角色 ARN** " 值，并在此处输入。 例如：`arn:aws:iam::284759281674:role/S3Role`。 <br><br>有关详细信息，请参阅 [检索新角色 ARN](#retrieve-your-new-role-arn)。 |
    | | |

    完成创建凭据后，选择 " **创建** "。

有关监控范围凭据的详细信息，请参阅 [Azure 监控范围公共预览文档](manage-credentials.md)。

### <a name="create-a-new-aws-role-for-purview"></a>为监控范围创建新的 AWS 角色

1.  打开 **Amazon Web Services** 控制台，在 " **安全、标识和符合性**" 下，选择 " **IAM**"。

1. 选择 " **角色** "，然后选择 " **创建角色**"。

1. 选择 **另一个 AWS 帐户**，然后输入以下值：

    |字段  |说明  |
    |---------|---------|
    |**帐户 ID**     |    输入你的 Microsoft 帐户 ID。 例如：`615019938638`     |
    |**外部 ID**     |   在 "选项" 下，选择 " **需要外部 id ...**"，然后在指定字段中输入外部 id。 <br>例如：`e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`    <br><br>可以在时找到此外部 ID。  |
    | | |

    > [!NOTE]
    > 你可以在 **"监控范围管理中心** 凭据" 区域中找到 **Microsoft 帐户 id** 和 **外部 ID** 的值  >   ，你可以在其中 [创建监控范围凭据](#create-a-purview-credential-for-your-aws-bucket-scan)。
    >

    例如：

    ![将 Microsoft 帐户 ID 添加到你的 AWS 帐户。](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. 在 " **创建角色 > 附加权限策略** " 区域中，筛选显示给 **S3** 的权限。 选择 " **AmazonS3ReadOnlyAccess**"，然后选择 " **下一步：标记**"。

    ![选择新 Amazon S3 扫描角色的 ReadOnlyAccess 策略。](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

1. 在 " **添加标记 (可选)** 区域中，可以选择为此新角色创建有意义的标记。 使用有用的标记可以为所创建的每个角色组织、跟踪和控制访问权限。

    根据需要为标记输入新密钥和值。 完成后，或者如果要跳过此步骤，请选择 " **下一步：查看** " 以查看角色详细信息并完成角色创建。

    ![添加有意义的标记以组织、跟踪或控制新角色的访问权限。](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. 在 " **审阅** " 区域中，执行以下操作：

    - 在 " **角色名称** " 字段中，为你的角色输入有意义的名称
    - 在 " **角色说明** " 框中，输入可选描述以标识该角色的用途
    - 在 " **策略** " 部分中，确认 **AmazonS3ReadOnlyAccess**) 的正确 (策略已附加到角色。

    然后选择 " **创建角色** " 以完成该过程。

    例如：

    ![创建角色之前，请查看详细信息。](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>配置加密 Amazon S3 存储桶的扫描

AWS bucket 支持多种加密类型。 对于使用 **AWS-KMS** 加密的存储桶，需要特殊配置才能启用扫描。

> [!NOTE]
> 对于不使用加密、AES-256 或 AWS S3 加密的存储桶，请跳过此部分并继续 [检索 Amazon s3 存储桶名称](#retrieve-your-amazon-s3-bucket-name)。
>

**若要检查 Amazon S3 存储桶中使用的加密类型，请执行以下操作：**

1. 在 AWS 中，导航到 **Storage**  >  **S3** > 并从左侧菜单中选择 " **bucket** "。

    ![选择 "Amazon S3 存储桶" 选项卡。](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. 选择要检查的存储桶。 在 bucket 的详细信息页上，选择 " **属性** " 选项卡，然后向下滚动到 " **默认加密** " 区域。

    - 如果你选择的存储桶是为任何内容配置的（包括 **AWS** ），包括是否 **禁用** 了 bucket 的默认加密，请跳过此过程的其余部分，继续 [检索 Amazon S3 存储桶名称](#retrieve-your-amazon-s3-bucket-name)。

    - 如果为 **AWS-kms** 加密配置了所选的存储桶，请按照下面所述继续操作，以添加一个新策略，该策略允许使用自定义 **AWS** 加密来扫描存储桶。

    例如：

    ![查看使用 AWS 加密配置的 Amazon S3 存储桶](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**添加新策略以允许使用自定义 AWS 加密存储桶：**

1. 在 AWS 中，导航到 "**服务**" "  >   **IAM**  >   **策略**"，然后选择 "**创建策略**"。

1. 在 "**创建策略**  >  **视觉对象编辑器**" 选项卡上，定义具有以下值的策略：

    |字段  |说明  |
    |---------|---------|
    |**服务**     |  输入，然后选择 " **KMS**"。       |
    |**操作**     | 在 " **访问级别**" 下，选择 " **写入** " 展开 " **写入** " 部分。<br>展开后，请仅选择 " **解密** " 选项。        |
    |**资源**     |选择特定的资源或 **所有资源**。         |
    | | |

    完成后，请选择 " **查看策略** " 以继续。

    ![创建用于扫描使用 AWS 加密的存储桶的策略。](./media/register-scan-amazon-s3/create-policy-kms.png)

1. 在 " **查看策略** " 页上，为策略输入有意义的名称和可选描述，然后选择 " **创建策略**"。

    新创建的策略将添加到策略列表中。

1. 将新策略附加到为扫描添加的角色。

    1. 导航回 " **IAM**  >  **角色**" 页，选择 [前面](#create-a-new-aws-role-for-purview)添加的角色。

    1. 在 " **权限** " 选项卡上，选择 " **附加策略**"。

        ![在角色的 "权限" 选项卡上，选择 "附加策略"。](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. 在 " **附加权限** " 页面上，搜索并选择前面创建的新策略。 选择 " **附加策略** "，将策略附加到角色。

        " **摘要** " 页面将更新，并将新策略附加到角色。

        ![查看已更新的摘要页面，其中包含附加到角色的新策略。](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>检索新角色 ARN

为 [Amazon S3 存储桶创建扫描](#create-a-scan-for-your-amazon-s3-bucket)时，需要记录 AWS 角色 ARN 并将其复制到监控范围。

**检索角色 ARN：**

1. 在 "AWS **标识和访问管理 (IAM)**  >  **角色**" 区域中，搜索并选择 [为监控范围创建](#create-a-purview-credential-for-your-aws-bucket-scan)的新角色。

1. 在角色的 "**摘要**" 页上，选择 **role ARN** 值右侧的 "**复制到剪贴板**" 按钮。

    ![将 role ARN 值复制到剪贴板。](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. 将此值粘贴到安全位置，以便在 [为 Amazon S3 存储桶创建扫描](#create-a-scan-for-your-amazon-s3-bucket)时使用。

### <a name="retrieve-your-amazon-s3-bucket-name"></a>检索 Amazon S3 存储桶名称

为[Amazon s3 存储桶创建扫描](#create-a-scan-for-your-amazon-s3-bucket)时，你将需要 Amazon s3 存储桶的名称，以便将其复制到监控范围

**检索存储桶名称：**

1. 在 AWS 中，导航到 **Storage**  >  **S3** > 并从左侧菜单中选择 " **bucket** "。

    ![查看 Amazon S3 Bucket 选项卡。](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. 搜索并选择你的 bucket 以查看 bucket 详细信息页，然后将 bucket 名称复制到剪贴板。

    例如：

    ![检索并复制 S3 bucket URL。](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    将 bucket 名称粘贴到安全文件中，并在其中添加一个 `s3://` 前缀，以创建将存储桶配置为监控范围资源时需要输入的值。

    例如：`s3://purview-tutorial-bucket`

> [!NOTE]
> 只支持将 bucket 的根级别作为监控范围数据源。 例如，以下 URL *不* 受支持，其中包括子文件夹： `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>找到你的 AWS 帐户 ID

需要使用 AWS 帐户 ID 将 AWS 帐户注册为监控范围数据源及其所有存储桶。

你的 AWS 帐户 ID 是用于登录到 AWS 控制台的 ID。 你还可以在以下情况下找到它：登录到 IAM 仪表板，在左侧导航选项下方，在顶部，作为登录 URL 的数字部分：

例如：

![检索你的 AWS 帐户 ID。](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>添加单个 Amazon S3 存储桶作为监控范围资源

如果你只想要将一个 S3 存储桶注册到监控范围作为数据源，或者在 AWS 帐户中有多个存储桶，但又不想将其注册到监控范围，请使用此过程。

1. 使用专用监控范围连接器为 Amazon S3 URL 启动监控范围门户。 Amazon S3 监控范围 connector 产品管理团队提供了此 URL。

    ![启动监控范围门户。](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. 导航到 "Azure 监控范围 **源** " 页，然后选择 " **注册** ![ 注册图标"。](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  > **继续**。

    ![将 Amazon AWS bucket 添加为监控范围数据源。](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > 如果有多个 [集合](manage-data-sources.md#manage-collections) ，并想要将 Amazon S3 添加到特定集合，请选择右上角的 " **地图" 视图** ，然后选择 " **注册** ![ 注册" 图标。](./media/register-scan-amazon-s3/register-button.png) 按钮。
    >

1. 在打开的 " **注册源 (Amazon S3)** " 窗格中，输入以下详细信息：

    |字段  |说明  |
    |---------|---------|
    |**Name**     |输入有意义的名称，或使用提供的默认值。         |
    |**Bucket URL**     | 使用以下语法输入 AWS bucket URL：   `s3://<bucketName>`     <br><br>**注意**：请确保仅使用存储桶的根级别，不包含任何子文件夹。 有关详细信息，请参阅 [检索 Amazon S3 存储桶名称](#retrieve-your-amazon-s3-bucket-name)。 |
    |**选择集合** |如果选择从集合中注册数据源，则该集合已列出。 <br><br>根据需要选择一个不同的集合，单击 " **无** " 可分配 "无"，选择 " **新建** " 以立即创建新的集合。 <br><br>有关监控范围集合的详细信息，请参阅 [管理 Azure 监控范围中的数据源](manage-data-sources.md#manage-collections)。|
    | | |

    完成后，选择 " **完成** " 以完成注册。

继续为[Amazon S3 存储桶创建扫描。](#create-a-scan-for-your-amazon-s3-bucket)

## <a name="add-all-of-your-amazon-s3-buckets-as-purview-resources"></a>将所有 Amazon S3 存储桶添加为监控范围资源

如果 Amazon 帐户中有多个 S3 存储桶，并且想要注册所有 as 监控范围数据源，请使用此过程。

1. 使用专用监控范围连接器为 Amazon S3 URL 启动监控范围门户。 Amazon S3 监控范围 connector 产品管理团队提供了此 URL。

    ![启动 Amazon S3 专用监控范围门户的连接器](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. 导航到 "Azure 监控范围 **源** " 页，然后选择 " **注册** ![ 注册图标"。](./media/register-scan-amazon-s3/register-button.png) > **Amazon 帐户**  > **继续**。

    ![添加 Amazon 帐户作为监控范围数据源。](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > 如果有多个 [集合](manage-data-sources.md#manage-collections) ，并想要将 Amazon S3 添加到特定集合，请选择右上角的 " **地图" 视图** ，然后选择 " **注册** ![ 注册" 图标。](./media/register-scan-amazon-s3/register-button.png) 按钮。
    >

1. 在打开的 " **注册源 (Amazon S3)** " 窗格中，输入以下详细信息：

    |字段  |说明  |
    |---------|---------|
    |**Name**     |输入有意义的名称，或使用提供的默认值。         |
    |**AWS 帐户 ID**     | 输入你的 AWS 帐户 ID。 有关详细信息，请参阅 [查找你的 AWS 帐户 ID](#locate-your-aws-account-id)|
    |**选择集合** |如果选择从集合中注册数据源，则该集合已列出。 <br><br>根据需要选择一个不同的集合，单击 " **无** " 可分配 "无"，选择 " **新建** " 以立即创建新的集合。 <br><br>有关监控范围集合的详细信息，请参阅 [管理 Azure 监控范围中的数据源](manage-data-sources.md#manage-collections)。|
    | | |

    完成后，选择 " **完成** " 以完成注册。

继续为 [Amazon S3 存储桶创建扫描](#create-a-scan-for-your-amazon-s3-bucket)。

## <a name="create-a-scan-for-your-amazon-s3-bucket"></a>为 Amazon S3 存储桶创建扫描

将 bucket 作为监控范围数据源添加后，可以将扫描配置为按计划的时间间隔或立即运行。

1. 导航到 "Azure 监控范围 **源** " 区域，然后执行下列操作之一：

    - 在 **地图视图** 中，选择 " **新建扫描** ![ 新扫描" 图标。](./media/register-scan-amazon-s3/new-scan-button.png) 在 "数据源" 框中。
    - 在 **列表视图** 中，将鼠标悬停在数据源的行上，然后选择 "**新扫描**" " ![ 新扫描" 图标。 ](./media/register-scan-amazon-s3/new-scan-button.png)

1. 在右侧打开的 " **扫描 ...** " 窗格上，定义以下字段，然后选择 " **继续**"：

    |字段  |说明  |
    |---------|---------|
    |**Name**     |  为扫描输入有意义的名称，或使用默认值。       |
    |类型 |仅当你已添加 AWS 帐户并包含所有存储桶时才显示。 <br><br>当前选项仅包括 **所有**  >  **Amazon S3**。 请继续关注更多选项以供选择，监控范围的支持矩阵将展开。 |
    |**凭据**     |  选择角色为 ARN 的监控范围凭据。 <br><br>**提示**：如果想要在此时创建新凭据，请选择 " **新建**"。 有关详细信息，请参阅为 [AWS bucket 扫描创建监控范围凭据](#create-a-purview-credential-for-your-aws-bucket-scan)。     |
    |     |         |

    监控范围会自动检查角色 ARN 是否有效，以及是否可访问存储桶中的存储桶和对象，如果连接成功，则继续。

    > [!TIP]
    > 若要输入不同的值并自行测试连接，请选择底部的 " **测试连接** "，然后选择 " **继续**"。
    >

1. 在 " **选择扫描规则集** " 窗格上，选择 " **AmazonS3** " 默认规则集，或选择 " **新建扫描规则集** " 创建新的自定义规则集。 选择规则集后，选择 " **继续**"。

    如果选择创建新的自定义扫描规则集，请使用向导来定义下列设置：

    |窗格  |说明  |
    |---------|---------|
    |**新扫描规则集** /<br>**扫描规则描述**    |   为规则集输入有意义的名称和可选描述      |
    |**选择文件类型**     | 选择要包括在扫描中的所有文件类型，然后选择 " **继续**"。<br><br>若要添加新的文件类型，请选择 " **新建文件类型**"，并定义以下内容： <br>-要添加的文件扩展名 <br>-可选描述  <br>-文件内容是否具有自定义分隔符，或是否为系统文件类型。 然后，输入自定义分隔符，或选择系统文件类型。 <br><br>选择 " **创建** " 创建自定义文件类型。     |
    |**选择分类规则**     |   导航到并选择要在数据集上运行的分类规则。      |
    |     |         |

    创建规则集完成后，选择 " **创建** "。

1. 在 " **设置扫描触发器** " 窗格上，选择以下各项之一，然后选择 " **继续**"：

    - **定期** 为定期扫描配置计划
    - **一次** 配置立即启动的扫描

1. 在 "**查看扫描**" 窗格上，检查你的扫描详细信息以确认它们正确无误，然后选择 "**保存**" 或 "保存"，并在上一个 **窗格中选择** 了 "保存 **并运行**"。

    > [!NOTE]
    > 启动后，扫描可能需要长达24小时才能完成。 开始每次扫描后，你将能够查看 **见解报告** 并搜索目录24小时。
    >

有关详细信息，请参阅 [探索监控范围扫描结果](#explore-purview-scanning-results)。

## <a name="explore-purview-scanning-results"></a>探索监控范围扫描结果

在 Amazon S3 存储桶上完成监控范围扫描后，请在 "监控范围 **源** " 区域中向下钻取以查看扫描历史记录。

选择数据源以查看其详细信息，然后选择 " **扫描** " 选项卡以查看当前正在运行或已完成的任何扫描。
如果已添加具有多个存储桶的 AWS 帐户，则该帐户下将显示每个存储桶的扫描历史记录。

例如：

![在 AWS 帐户源下显示 AWS S3 bucket 扫描。](./media/register-scan-amazon-s3/account-scan-history.png)

使用监控范围的其他区域来了解有关数据空间中的内容的详细信息，包括 Amazon S3 存储桶：

- **搜索监控范围数据目录，** 并筛选特定存储桶。 例如：

    ![在目录中搜索 AWS S3 资产。](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **查看见解报告** 来查看分类、敏感度标签、文件类型和内容的详细信息。

    所有监控范围见解报告都包括 Amazon S3 扫描结果，以及 Azure 数据源的其余结果。 相关时，会向报表筛选选项添加其他 **Amazon S3** 资产类型。

    有关详细信息，请参阅 [了解 Azure 中的见解监控范围](concept-insights.md)。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 监控范围见解报表：

> [!div class="nextstepaction"]
> [了解 Azure Purview 中的见解](concept-insights.md)
