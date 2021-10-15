---
title: 添加 XSLT 映射以在工作流中转换 XML
description: 添加 XSLT 映射以使用 Azure 逻辑应用和 Enterprise Integration Pack 在工作流转换 XML。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: 55c6d6c20b98415fa09725d4101317ae6c43e23f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359488"
---
# <a name="add-xslt-maps-to-transform-xml-in-workflows-with-azure-logic-apps"></a>添加 XSLT 映射以使用 Azure 逻辑应用在工作流中转换 XML

若要在格式之间转换 XML，你的逻辑应用工作流可以将映射与“转换 XML”操作结合使用。 映射是使用可扩展样式表语言转换 (XSLT) 语言的 XML 文档，用于描述如何将数据从 XML 转换为其他格式。 映射由一个作为输入的源 XML 架构和一个作为输出的目标 XML 架构组成。 你可以定义一个基本转换，例如，将名称和地址从一个文档复制到另一个文档。 或者，可以使用现成可用的映射操作创建更复杂的转换。 可以使用不同的内置函数来操作或控制数据，如字符串操作、条件分配、算术表达式、日期时间格式化程序甚至是循环构造。

例如，假设你从使用 YearMonthDay 日期格式 (YYYYMMDD) 的客户定期接收 B2B 订单或发票。 但是，你的组织使用 MonthDayYear 日期格式 (MMDDYYYY)。 在将订单或发票详细信息存储在客户活动数据库中之前，可以定义并使用一个映射，以便将 YYYYMMDD 格式转换为 MMDDYYYY 格式。

