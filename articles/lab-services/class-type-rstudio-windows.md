---
title: 使用 Azure 实验室服务在 Windows 上使用 R 和 RStudio 设置实验室
description: 了解如何在 Windows 上使用 RStudio 设置实验室来讲授 R
author: emaher
ms.topic: article
ms.date: 08/26/2021
ms.author: enewman
ms.openlocfilehash: 5d081e21723fa73d84beec12e8602234ad5e8074
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757375"
---
# <a name="set-up-a-lab-to-teach-r-on-windows"></a>在 Windows 上设置实验室来讲授 R

[R](https://www.r-project.org/about.html) 是一种用于统计计算和图形的开源语言。  它被运用于遗传学统计分析、自然语言处理和金融数据数据分析等领域。  R 提供了一种[交互式命令行](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line)体验。  [RStudio](https://www.rstudio.com/products/rstudio/) 是可用于 R 语言的交互式开发环境 (IDE)。  免费版提供代码编辑工具、集成调试体验和包开发工具。

本文仅重点介绍 RStudio 和 R，两者是需要使用统计计算的课程的基础。  [深度学习](class-type-deep-learning-natural-language-processing.md)以及 [Python 和 Jupyter Notebook](class-type-jupyter-notebook.md) 课程类型通过不同的方式设置 RStudio。  每篇文章都介绍了如何使用[适用于 Linux (Ubuntu) 的 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) 市场映像，该映像预先安装了许多[与数据科学相关的工具](../machine-learning/data-science-virtual-machine/tools-included.md)，包括 RStudio。  

## <a name="lab-account-configuration"></a>实验室帐户配置

若要设置此实验室，需要有 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅有关[如何设置实验室帐户](./tutorial-setup-lab-account.md)的教程。 也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

启用实验室帐户设置，如下表所述。

| 实验室帐户设置 | Instructions |
| -------------------- | ----- |
| [启用对等虚拟网络](how-to-connect-peer-virtual-network.md) | 在以下情况下启用该设置：<ul><li>课程需要共享 R Server。</li><li>课程需要你想要存储在外部（而不是学生 VM）上的大型数据文件。</li></ul> |

> [!IMPORTANT]
> 如果选择启用对等虚拟网络，则必须在创建实验室之前完成此操作。

## <a name="lab-configuration"></a>实验室配置

有关创建新实验室和应用所需设置的说明，请参阅[教程：设置课堂实验室](tutorial-setup-classroom-lab.md)。  创建实验室时，请应用以下设置：

| 实验室设置 | 值和描述 |
| ------------ | ------------------ |
| 虚拟机大小 | 小型 GPU (计算)|
| VM 映像 | Windows 10 专业版 版本 2004 |

## <a name="external-resource-configuration"></a>外部资源配置

某些课程需要将文件（例如大型数据文件）存储在外部。  有关选项和设置说明，请参阅[在 Azure 实验室服务中使用外部文件存储](how-to-attach-external-storage.md)。

如果选择让学生拥有共享 RStudio 服务器，则应在创建实验室之前设置服务器。  有关如何设置共享服务器的详细信息，请参阅[如何在 Azure 实验室服务中创建包含共享资源的实验室](how-to-create-a-lab-with-shared-resource.md)。  有关创建 RStudio 服务器的说明，请参阅[下载适用于 Debian 和 Ubuntu 的 RStudio 服务器](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/)和[访问 RStudio 服务器开源代码](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started)。

## <a name="template-configuration"></a>模板配置

在创建模板计算机后，启动并连接到该计算机，以[安装 R](https://docs.rstudio.com/resources/install-r/) 和 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。  

### <a name="install-r"></a>安装 R

1. 下载[适用于 Windows 的 R 的最新安装程序](https://cran.r-project.org/bin/windows/base/release.html)。  有关可用版本的完整列表，请参阅[适用于 Windows 的 R 下载页面](https://cran.r-project.org/bin/windows/base/)。
2. 运行安装程序。
    1. 对于“选择设置语言”提示，选择所需的语言，然后选择“确定” 
    2. 在安装程序的“信息”页上，阅读许可协议。  选择“下一步”，接受协议并继续。
    3. 在“选择目标位置”页面上，接受默认安装位置，然后选择“下一步” 。
    4. 在“选择组件”页面上，选择性地取消选中“32 位文件”选项 。  有关同时运行 32 位和 62 位版本的 R 的详细信息，请参阅[可以将 32 位和 64 位的 R 安装在同一台计算机上吗？](https://cran.r-project.org/bin/windows/base/rw-FAQ.html#Can-both-32_002d-and-64_002dbit-R-be-installed-on-the-same-machine_003f)常见问题解答。
    5. 在“启动选项”页面上，将启动选项设置为“否(接受默认值)” 。  如果希望 R 图形用户界面 (GUI) 使用单独的窗口 (SDI) 或纯文本帮助，请选择“是(自定义启动)”单选按钮，并将以下启动选项更改为向导页面。
    6. 在“选择开始菜单文件夹”页上，选择“下一步”。
    7. 在“选择其他任务”页面上，选择“创建桌面快捷方式”（可选） 。  选择“**下一页**”。
    8. 在“正在安装”页上，等待安装完成。
    9. 在“完成适用于 Windows 的 R”页面上，选择“完成” 。

也可使用 PowerShell 来安装 R。  此代码示例显示如何在没有 32 位组件的情况下安装 R 并为最新版本的 R 添加桌面图标。若要查看安装程序命令行选项的完整列表，请参阅[设置命令行参数](https://jrsoftware.org/ishelp/index.php?topic=setupcmdline)。

```powershell
#Avoid prompt to setup Internet Explorer if we must parse download page
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Internet Explorer\Main" -Name "DisableFirstRunCustomize" -Value 2

$outputfile = "R-win.exe"

$result = Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/release.html" -OutFile $outputfile -PassThru

#Check if we need to parse the result ourselves, to find the latest version of R
if ($result.StatusCode -eq '200' -and $result.Headers["Content-Type"] -eq 'text/html')
{
    $metaTag = $result.ParsedHtml.Head.children | Where-Object {$_.nodeName -eq 'META'}
    if ($metaTag.content  -match "R-\d+\.\d+\.\d+-win.exe"){
        $outputfile = $Matches.0

        #Download latest version
        Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/$outputfile" -OutFile $outputfile
    }else{
        Write-Error "Unable to find latest version of R installer.  Go to https://cran.r-project.org/bin/windows/base/release.html to download manually."
    }
}

#Install Silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/VERYSILENT /LOG=r-install.log /NORESTART /COMPONENTS=""main,x64,translations"" /MERGETASKS=""desktopicon"" /LANG=""en""" -NoNewWindow -Wait
```

### <a name="install-rstudio"></a>安装 RStudio

现在已在本地安装 R，接下来可以安装 RStudio IDE。  我们将安装免费版本的 RStudio Desktop。  有关所有可用版本，请参阅 [RStudio 下载](https://www.rstudio.com/products/rstudio/download/)。

1. 下载适用于 Windows 10 的 [R Studio 安装程序](https://www.rstudio.com/products/rstudio/download/#download)。  安装程序文件的格式为 `rstudio-{version}.exe`。  
2. 运行 RStudio 安装程序。
    1. 在“RStudio 设置”向导的“欢迎使用 RStudio 设置”页面上，选择“下一步”  。
    2. 在“选择安装位置”页面上，选择“下一步” 。
    3. 在“选择开始菜单文件夹”页面上，选择“安装” 。
    4. 在“正在安装”页上，等待安装完成。
    5. 在“完成 RStudio 设置”页面上，选择“完成” 。

若要使用 PowerShell 执行 RStudio 安装步骤，请运行以下命令。  在执行命令之前，请参阅 [RStudio 下载](https://www.rstudio.com/products/rstudio/download/)以验证 RStudio 版本是否可用。

```powershell
$rstudiover="1.4.1717"
$outputfile = "RStudio-$rstudiover.exe"

#Download installer executable
Invoke-WebRequest "https://download1.rstudio.org/desktop/windows/RStudio-$rstudiover.exe" -OutFile $outputfile

#Install RStudio silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/S" -NoNewWindow -Wait
```

### <a name="cran-packages"></a>CRAN 包

在 R 交互式会话中使用 `install.packages(“package name”)` 命令，如[有用的 R 包快速列表](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages)一文中所示。  也可使用 RStudio 中的“工具”->“安装包”菜单项。

如果在查找包时需要帮助，请参阅[按任务列出的包列表](https://cran.r-project.org/web/views/)或[按字母顺序排列的包列表](https://cloud.r-project.org/web/packages/available_packages_by_name.html)。

## <a name="cost"></a>成本

接下来介绍此课程的示例成本估算。  假设你的班上有 25 名学生。 每名学生都有 20 小时的计划课程时间。  除计划的课堂时间外，每名学生还有另外 10 小时的配额，用于完成家庭作业或分配给他们的任务。  我们选择的虚拟机大小是“小型 GPU (计算)”，即 139 个实验室单位。

25 名学生 &times;（计划的 20 小时 + 配额的 10 小时）&times; 139 个实验室单位 &times; 每小时 0.01 美元 = 1042.5 美元

> [!IMPORTANT]
> 成本估算仅用于示例。  有关最新定价信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>后续步骤

现在可以将模板映像发布到实验室。 有关进一步的说明，请参阅[发布模板 VM](how-to-create-manage-template.md#publish-the-template-vm)。

设置实验室时，请参阅以下文章：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)