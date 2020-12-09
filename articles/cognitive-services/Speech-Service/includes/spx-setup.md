---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: da88b8554d6c3214da9a386613538c237a318f73
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546891"
---
## <a name="download-and-install"></a>下载并安装

#### <a name="windows-install"></a>[Windows 安装](#tab/windowsinstall)

按照以下步骤在 Windows 上安装语音 CLI：

1. 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 首次安装时，可能需要重启。
2. 下载语音 CLI [zip 存档](https://aka.ms/speech/spx-zips.zip)然后提取它。
3. 转到提取 `spx-zips` 的目录。 此文件夹包含适用于各种平台上的语音 CLI 的程序文件。 
4. 为平台提取文件（为 .NET Framework 4.7 提取 `spx-net471`，或为 x64 CPU 上的 .NET Core 3.0 提取 `spx-netcore-win-x64`）。 请记住，将从此目录运行 `spx`。

### <a name="run-the-speech-cli"></a>运行语音 CLI

1. 打开命令提示符或 PowerShell，然后导航到在其中提取了语音 CLI 的目录。  
2. 若要查看语音 CLI 的帮助命令，请键入 `spx`。

> [!NOTE]
> 查找命令时，Powershell 不会检查本地目录。 在 Powershell 中，将目录更改为 `spx` 的位置，并通过输入 `.\spx` 调用工具。
> 如果将此目录添加到路径，则 Powershell 和 Windows 命令提示符会从不包含 `.\` 前缀的任何目录中查找 `spx`。

### <a name="font-limitations"></a>字体限制

在 Windows 上，语音 CLI 只能显示本地计算机上命令提示符适用的字体。
[Windows 终端](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701)支持通过语音 CLI 以交互方式生成的所有字体。

如果输出到文件，文本编辑器（例如记事本）或 web 浏览器（例如 Microsoft Edge）也可以显示所有字体。

#### <a name="linux-install"></a>[Linux 安装](#tab/linuxinstall)

按照以下步骤在 x64 CPU 上安装 Linux 上的语音 CLI：

1. 安装 [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)。
2. 下载语音 CLI [zip 存档](https://aka.ms/speech/spx-zips.zip)然后提取它。
3. 转到从下载中提取的根目录 `spx-zips`，并将 `spx-netcore-30-linux-x64` 提取到新 `~/spx` 目录。
4. 在终端中，键入以下命令：
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

若要查看语音 CLI 的帮助，请键入 `spx`。

#### <a name="docker-install-windows-linux-macos"></a>[Docker 安装（Windows、Linux、macOS）](#tab/dockerinstall)

按照以下步骤在 Docker 容器中安装语音 CLI：

1. 为你的平台<a href="https://www.docker.com/get-started" target="_blank">安装 Docker Desktop<span class="docon docon-navigate-external x-hidden-focus"></span></a>（如果尚未安装）。
2. 在新的命令提示符或终端中，键入以下命令：
   ```shell   
   docker pull msftspeech/spx
   ```
3. 键入此命令。 你应看到语音 CLI 的帮助信息：
   ```shell 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>在容器中装载目录

语音 CLI 工具会将配置设置保存为文件，并在执行任何命令时加载这些文件（帮助命令除外）。
在 Docker 容器中使用语音 CLI 时，必须从容器装载本地目录，以便该工具可以存储或查找配置设置，并且还可以读取或写入命令所需的任何文件，例如语音音频文件。

在 Windows 上，键入以下命令以创建一个语音 CLI 可以在容器内使用的本地目录：

`mkdir c:\spx-data`

或者在 Linux 或 macOS 上，在终端中键入以下命令以创建目录并查看其绝对路径：

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

调用语音 CLI 时，将使用绝对路径。

### <a name="run-speech-cli-in-the-container"></a>在容器中运行语音 CLI

本文档显示了在非 Docker 安装中使用的语音 CLI `spx` 命令。
在 Docker 容器中调用 `spx` 命令时，必须将容器中的目录装载到文件系统中，语音 CLI 可在其中存储和查找配置值以及读取和写入文件。

在 Windows 上，命令会以下方的方式开始：

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

在 Linux 或 macOS 上，命令会以下方的方式启动：
```shell   
sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx
```

> [!NOTE]
> 将 `/ABSOLUTE_PATH` 替换为上一节中 `pwd` 命令显示的绝对路径。

若要使用安装在容器中的 `spx` 命令，请始终输入上面所示的完整命令，然后输入请求的参数。
例如，在 Windows 上，此命令将设置密钥：

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

> [!WARNING]
> 在 Docker 容器中运行语音 CLI 时，不能使用计算机的麦克风。 但你可以在本地装载的目录中读取和保存音频文件。 

### <a name="optional-create-a-command-line-shortcut"></a>可选：创建命令行快捷方式

如果是在 Linux 或 macOS 上的 Docker 容器中运行语音 CLI，则可以创建快捷方式。 

请按照以下说明创建一个快捷方式：
1. 在你喜欢的文本编辑器中打开 `.bash_profile`。 例如：
   ```shell
   nano ~/.bash_profile
   ```
2. 然后，将此函数添加到 `.bash_profile`。 请确保使用已装载目录的正确路径更新此函数：
   ```shell   
   spx(){
       sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. 寻找配置文件的来源：
   ```shell
   source ~/.bash_profile
   ```
4. 现在，只需键入 `spx`，然后再键入参数即可，而不是运行 `sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`。 例如： 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> 如果更改了 Docker 引用的已装载目录，则需要在 `.bash_profile`中更新函数。

***

## <a name="create-subscription-config"></a>创建订阅配置

若要开始使用语音 CLI，需要输入语音订阅密钥和区域标识符。 按照[免费试用语音服务](../overview.md#try-the-speech-service-for-free)中的步骤获取这些凭据。
获得订阅密钥和区域标识符后（例如 `eastus` 和 `westus`），运行以下命令。

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

现在会存储订阅身份验证，用于将来的 SPX 请求。 如果需要删除这些已存储值中的任何一个，请运行 `spx config @region --clear` 或 `spx config @key --clear`。
