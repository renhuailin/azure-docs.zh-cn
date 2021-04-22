---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 8ac70027f7483fbf0131c31a5ba3631ed1d4ff9a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079733"
---
## <a name="download-and-install-the-osm-client-binary"></a>下载并安装 OSM 客户端库

在 Windows 上基于 PowerShell 的 shell 中，使用 `Invoke-WebRequest` 下载 Istio 发行版，然后使用 `Expand-Archive` 进行解压缩，如下所示：

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.8.2"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

`osm` 客户端二进制文件在客户端计算机上运行，这使你可以在 AKS 群集中管理 OSM 控制器。 在 Windows 上基于 PowerShell 的 shell 中，使用以下命令安装 OSM `osm` 客户端二进制文件。 这些命令可将 `osm` 客户端二进制文件复制到 OSM 文件夹，然后你可以通过 `PATH` 使其立即可用（在当前 shell 中）或永久可用（跨 shell 重启）。 不需要提升的（管理员）特权即可运行这些命令，不需重启 shell。

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