> [!NOTE]
> Azure 逻辑应用会分配有限的内存来处理 XML 转换。 如果基于[“逻辑应用(消耗)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)创建逻辑应用，并且映射或有效负载转换的内存消耗较高，则此类转换可能会失败，从而导致内存不足错误。 若要避免这种情况，请考虑以下选项：
>
> * 编辑映射或有效负载以减少内存消耗。
>
> * 请改用[“逻辑应用(标准)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)来创建逻辑应用。
>
>   这些工作流在单租户 Azure 逻辑应用中运行，为计算和内存资源提供专用且灵活的选项。 
>   但是，标准逻辑应用资源类型目前不支持从映射引用外部程序集。 此外，目前仅支持可扩展样式表语言转换 (XSLT) 1.0。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](logic-apps-overview.md) 有关 B2B 企业集成的详细信息，请参阅[使用 Azure 逻辑应用和 Enterprise Integration Pack 构建的 B2B 企业集成工作流](logic-apps-enterprise-integration-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 若要创建映射，可使用以下工具：

  * Visual Studio 2019 和 [Microsoft Azure 逻辑应用企业集成工具扩展](https://aka.ms/vsenterpriseintegrationtools)。

  * Visual Studio 2015 和[适用于 Visual Studio 2015 2.0 的 Microsoft Azure 逻辑应用企业集成工具](https://aka.ms/vsmapsandschemas)扩展。

   > [!IMPORTANT]
   > 不要将此扩展与 BizTalk Server 扩展一起安装。 同时具有这两个扩展可能会导致意外行为。 请确保只安装其中一个扩展。

   > [!NOTE]
   > 在高分辨率监视器上，可能会在 Visual Studio 中遇到[地图设计器显示问题](/visualstudio/designers/disable-dpi-awareness)。 若要解决此显示问题，请[在 DPI 无感知模式下重启 Visual Studio](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process) 或添加 [DPIUNAWARE 注册表值](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry)。

* 一个可以在其中定义和存储项目（如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与你计划在其中使用“转换 XML”操作的逻辑应用资源位于同一个位置或 Azure 区域。

  * 如果使用的是[“逻辑应用(消耗)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，必须[将集成帐户链接到逻辑应用资源](logic-apps-enterprise-integration-create-integration-account.md#link-account)，然后才能在工作流中使用项目。

    若要创建和添加要在“逻辑应用(消耗)”工作流中使用的映射，尚不需要逻辑应用资源。 但是，当你准备好在工作流中使用这些映射时，逻辑应用资源需有一个用于存储这些映射的链接集成帐户。

  * 如果使用的是[“逻辑应用(标准)”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，则需要一个现有的逻辑应用资源，因为你不会在集成帐户中存储映射。 但可以使用 Azure 门户或 Visual Studio Code 将映射直接添加到逻辑应用资源。 目前仅支持 XSLT 1.0。 然后，可以在同一逻辑应用资源内的多个工作流中使用这些映射。

    你仍需要一个集成帐户，用来存储其他项目（例如合作伙伴、协议和证书），以及使用 [AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 和 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作。 但是，你不需要将逻辑应用资源链接到集成帐户，因此链接功能不存在。 集成帐户仍必须满足其他要求，例如，使用相同的 Azure 订阅并与逻辑应用资源存在于同一位置。

    > [!NOTE]
    > 目前，仅“逻辑应用(消耗)”资源类型支持 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。 “逻辑应用(标准)”资源类型不包括 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。

* 尽管逻辑应用（消耗型）支持从映射引用外部程序集，但逻辑应用（标准型）目前并不支持此功能 。 引用某个程序集可以直接从 XSLT 映射调用自定义 .NET 代码。

  * 需要一个 64 位程序集。 转换服务运行 64 位进程，因此不支持 32 位程序集。 如果有 32 位程序集的源代码，请将此代码重新编译为 64 位程序集。 如果没有源代码，但从第三方提供程序获取了二进制文件，请从该提供程序获取 64 位版本。 例如，某些供应商在包中提供有 32 位和 64 位版本的程序集。 如果可以选择，请改用 64 位版本。

  * 必须按特定的顺序将程序集和映射上传到集成帐户。 请务必[先上传程序集](#add-assembly)，然后再上传引用程序集的映射。 

  * 如果程序集为 [2 MB 或更小](#smaller-map)，你可以直接从 Azure 门户将程序集和映射添加到集成帐户。 但是，如果程序集或映射大于 2 MB 但不超过[程序集或映射的大小限制](logic-apps-limits-and-config.md#artifact-capacity-limits)，你可以使用一个可在其中上传程序集的 Azure Blob 容器并指定该容器的位置。 这样，在将程序集添加到集成帐户时，便可以提供该位置。 对于此任务，需要提供以下各项：

    | 项目 | 说明 |
    |------|-------------|
    | [Azure 存储帐户](../storage/common/storage-account-overview.md) | 在此帐户中创建程序集的 Azure Blob 容器。 了解[如何创建存储帐户](../storage/common/storage-account-create.md)。 |
    | Blob 容器 | 可在此容器中上传程序集。 将程序集添加到集成帐户时，也需要此容器的内容 URI 位置。 了解如何[创建 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 |
    | [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md) | 借助此工具可以更轻松地管理存储帐户和 Blob 容器。 若要使用存储资源管理器，请[下载并安装 Azure 存储资源管理器](https://www.storageexplorer.com/)。 然后，遵循[存储资源管理器入门](../vs-azure-tools-storage-manage-with-storage-explorer.md)中的步骤将存储资源管理器连接到存储帐户。 若要了解详细信息，请参阅[快速入门：使用 Azure 存储资源管理器在对象存储中创建 Blob](../storage/blobs/quickstart-storage-explorer.md)。 <p>或者在 Azure 门户中，选择你的存储帐户。 在存储帐户菜单中选择“存储资源管理器”。  |
    |||

  * 若要为“逻辑应用(消耗)”资源类型添加更大的映射，还可使用 [Azure 逻辑应用 REST API - 映射](/rest/api/logic/maps/createorupdate)。 但是，对于“逻辑应用(标准)”资源类型，Azure 逻辑应用 REST API 目前不可用。

## <a name="limits"></a>限制

* 对于“逻辑应用(标准)”，映射文件大小没有限制。

* 对于“逻辑应用(消耗)”，集成帐户和项目（例如映射）存在限制。 有关详细信息，请查看 [Azure 逻辑应用的限制和配置信息](logic-apps-limits-and-config.md#integration-account-limits)。

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies-consumption-resource-only"></a>添加引用的程序集（仅限消耗型资源）

1. 在 [Azure 门户](https://portal.azure.com)中，使用 Azure 帐户凭据登录。

1. 在 Azure 主搜索框中输入 `integration accounts`，然后选择“集成帐户”。

1. 选择要将程序集添加到的集成帐户。

1. 在集成帐户的菜单上，选择“概述”。 在“设置”下选择“程序集” 。

1. 在“程序集”窗格工具栏上，选择“添加” 。

根据程序集文件的大小，遵循相应的步骤来上传[不超过 2 MB](#smaller-assembly) 或[大于 2 MB 但不超过 8 MB](#larger-assembly) 的程序集。 有关集成帐户中程序集数量的限制，请查看 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md#artifact-number-limits)。

> [!NOTE]
> 如果更改程序集，则无论映射是否有更改，都必须更新映射。

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>添加不超过 2 MB 的程序集

1. 在“添加程序集”下，输入程序集的名称。  将“小文件”保持选定状态。  选择“程序集”框旁边的文件夹图标。 找到并选择要上传的程序集。

   选择程序集后，“程序集名称”属性会自动显示该程序集的文件名。

1. 完成后，请选择“确定”。

   完成程序集文件上传后，该程序集将显示在“程序集”列表中。  在集成帐户“概述”窗格的“项目”下，也会显示上传的程序集 。

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>添加 2 MB 以上的程序集

若要添加更大的程序集，可将程序集上传到 Azure 存储帐户中的 Azure Blob 容器。 添加程序集的步骤因 Blob 容器是否具有公共读取访问权限而异。 因此，首先请执行以下步骤检查 Blob 容器是否具有公共读取访问权限：[为 Blob 容器设置公共访问级别](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>检查容器访问级别

1. 打开 Azure 存储资源管理器。 在“资源管理器”窗口中展开你的 Azure 订阅（如果尚未展开）。

1. 展开“存储帐户”>“{你的存储帐户}”>“Blob 容器”。    选择你的 Blob 容器。

1. 在 Blob 容器的快捷菜单中，选择“设置公共访问级别”。 

   * 如果 Blob 容器至少具有公共访问权限，请选择“取消”，并执行本页稍后所述的步骤：[上传到具有公共访问权限的容器](#public-access-assemblies)

     ![公共访问](media/logic-apps-enterprise-integration-maps/azure-blob-container-public-access.png)

   * 如果 Blob 容器没有公共访问权限，请选择“取消”，并执行本页稍后所述的步骤：[上传到没有公共访问权限的容器](#no-public-access-assemblies)

     ![没有公共访问权限](media/logic-apps-enterprise-integration-maps/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>上传到具有公共访问权限的容器

1. 将程序集上传到存储帐户。 在右侧窗口中，选择“上传”。

1. 完成上传后，选择上传的程序集。 在工具栏中，选择“复制 URL”以复制程序集的 URL。

1. 返回 Azure 门户，其中已打开“添加程序集”窗格。  输入程序集的名称。 选择“大文件(大于 2 MB)”。

   此时会显示“内容 URI”框而不是“程序集”框。  

1. 在“内容 URI”框中，粘贴程序集的 URL。  完成添加程序集。

   完成程序集上传后，该程序集将显示在“程序集”列表中。 在集成帐户“概述”窗格的“项目”下，也会显示上传的程序集 。

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>上传到没有公共访问权限的容器

1. 将程序集上传到存储帐户。 在右侧窗口中，选择“上传”。

1. 上传完成后，为程序集生成共享访问签名 (SAS)。 在程序集的快捷菜单中，选择“获取共享访问签名”。 

1. 在“共享访问签名”窗格中，选择“生成容器级共享访问签名 URI” > “创建”。    生成 SAS URL 后，选择“URL”框旁边的“复制” 。

1. 返回 Azure 门户，其中已打开“添加程序集”窗格。  输入程序集的名称。 选择“大文件(大于 2 MB)”。

   此时会显示“内容 URI”框而不是“程序集”框。  

1. 在“内容 URI”框中，粘贴前面生成的 SAS URI。 完成添加程序集。

完成程序集上传后，该程序集将显示在“程序集”列表中。 在集成帐户“概述”页的“项目”下，也会显示上传的程序集 。

<a name="create-maps"></a>

## <a name="create-maps"></a>创建映射

若要创建可用作映射的可扩展样式表语言转换 (XSLT) 文档，可以使用 Visual Studio 2015 或 2019 通过 [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas) 创建集成项目。 在此项目中，可以生成用于直观地在两个 XML 架构文件之间映射项的集成映射文件。 生成此项目后，会获得一个 XSLT 文档。 有关集成帐户中映射数量的限制，请查看 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md#artifact-number-limits)。

映射必须具有以下属性和 `CDATA` 节，该节包含对程序集代码的调用：

* `name` 是自定义程序集名称。

* `namespace` 是包含自定义代码的程序集中的命名空间。

以下示例演示了一个映射，该映射引用名为 `XslUtilitiesLib` 的程序集并从该程序集调用 `circumference` 方法。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
<msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
</msxsl:script>
<xsl:template match="data">
<circles>
    <xsl:for-each select="circle">
        <circle>
            <xsl:copy-of select="node()"/>
                <circumference>
                    <xsl:value-of select="user:circumference(radius)"/>
                </circumference>
        </circle>
    </xsl:for-each>
</circles>
</xsl:template>
</xsl:stylesheet>
```

## <a name="tools-and-capabilities-for-maps"></a>适用于映射的工具和功能

* 使用 Visual Studio 和[企业集成 SDK](https://aka.ms/vsmapsandschemas) 创建映射时，可以使用映射的图形表示形式，其中会显示你创建的所有关系和链接。

* 你可以在用于创建映射的 XML 架构之间直接进行数据复制。 Visual Studio 的[企业集成 SDK](https://aka.ms/vsmapsandschemas) 包含一个映射器，它能够使此任务变得非常简单，就如同绘制一条线将源 XML 架构中的元素与目标 XML 架构中的对应元素连接到一起一样。

* 针对多个映射的操作或函数已提供，包括字符串函数、日期时间函数等。

* 若要添加示例 XML 消息，可以使用映射测试功能。 只需一个手势，就能测试创建的映射并查看生成的输出。

<a name="add-map"></a>

## <a name="add-maps"></a>添加映射

### <a name="consumption"></a>[消耗](#tab/consumption)

上传映射引用的所有程序集之后，接下来可以上传映射。

1. 在 Azure 门户中，如果你的集成帐户尚未打开，请在 Azure 主搜索框中输入 `integration accounts`，然后选择“集成帐户”。

1. 选择要将映射添加到的集成帐户。

1. 在集成帐户的菜单上，选择“概述”。 在“设置”下选择“映射” 。

1. 在“映射”窗格工具栏中选择“添加” 。

1. 继续添加高达 [2 MB](#smaller-map) 或超过 [2 MB](#larger-map) 的映射。

<a name="smaller-map"></a>

#### <a name="add-maps-up-to-2-mb"></a>添加不超过 2 MB 的映射

1. 在“添加映射”窗格中，输入映射的唯一名称。

1. 在“映射类型”下选择类型，例如：  “Liquid”、“XSLT”、“XSLT 2.0”或“XSLT 3.0”。    

1. 选择“映射”框旁边的文件夹图标。 选择要上传的映射。

   如果将“名称”属性留空，则选择映射文件后，映射的文件名将自动显示在该属性中。

1. 完成后，请选择“确定”。

   完成映射文件上传后，该映射将显示在“映射”列表中。  在集成帐户“概述”页的“项目”下，也会显示上传的映射 。

<a name="larger-map"></a>

#### <a name="add-maps-more-than-2-mb"></a>添加 2 MB 以上的映射

目前，若要添加较大的映射，需要使用 [Azure 逻辑应用 REST API - 映射](/rest/api/logic/maps/createorupdate)。

### <a name="standard"></a>[标准](#tab/standard)

#### <a name="azure-portal"></a>Azure 门户

1. 在逻辑应用资源菜单中的“设置”下，选择“映射” 。

1. 在“映射”窗格工具栏中选择“添加” 。

1. 在“添加映射”窗格中，输入映射的唯一名称并包括 `.xslt` 扩展名。

1. 选择“映射”框旁边的文件夹图标。 选择要上传的映射。

1. 完成后，请选择“确定”。

   完成映射文件上传后，该映射将显示在“映射”列表中。  在集成帐户“概述”页的“项目”下，也会显示上传的映射 。

#### <a name="visual-studio-code"></a>Visual Studio Code

1. 在逻辑应用项目的结构中打开“项目”文件夹，然后打开“映射”文件夹 。

1. 在“映射”文件夹中添加映射。

---

<a name="edit-map"></a>

## <a name="edit-a-map"></a>编辑映射

若要更新现有的映射，必须上传一个新的映射文件，其中包含所要做出的更改。 但是，可以先下载现有的映射进行编辑。

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)中，打开你的集成帐户（如果尚未打开）。

1. 在集成帐户菜单上的“设置”下，选择“映射” 。

1. “映射”窗格打开后，请选择你的映射。 若要先下载并编辑映射，请在“映射”窗格工具栏上选择“下载”，然后保存映射 。

1. 准备好上传更新的映射时，请在“映射”窗格上选择要更新的映射。 在“映射”窗格工具栏中选择“更新” 。

1. 找到并选择要上传的已更新映射。

   完成映射文件上传后，更新的映射将显示在“映射”列表中。 

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)中打开你的逻辑应用资源（如果尚未打开）。

1. 在逻辑应用资源菜单中的“设置”下，选择“映射” 。

1. “映射”窗格打开后，请选择你的映射。 若要先下载并编辑映射，请在“映射”窗格工具栏上选择“下载”，然后保存映射 。

1. 在“映射”窗格工具栏中选择“添加” 。

1. 在“添加映射”下，输入映射的唯一名称并包括 `.xslt` 扩展名。

1. 选择“映射”框旁边的文件夹图标。 选择要上传的映射。

1. 完成后，请选择“确定”。

   完成映射文件上传后，更新的映射将显示在“映射”列表中。 

---

<a name="delete-map"></a>

## <a name="delete-a-map"></a>删除映射

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)中，打开你的集成帐户（如果尚未打开）。

1. 在集成帐户菜单上的“设置”下，选择“映射” 。

1. “映射”窗格打开后，请选择你的映射，然后选择“删除” 。

1. 若要确认删除该映射，请选择“是”。

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)中打开你的逻辑应用资源（如果尚未打开）。

1. 在逻辑应用资源菜单中的“设置”下，选择“映射” 。

1. “映射”窗格打开后，请选择你的映射，然后选择“删除” 。

1. 若要确认删除该映射，请选择“是”。

---

## <a name="next-steps"></a>后续步骤

* [在 Azure 逻辑应用中转换工作流的 XML](logic-apps-enterprise-integration-transform.md)
* [在 Azure 逻辑应用中验证工作流的 XML](logic-apps-enterprise-integration-xml-validation.md)
