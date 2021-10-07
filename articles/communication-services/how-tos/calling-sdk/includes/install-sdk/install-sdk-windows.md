---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 023f3413fbd7b642300de19ad479029c94e7c4d5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698906"
---
## <a name="setting-up"></a>设置

### <a name="creating-the-visual-studio-project"></a>创建 Visual Studio 项目

在 Visual Studio 2019 中创建一个新的 `Blank App (Universal Windows)` 项目。 输入项目名称后，可随意选取版本高于 `10.0.17134` 的 Windows SDK。 

### <a name="install-the-package-and-dependencies-with-nuget-package-manager"></a>使用 NuGet 包管理器安装包和依赖项

可以通过 NuGet 包公开提供通话 SDK API 和库。
以下步骤将举例说明如何查找、下载和安装通话 SDK NuGet 包。

1. 打开 NuGet 包管理器 (`Tools` -> `NuGet Package Manager` -> `Manage NuGet Packages for Solution`)
2. 单击 `Browse`，然后在搜索框中键入 `Azure.Communication.Calling`。
3. 确保选中 `Include prerelease` 复选框。
4. 单击 `Azure.Communication.Calling` 包。
5. 选中与右侧选项卡上的 CS 项目对应的复选框。
6. 单击“”`Install`按钮。