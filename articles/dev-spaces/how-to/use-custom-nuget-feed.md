---
title: 使用自定义 NuGet 源
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: 使用自定义 NuGet 源访问和使用 Azure Dev Space 中的 NuGet 包。
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
manager: gwallace
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960212"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>结合使用自定义 NuGet 源和 Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

NuGet 源提供了一种方便方式将包源包含在项目中。 Azure Dev Spaces 需要能够访问此源，以便将依赖项正确安装到 Docker 容器中。

## <a name="set-up-a-nuget-feed"></a>设置 NuGet 源

在 `PackageReference` 节点下的 `*.csproj` 文件中为依赖项添加[包引用](/nuget/consume-packages/package-references-in-project-files)。 例如：

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

在项目文件夹中创建一个 [NuGet.Config](/nuget/reference/nuget-config-file) 文件，并针对 NuGet 源设置 `packageSources` 和 `packageSourceCredentials` 部分。 `packageSources` 部分包含源 url，必须能够从 AKS 群集访问。 `packageSourceCredentials` 是访问该源的凭据。 例如：

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

更新 Dockerfile 以将 `NuGet.Config` 文件复制到映像。 例如：

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> 在 Windows 上，`NuGet.Config`、`Nuget.Config` 和 `nuget.config` 都可以作为有效的文件名。 在 Linux 上，只有 `NuGet.Config` 是此文件的有效文件名。 由于 Azure Dev Spaces 使用 Docker 和 Linux，因此必须将此文件命名为 `NuGet.Config`。 可以手动或通过运行 `dotnet restore --configfile nuget.config` 来修复命名。


如果使用 Git，不应将 NuGet 源的凭据放到版本控制中。 将 `NuGet.Config` 添加到项目的 `.gitignore` 中，这样 `NuGet.Config` 文件则不会被添加到版本控制中。 Azure Dev Spaces 在容器映像生成过程中将需要此文件，但默认情况下，它会考虑执行同步期间 `.gitignore` 和 `.dockerignore` 中定义的规则。 要更改默认规则并允许 Azure Dev Spaces 同步 `NuGet.Config` 文件，请更新 `azds.yaml` 文件：

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

如果未使用 Git，请跳过此步骤。

下次在 Visual Studio Code 或 Visual Studio 中运行或 `azds up` 或点击 `F5` 时，Azure Dev Spaces 将同步 `NuGet.Config` 文件，使用它来安装包依赖项。

## <a name="next-steps"></a>后续步骤

详细了解 [NuGet 及其工作原理](/nuget/what-is-nuget)。
