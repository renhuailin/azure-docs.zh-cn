---
title: 通过 Azure 实验室服务在 Linux 上使用 R 和 RStudio 设置实验室
description: 了解如何在 Linux 上使用 RStudio 设置实验室来讲授 R
author: emaher
ms.topic: article
ms.date: 08/25/2021
ms.author: enewman
ms.openlocfilehash: 14e30572ab620b36730c725c7ac5a2e762947366
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755497"
---
# <a name="set-up-a-lab-to-teach-r-on-linux"></a>在 Linux 上设置实验室来讲授 R

[R](https://www.r-project.org/about.html) 是一种用于统计计算和图形的开源语言。  它被运用于遗传学统计分析、自然语言处理和金融数据数据分析等领域。  R 提供了一种[交互式命令行](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line)体验。  [RStudio](https://www.rstudio.com/products/rstudio/) 是可用于 R 语言的交互式开发环境 (IDE)。  免费版提供代码编辑工具、集成调试体验和包开发工具。

本文仅重点介绍 RStudio 和 R，两者是需要使用统计计算的课程的基础。  [深度学习](class-type-deep-learning-natural-language-processing.md)以及 [Python 和 Jupyter Notebook](class-type-jupyter-notebook.md) 课程类型通过不同的方式设置 RStudio。  每篇文章都介绍了如何使用[适用于 Linux (Ubuntu) 的 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) 市场映像，该映像预先安装了许多[与数据科学相关的工具](../machine-learning/data-science-virtual-machine/tools-included.md)，包括 RStudio。  

## <a name="lab-account-configuration"></a>实验室帐户配置

若要设置此实验室，需要有 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅有关[如何设置实验室帐户](./tutorial-setup-lab-account.md)的教程。 也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

启用实验室帐户设置，如下表所述。 若要详细了解如何启用 Azure 市场映像，请参阅[指定可供实验室创建者使用的 Azure 市场映像](./specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| -------------------- | ----- |
| 市场映像 | Ubuntu Server 18.04 LTS |
| [启用对等虚拟网络](how-to-connect-peer-virtual-network.md) | 在以下情况下启用该设置：<ul><li>课程需要共享 R Server。</li><li>课程需要你想要存储在外部（而不是学生 VM）上的大型数据文件。</li></ul> |

> [!IMPORTANT]
> 如果选择启用对等虚拟网络，则必须在创建实验室之前完成此操作。

## <a name="lab-configuration"></a>实验室配置

有关创建新实验室和应用所需设置的说明，请参阅[教程：设置课堂实验室](tutorial-setup-classroom-lab.md)。  创建实验室时，请应用以下设置：

| 实验室设置 | 值和描述 |
| ------------ | ------------------ |
| 虚拟机大小 | 小型 GPU (计算)|
| VM 映像 | Ubuntu Server 18.04 LTS |
| 启用远程桌面连接 | 选择使用 RDP 时，应启用此设置。  如果选择使用 [X2Go 连接到实验室计算机](how-to-use-remote-desktop-linux-student.md)，则不需要此设置。  首次连接到 Linux VM 时需要使用 SSH，并安装 RDP/X2Go 和 GUI 包。  有关详细信息，请参阅[启用适用于 Linux VM 的图形远程桌面](how-to-enable-remote-desktop-linux.md)。 |

## <a name="external-resource-configuration"></a>外部资源配置

某些课程需要将文件（例如大型数据文件）存储在外部。  有关选项和设置说明，请参阅[在 Azure 实验室服务中使用外部文件存储](how-to-attach-external-storage.md)。

如果选择让学生拥有共享 R Server，则应在创建实验室之前设置服务器。  若要详细了解如何设置共享服务器，请参阅[如何在 Azure 实验室服务中创建具有共享资源的实验室](how-to-create-a-lab-with-shared-resource.md)。  有关创建 RStudio 服务器的说明，请参阅[下载适用于 Debian 和 Ubuntu 的 RStudio 服务器](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/)和[访问 RStudio 服务器开源代码](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started)。

## <a name="template-configuration"></a>模板配置

在创建模板计算机后，启动计算机并连接到它，以[安装 R](https://docs.rstudio.com/resources/install-r/) 和 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)，并根据需要安装 [X2Go 服务器](https://wiki.x2go.org/doku.php/doc:installation:x2goserver)。  

首先，更新 apt 并升级计算机上的现有包。

```bash
sudo apt update 
sudo apt upgrade
```

### <a name="install-x2go-server"></a>安装 X2Go 服务器

如果选择使用 X2Go，请[安装服务器](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop#install-x2go-server)。  首先需要[使用 SSH 进行连接](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh)来安装服务器组件。  完成后，可在[使用 X2Go 客户端进行连接](how-to-use-remote-desktop-linux-student.md)之后完成其余设置。

X2Go 的默认安装与 RStudio 不兼容。  若要解决此问题，请更新 x2goagent 选项文件。

1. 编辑 `/etc/x2go/x2goagent.options` 文件。  别忘了将文件编辑为 sudo。
    1. 取消注释行：`X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
    1. 注释行：`X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
2. 重启 X2Go 服务器以使用新选项。

    ```bash
    sudo systemctl restart x2goserver
    ```

或者，可按照[适用于 X2Go 的 Glx Xlib 解决方法](https://wiki.x2go.org/doku.php/wiki:development:glx-xlib-workaround)中的说明生成所需的库。

### <a name="install-r"></a>安装 R

可通过多种方式在 VM 上安装 R。  接下来将通过 Comprehensive R Archive Network (CRAN) 安装 R。  它提供最新版本的 R。将此存储库添加到计算机后，可安装 R 和许多其他相关包。

需要添加 CRAN 存储库。 命令是根据[适用于 R 的 Ubuntu 包的简要说明](https://cran.rstudio.com/bin/linux/ubuntu/)一文中的说明修改的。

```bash
#download helper packages
sudo apt install --no-install-recommends software-properties-common dirmngr
# download and add the signing key (by Michael Rutter) for these repos
sudo wget -q "https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc" -O /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
#add repository
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/"
```

现在可运行以下命令来安装 R：

```bash
sudo apt install r-base
```

### <a name="install-rstudio"></a>安装 RStudio

现在已在本地安装 R，接下来可以安装 RStudio IDE。  我们将安装免费版本的 RStudio Desktop。  有关所有可用版本，请参阅 [RStudio 下载](https://www.rstudio.com/products/rstudio/download/)。

1. 为 RStudio [导入代码签名密钥](https://www.rstudio.com/code-signing/)。

    ```bash
    sudo gpg --keyserver keyserver.ubuntu.com  --recv-keys 3F32EE77E331692F
    ```

2. 下载适用于 Ubuntu 的 [R Studio 的 Debian Linux 软件包文件 (.deb)](https://www.rstudio.com/products/rstudio/download/#download)。  文件将采用 `rstudio-{version}-amd64.deb` 格式。  例如：

    ```bash
    export rstudiover="1.4.1717"
    wget --quiet -O rstudio.deb https://download1.rstudio.org/desktop/bionic/amd64/rstudio-$rstudiover-amd64.deb
    ```

3. 使用 gdebi 安装 RStudio。   确保使用文件路径向 apt 指示正在安装本地文件。

    ```bash
    sudo apt install gdebi-core 
    echo "y" | gdebi rstudio.deb –quiet
    ```

### <a name="cran-packages"></a>CRAN 包

现在可安装所需的任何 [CRAN 包](https://cloud.r-project.org/web/packages/available_packages_by_name.html)。  首先，添加[当前 R 4.0 或更高版本的“c2d4u”存储库](https://cran.rstudio.com/bin/linux/ubuntu/#get-5000-cran-packages)。

```bash
sudo add-apt-repository ppa:c2d4u.team/c2d4u4.0+
```

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