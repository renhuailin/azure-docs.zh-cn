---
title: 在 Azure 存储中托管静态网站
description: 了解如何直接从 Azure 存储 GPv2 帐户中的容器提供静态内容（HTML、CSS、JavaScript 和图像文件）。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurepowershell
ms.openlocfilehash: 46c22fe3033a9fc9cda90bf9901f10f20355838e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645885"
---
# <a name="host-a-static-website-in-azure-storage"></a>在 Azure 存储中托管静态网站

可以直接从[常规用途 V2](../common/storage-account-create.md) 或 [ BlockBlobStorage](../common/storage-account-create.md) 帐户中的容器提供静态内容（HTML、CSS、JavaScript 和图像文件）。 若要了解详细信息，请参阅 [Azure 存储中的静态网站托管](storage-blob-static-website.md)。

本文介绍如何使用 Azure 门户、Azure CLI 或 PowerShell 启用静态网站托管。

## <a name="enable-static-website-hosting"></a>启用静态网站托管

静态网站托管是必须在存储帐户上启用的一项功能。

### <a name="portal"></a>[门户](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com/)即可开始操作。

2. 找到存储帐户并显示帐户概览。

3. 选择“静态网站”，以显示静态网站的配置页。

4. 选择“启用”，启用针对存储帐户的静态网站托管功能。

5. 在“索引文档名称”字段中，指定一个默认索引页（例如：*index.html*）。

   当用户导航到静态网站的根目录时，会显示默认索引页。

6. 在“错误文档路径”字段中，指定一个默认错误页（例如：*404.html*）。

   当用户尝试导航到静态网站中不存在的页面时，会显示默认错误页。

