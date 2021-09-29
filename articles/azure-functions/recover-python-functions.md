---
title: 在 Azure Functions 中排查 Python 函数应用错误
description: 了解如何对 Python 函数进行错误排查。
ms.topic: article
ms.date: 07/29/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3d6be539763e655405a1e46b628e68cb5690e4f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654340"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>在 Azure Functions 中排查 Python 错误

下面列出了 Python 函数常见问题的错误排查指南：

* [ModuleNotFoundError 和 ImportError](#troubleshoot-modulenotfounderror)
* [无法导入“cygrpc”](#troubleshoot-cannot-import-cygrpc)
* [Python 已退出，代码为 137](#troubleshoot-python-exited-with-code-137)
* [Python 已退出，代码为 139](#troubleshoot-python-exited-with-code-139)

## <a name="troubleshoot-modulenotfounderror"></a>ModuleNotFoundError 错误排查

本部分可帮助你排查 Python 函数应用中与模块相关的错误。 这些错误通常会导致以下 Azure Functions 错误消息：

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

当 Python 函数应用加载 Python 模块失败时，会发生此错误。 此错误的根本原因是以下问题之一：

* [找不到该包](#the-package-cant-be-found)
* [未使用正确的 Linux wheel 解析该包](#the-package-isnt-resolved-with-proper-linux-wheel)
* [该包与 Python 解释器版本不兼容](#the-package-is-incompatible-with-the-python-interpreter-version)
* [该包与其他包冲突](#the-package-conflicts-with-other-packages)
* [该包仅支持 Windows 或 macOS 平台](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>查看项目文件

为了确定问题的真正原因，你需要获取在函数应用上运行的 Python 项目文件。 如果本地计算机上没有这些项目文件，可以通过以下方式之一获取它们：

* 如果函数应用具有 `WEBSITE_RUN_FROM_PACKAGE` 应用设置，并且其值是一个 URL，请通过将该 URL 复制并粘贴到浏览器来下载文件。
* 如果函数应用具有 `WEBSITE_RUN_FROM_PACKAGE` 并且它设置为 `1`，请导航至 `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages`，并从最新的 `href` URL 下载文件。
* 如果函数应用没有上述应用设置，请导航至 `https://<app-name>.scm.azurewebsites.net/api/settings`，并在 `SCM_RUN_FROM_PACKAGE` 下找到 URL。 通过将该 URL 复制并粘贴到浏览器来下载文件。
* 如果这些都不适用，请导航至 `https://<app-name>.scm.azurewebsites.net/DebugConsole`，并显示 `/home/site/wwwroot` 下的内容。

本文的其余部分通过检查函数应用的内容、确定根本原因并解决特定问题，来帮助你排查此错误的潜在原因。

### <a name="diagnose-modulenotfounderror"></a>诊断 ModuleNotFoundError

本部分详细介绍与模块相关的错误的潜在根本原因。 找出可能的根本原因后，可以转到相关的缓解措施。

#### <a name="the-package-cant-be-found"></a>找不到该包

浏览到 `.python_packages/lib/python3.6/site-packages/<package-name>` 或 `.python_packages/lib/site-packages/<package-name>`。 如果文件路径不存在，这个缺失的路径可能就是根本原因。

在部署过程中使用第三方或过时的工具可能会导致此问题。

有关缓解措施，请参阅[启用远程生成](#enable-remote-build)或[生成本机依赖项](#build-native-dependencies)。

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>未使用正确的 Linux wheel 解析该包

转到 `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` 或 `.python_packages/lib/site-packages/<package-name>-<version>-dist-info`。 使用你喜欢的文本编辑器打开 wheel 文件并检查 Tag: 部分。 如果标记的值不包含 linux，这可能就是问题所在。

Python 函数仅在 Azure 中的 Linux 上运行：Functions 运行时 v2.x 在 Debian Stretch 上运行，v3.x 运行时在 Debian Buster 上运行。 该项目应包含正确的 Linux 二进制文件。 在 Core Tools、第三方或过时的工具中使用 `--build local` 标志可能会导致使用较旧的二进制文件。

有关缓解措施，请参阅[启用远程生成](#enable-remote-build)或[生成本机依赖项](#build-native-dependencies)。

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>该包与 Python 解释器版本不兼容

转到 `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` 或 `.python_packages/lib/site-packages/<package-name>-<version>-dist-info`。 使用文本编辑器打开 METADATA 文件并检查 Classifiers: 部分。 如果该部分不包含 `Python :: 3`、`Python :: 3.6`、`Python :: 3.7` 或 `Python :: 3.8`，则表示包版本太旧，或者该包很可能已停止维护。

可以从 [Azure 门户](https://portal.azure.com)检查函数应用的 Python 版本。 导航到你的函数应用，选择“资源浏览器”，然后选择“前往”。

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="在门户中打开函数应用的资源浏览器":::

加载资源浏览器后，搜索“LinuxFxVersion”，其中显示了 Python 版本。

有关缓解措施，请参阅[将包更新到最新版本](#update-your-package-to-the-latest-version)或[用等效的包代替](#replace-the-package-with-equivalents)。

#### <a name="the-package-conflicts-with-other-packages"></a>该包与其他包冲突

如果已验证使用适当的 Linux wheel 正确解析了该包，则可能是该包与其他包冲突。 在某些包中，PyPi 文档可能会阐明不兼容的模块。 例如，在 [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) 中，有一条如下所示的声明：

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

可以在 `https://pypi.org/project/<package-name>/<package-version>` 中找到包版本的相应文档。

有关缓解措施，请参阅[将包更新到最新版本](#update-your-package-to-the-latest-version)或[用等效的包代替](#replace-the-package-with-equivalents)。

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>该包仅支持 Windows 或 macOS 平台

使用文本编辑器打开 `requirements.txt`，并在 `https://pypi.org/project/<package-name>` 中检查该包。 有些包仅在 Windows 或 macOS 平台上运行。 例如，pywin32 仅在 Windows 上运行。

使用 Windows 或 macOS 进行本地开发时，可能不会发生 `Module Not Found` 错误。 但是，在 Azure Functions 上无法导入该包，因为 Azure Functions 在运行时使用 Linux。 这很可能是由于在项目初始化期间使用 `pip freeze` 从 Windows 或 macOS 计算机将虚拟环境导出到 requirements.txt 引起的。

有关缓解措施，请参阅[用等效的包代替](#replace-the-package-with-equivalents)或[手动创建 requirements.txt](#handcraft-requirementstxt)。

### <a name="mitigate-modulenotfounderror"></a>缓解 ModuleNotFoundError

下面是与模块相关的问题的潜在缓解措施。 使用[上面的诊断](#diagnose-modulenotfounderror)确定要尝试哪种缓解措施。

#### <a name="enable-remote-build"></a>启用远程生成

确保启用了远程生成。 执行此操作的方式取决于你的部署方法。

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
确保已安装[适用于 Visual Studio Code 的 Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)的最新版本。 确保 `.vscode/settings.json` 存在并包含设置 `"azureFunctions.scmDoBuildDuringDeployment": true`。 如果不是这样，请在启用 `azureFunctions.scmDoBuildDuringDeployment` 设置的情况下创建此文件，然后重新部署项目。

## <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

确保已安装 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) 的最新版本。 转到本地函数项目文件夹，并使用 `func azure functionapp publish <app-name>` 进行部署。

## <a name="manual-publishing"></a>[手动发布](#tab/manual)

如果要手动将包发布到 `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` 终结点，请确保将 SCM_DO_BUILD_DURING_DEPLOYMENT 和 ENABLE_ORYX_BUILD 都设置为 true。 若要了解详细信息，请参阅[如何使用应用程序设置](functions-how-to-use-azure-function-app-settings.md#settings)。

---

#### <a name="build-native-dependencies"></a>生成本机依赖项

确保已安装 docker 和 [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) 的最新版本。 转到本地函数项目文件夹，并使用 `func azure functionapp publish <app-name> --build-native-deps` 进行部署。

#### <a name="update-your-package-to-the-latest-version"></a>将包更新到最新版本

浏览 `https://pypi.org/project/<package-name>` 中的最新包版本，并检查 Classifiers: 部分。 包应为 `OS Independent` 或与操作系统中的 `POSIX` 或 `POSIX :: Linux` 兼容。 另外，编程语言应包含 `Python :: 3`、`Python :: 3.6`、`Python :: 3.7` 或 `Python :: 3.8`。

如果这些都是正确的，则可以通过更改 requirements.txt 中的行 `<package-name>~=<latest-version>`，将包更新为最新版本。

#### <a name="handcraft-requirementstxt"></a>手动创建 requirements.txt

一些开发人员使用 `pip freeze > requirements.txt` 为其开发环境生成 Python 包列表。 尽管这种便捷做法在大多数情况下都是可行的，但在跨平台部署方案中可能会出现问题，例如在 Windows 或 macOS 本地开发函数，但发布到在 Linux 上运行的函数应用。 在此方案中，`pip freeze` 可能会引入特定于操作系统的意外依赖项或本地开发环境的依赖项。 在 Linux 上运行时，这些依赖项可能会中断 Python 函数应用。

最佳做法是在项目源代码中检查每个 .py 文件的导入语句，并且仅在 requirements.txt 文件中签入这些模块。 这将确保可以在不同的操作系统上正确处理包的解析。

#### <a name="replace-the-package-with-equivalents"></a>用等效的包代替

首先，应在 `https://pypi.org/project/<package-name>` 中查看包的最新版本。 通常，此包有自己的 GitHub 页面，请转到 GitHub 上的“Issues”部分，并搜索你的问题是否已解决。 如果已解决，请将该包更新到最新版本。

有时，该包可能已集成到 [Python 标准库](https://docs.python.org/3/library/)（例如 pathlib）中。 如果是这样，由于我们在 Azure Functions 中提供了特定的 Python 分发版（Python 3.6、Python 3.7 和 Python 3.8），因此应在 requirements.txt 中删除该包。

但是，如果你遇到的问题尚未解决，但又必须按时完成项目。 建议你做些研究，为项目找到一个类似的包。 通常，Python 社区将提供各种类似的库供你使用。

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>错误排查无法导入“cygrpc”

本文可帮助你排查 Python 函数应用中与“cygrpc”相关的错误。 这些错误通常会导致以下 Azure Functions 错误消息：

> `Cannot import name 'cygrpc' from 'grpc._cython'`

如果 Python 函数应用无法在相应的 Python 解释器上启动，会发生此错误。 此错误的根本原因是以下问题之一：

- [Python 解释器与操作系统体系结构不匹配](#the-python-interpreter-mismatches-os-architecture)
- [Azure Functions Python 辅助角色不支持 Python 解释器](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>诊断“cygrpc”引用错误

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>Python 解释器与操作系统体系结构不匹配

这很可能是因为 32 位 Python 解释器安装在 64 位操作系统上。

如果在 x64 操作系统上运行，请确保 Python 3.6、3.7 或 3.8 解释器也安装在 64 位版本上。

可以通过以下命令检查 Python 解释器位数：

在 Windows PowerShell 上：`py -c 'import platform; print(platform.architecture()[0])'`

在类 Unix 的 shell 上：`python3 -c 'import platform; print(platform.architecture()[0])'`

如果 Python 解释器位数和操作系统体系结构不匹配，请从 [Python Software Foundation](https://www.python.org/downloads) 下载相应的 Python 解释器。

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>Azure Functions Python 辅助角色不支持 Python 解释器

Azure Functions Python 辅助角色仅支持 Python 3.6、3.7 和 3.8。
请检查 Python 解释器是否与 Windows 中的 `py --version` 或类 Unix 系统中的 `python3 --version` 的预期版本相匹配。 确保返回结果为 Python 3.6.x、Python 3.7.x 或 Python 3.8.x。

如果 Python 解释器版本与预期版本不匹配，请从 [Python Software Foundation](https://www.python.org/downloads) 下载 Python 3.6、3.7 或 3.8 解释器。

---

## <a name="troubleshoot-python-exited-with-code-137"></a>对“Python 已退出，代码为 137”进行故障排除

发生代码 137 错误的原因通常是 Python 函数应用中存在内存不足的问题。 因此，你会收到以下 Azure Functions 错误消息：

> `Microsoft.Azure.WebJobs.Script.Workers.WorkerProcessExitException : python exited with code 137`

当一个 Python 函数应用被操作系统用 SIGKILL 信号强制终止时，会发生此错误。 此信号通常指示 Python 进程中出现内存不足错误。 Azure Functions 平台有一个[服务限制](functions-scale.md#service-limits)，它会终止任何超出此限制的函数应用。

请访问 [Python 函数的内存分析](python-memory-profiler-reference.md#memory-profiling-process)中的教程部分，以分析函数应用中的内存瓶颈。

---

## <a name="troubleshoot-python-exited-with-code-139"></a>对“Python 已退出，代码为 139”进行故障排除

本部分可帮助你对 Python 函数应用中的分段错误进行故障排除。 这些错误通常会导致以下 Azure Functions 错误消息：

> `Microsoft.Azure.WebJobs.Script.Workers.WorkerProcessExitException : python exited with code 139`

当一个 Python 函数应用被操作系统用 SIGSEGV 信号强制终止时，会发生此错误。 此信号指示存在内存分段冲突，可能是因为意外地从受限的内存区域执行了读出或写入操作。 在以下各节中，我们将提供常见根本原因的列表。

### <a name="a-regression-from-third-party-packages"></a>来自第三方包的回归

在函数应用的 requirements.txt 中，取消固定的包将会在每次 Azure Functions 部署中升级到最新版本。 这些包的供应商可能会在最新版本中引入回归。 若要修复此问题，请尝试在 requirements.txt 中注释掉导入语句、禁用包引用或将包固定到之前的版本。

### <a name="unpickling-from-a-malformed-pkl-file"></a>从格式错误的 .pkl 文件取消封装

如果函数应用使用 Python pickel 库从 .pkl 文件加载 Python 对象，那么 .pkl 有可能在其中包含格式错误的字节字符串或无效的地址引用。 若要修复此问题，请尝试注释掉 pickle.load() 函数。

### <a name="pyodbc-connection-collision"></a>Pyodbc 连接冲突

如果函数应用使用热门的 ODBC 数据库驱动程序 [pyodbc](https://github.com/mkleehammer/pyodbc)，则有可能在单个函数应用中打开多个连接。 若要避免此问题，请使用单一实例模式，并确保函数应用中仅使用一个 pyodbc 连接。

---

## <a name="next-steps"></a>后续步骤

如果无法解决问题，请向 Functions 团队报告：

> [!div class="nextstepaction"]
> [报告未解决的问题](https://github.com/Azure/azure-functions-python-worker/issues)
