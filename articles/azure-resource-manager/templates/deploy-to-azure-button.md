---
title: “部署到 Azure”按钮
description: 使用此按钮从 GitHub 存储库部署 Azure 资源管理器模板。
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: e25d49571347bb5ed27dbd52bb60c68cbeb4360d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543766"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>使用部署按钮从 GitHub 存储库部署模板

本文介绍了如何使用“部署到 Azure”按钮从 GitHub 存储库部署模板。 可直接将此按钮添加到 GitHub 存储库中的 README.md 文件， 也可将其添加到引用该存储库的网页中。

部署范围由模板架构确定。 有关详细信息，请参阅：

- [resource groups](deploy-to-resource-group.md)
- [subscriptions](deploy-to-subscription.md)
- [管理组](deploy-to-management-group.md)
- [tenants](deploy-to-tenant.md)

## <a name="use-common-image"></a>使用常用图像

若要将此按钮添加到网页或存储库，请使用以下图像：

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

此图像显示为：

![“部署到 Azure”按钮](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>创建用于部署模板的 URL

若要为模板创建 URL，请从存储库中模板的原始 URL 开始。 若要查看原始 URL，请选择“原始”。

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="选择“原始”":::

URL 的格式为：

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

然后，将 URL 转换为 URL 编码的值。 可以使用联机编码器，也可以运行一个命令。 以下 PowerShell 示例展示了如何对值进行 URL 编码。

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

进行 URL 编码后，示例 URL 具有以下值。

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

每个链接都以相同的基 URL 开头：

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

将进行 URL 编码后的模板链接添加到基 URL 的末尾。

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

你已具有该链接的完整 URL。

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

如果使用[包含 Azure Repos 的 Git](/azure/devops/repos/git/) 而不是 GitHub 存储库，那么仍可使用“部署到 Azure”按钮。 请确保存储库是公共的。 使用[项操作](/rest/api/azure/devops/git/items/get)获取模板。 请求应采用以下格式：

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

对此请求 URL 进行编码。

## <a name="create-deploy-to-azure-button"></a>创建“部署到 Azure”按钮

最后，将链接和图像放在一起。

若要向 GitHub 存储库中的 README.md 文件或者向网页中添加带 Markdown 的按钮，请使用：

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

对于 HTML，请使用：

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

对于包含 Azure 存储库的 Git，该按钮的格式为：

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>部署模板

若要测试整个解决方案，请选择以下按钮：

[![“部署到 Azure”](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

门户会显示一个窗格，你可以在其中轻松地提供参数值。 这些参数预先填充了来自模板的默认值。

![使用门户进行部署](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>后续步骤

- 若要详细了解模板，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