7. 单击“ **保存**”。 Azure 门户现在会显示静态网站终结点。

    ![启用针对存储帐户的静态网站托管功能](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

可以使用 [Azure 命令行接口 (CLI)](/cli/azure/) 来启用静态网站托管。

1. 首先，打开 [Azure Cloud Shell](../../cloud-shell/overview.md)，或者，如果已在本地[安装](/cli/azure/install-azure-cli) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

3. 启用静态网站托管。

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   - 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

   - 将 `<error-document-name>` 占位符替换为当浏览器请求站点上不存在的页面时向用户显示的错误文档的名称。

   - 将 `<index-document-name>` 占位符替换为索引文档的名称。 该文档通常是“index.html”。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

可以使用 Azure PowerShell 模块启用静态网站托管。

1. 打开 Windows PowerShell 命令窗口。

2. 验证你是否具有 Azure PowerShell 模块 Az 版本 0.7 或更高版本。

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。

3. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
   Connect-AzAccount
   ```

4. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

5. 获取定义了要使用的存储帐户的存储帐户上下文。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   - 将 `<resource-group-name>` 占位符值替换为资源组的名称。

   - 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

6. 启用静态网站托管。

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   - 将 `<error-document-name>` 占位符替换为当浏览器请求站点上不存在的页面时向用户显示的错误文档的名称。

   - 将 `<index-document-name>` 占位符替换为索引文档的名称。 该文档通常是“index.html”。

---

## <a name="upload-files"></a>上传文件

### <a name="portal"></a>[门户](#tab/azure-portal)

这些说明介绍如何使用 Azure 门户中出现的存储资源管理器版本来上传文件。 不过，也可以使用在 Azure 门户之外运行的[存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)版本。 可以使用 [AzCopy](../common/storage-use-azcopy-v10.md)、PowerShell、CLI 或可将文件上传到帐户的 $web 容器的任何自定义应用程序。 有关使用 Visual Studio Code 上传文件的分步教程，请参阅[教程：在 Blob 存储上托管静态网站](./storage-blob-static-website-host.md)。

1. 选择“存储资源管理器（预览版）”。

2. 展开“Blob 容器”节点，然后选择 $web 容器。

3. 选择“上传”按钮来上传文件。

   ![上传文件](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. 如果希望浏览器显示文件的内容，请确保将该文件的内容类型设置为 `text/html`。

   ![检查内容类型](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   > [!NOTE]
   > 对于通常可识别的扩展名（例如 `.html`），存储资源管理器会自动将此属性设置为 `text/html`。 但是，在某些情况下，你必须自行设置此项。 如果你没有将此属性设置为 `text/html`，则浏览器将提示用户下载文件而不是呈现内容。 若要设置此属性，请右键单击文件，然后单击“属性”。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

将对象从源目录上传到 $web 容器。

此示例假设从 Azure Cloud Shell 会话中运行命令。

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE]
> 如果浏览器提示用户下载文件而不是呈现内容，则可以将 `--content-type 'text/html; charset=utf-8'` 追加到命令中。

- 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

- 将 `<source-path>` 占位符替换为要上传的文件所在位置的路径。

> [!NOTE]
> 如果使用的是 Azure CLI 的位置安装，则可以使用本地计算机上的任何位置的路径（例如：`C:\myFolder`）。
>
> 如果使用 Azure Cloud Shell，则必须引用对 Cloud Shell 可见的文件共享。 此位置可以是云共享本身的文件共享，也可以是从 Cloud Shell 装载的现有文件共享。 若要了解如何执行此操作，请参阅[在 Azure Cloud Shell 中持久保存文件](../../cloud-shell/persisting-shell-storage.md)。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

将对象从源目录上传到 $web 容器。

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE]
> 如果浏览器提示用户下载文件而不是呈现内容，则可以将 `-Properties @{ ContentType = "text/html; charset=utf-8";}` 追加到命令。

- 将 `<path-to-file>` 占位符值替换为要上传的文件的完全限定的路径（例如：`C:\temp\index.html`）。

- 将 `<blob-name>` 占位符值替换为要为生成的 blob 提供的名称（例如：`index.html`）。

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>查找网站 URL

可以使用网站的公共 URL 从浏览器查看网站的页面。

### <a name="portal"></a>[Portal](#tab/azure-portal)

在存储帐户的“帐户概述”页旁显示的窗格中，选择“静态网站”。 你的站点的 URL 显示在“主终结点”字段中。

![Azure 存储静态网站指标 - 指标](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下命令查找静态网站的公共 URL：

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

- 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

- 将 `<resource-group-name>` 占位符值替换为资源组的名称。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用以下命令查找静态网站的公共 URL：

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

- 将 `<resource-group-name>` 占位符值替换为资源组的名称。

- 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>在静态网站页面上启用指标

启用指标后，指标仪表板会报告有关 $web 容器中文件的流量统计信息。

1. 单击存储帐户菜单的“监视器”部分下的“指标”。

   > [!div class="mx-imgBorder"]
   > ![指标链接](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > 将通过挂接到不同的指标 API 来生成指标数据。 门户只会显示在给定时间范围内使用的 API 成员，以便重点关注可返回数据的成员。 为确保能够选择所需的 API 成员，第一步是展开期限。

2. 单击“期限”按钮，选择一个期限，然后单击“应用”。

   ![Azure 存储静态网站指标 - 时间范围](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. 从“命名空间”下拉列表中选择“Blob”。

   ![Azure 存储静态网站指标 - 命名空间](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 然后选择“传出”指标。

   ![显示 Azure 存储静态网站出口指标的屏幕截图。](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. 从“聚合”选择器中选择“总和”。

   ![Azure 存储静态网站指标 - 聚合](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. 单击“添加筛选器”按钮，并从“属性”选择器中选择“API 名称”。 

   ![Azure 存储静态网站指标 - API 名称](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. 在“值”选择器中选中“GetWebContent”旁边的复选框，以填充指标报告。

   ![Azure 存储静态网站指标 - GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   > [!NOTE]
   > 只有在给定的期限内使用该 API 成员时，才会显示 GetWebContent 复选框。 门户只会显示在给定时间范围内使用的 API 成员，以便重点关注可返回数据的成员。 如果在此列表中找不到特定的 API 成员，请展开期限。

## <a name="next-steps"></a>后续步骤

- 了解如何为静态网站配置自定义域。 请参阅[将自定义域映射到 Azure Blob 存储终结点](storage-custom-domain-name.md)。
