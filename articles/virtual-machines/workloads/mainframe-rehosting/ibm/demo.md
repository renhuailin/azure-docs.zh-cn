---
title: 在 IBM zD&T v1 中设置应用程序开发人员控制的分发 (ADCD) | Microsoft Docs
description: 在 Azure 虚拟机 (VM) 上运行 IBM Z 开发和测试环境 (zD&T)。
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: b1391f81c571879534258c289436d2d086b84bef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045937"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>在 IBM zD&T v1 中设置应用程序开发人员控制的分发 (ADCD)

可以在 Azure 虚拟机 (VM) 上运行 IBM Z 开发和测试环境 (zD&T)。 此环境模拟 IBM Z 系列体系结构。 它可以托管各种 Z 系列操作系统或安装（也称为 Z 实例或包）。可以通过称作“IBM 应用程序开发人员控制的分发 (ADCD)”的自定义捆绑包获取此环境。

本文介绍如何在 Azure 上的 zD&T 环境中设置 ADCD 实例。 ADCD 为在 zD&T 中运行的开发和测试环境创建完整的 Z 系列操作系统实施方案。

与 zD&T 一样，ADCD 仅供 IBM 客户和合作伙伴使用，且专用于开发和测试目的。 它们不适用于生产环境。 有大量的 IBM 安装包可通过 [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) 或 [IBM PartnerWorld](https://www.ibm.com/partnerworld/public) 下载。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 已事先在 Azure 上设置了 [zD&T 环境][ibm-install-z]。 本文假设使用的是以前创建的同一 Ubuntu 16.04 VM 映像。

- 通过 IBM PartnerWorld 或 Passport Advantage 访问 ADCD 媒体。

- 一个[授权服务器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 必须使用此服务器来运行 IBM zD&T。 该服务器的创建方式取决于 IBM 软件的授权方式：

  - 基于硬件的授权服务器需要一个 USB 硬件设备，其中包含访问软件的所有部分所必需的 Rational 令牌。 必须从 IBM 获得它。

  - 基于软件的授权服务器要求设置集中式服务器来管理授权密钥。 这是首选的方法，要求在管理服务器中设置从 IBM 收到的密钥。

## <a name="download-the-installation-packages-from-passport-advantage"></a>通过 Passport Advantage 下载安装包

必须能够访问 ADCD 媒体。 以下步骤假设你是 IBM 客户，并可以使用 Passport Advantage。 IBM 合作伙伴可以使用 [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)。

> [!NOTE]
> 本文假设使用一台 Windows 电脑来访问 Azure 门户并下载 IBM 媒体。 如果使用 Mac 或 Ubuntu 桌面版，用于获取 IBM 媒体的命令和过程可能略有不同。

1. 登录到 [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)。

2. 选择“Software Downloads”（软件下载）和“Media Access”（媒体访问）。 

3. 选择“Program offering and agreement number”（程序产品/服务和协议编号），然后单击“Continue”（继续）。 

4. 输入部件说明或部件号，然后单击“Finder”（查找器）。

5. （可选）单击按字母顺序排序的列表，以按名称显示并查看产品。

6. 在“Operating system”（操作系统）字段中选择“All Operating Systems”（所有操作系统），在“Languages”（语言）字段中选择“All Languages”（所有语言）。    然后单击“Go”（前往）。

7. 单击“Select individual files”（选择单个文件）以展开列表并显示要下载的单个媒体。

8. 核对要下载的包，选择“Download”（下载），然后将文件下载到所需的目录。

## <a name="upload-the-adcd-packages"></a>上传 ADCD 包

获取包后，必须将其上传到 Azure 中的 VM。

1. 在 Azure 门户中，与创建的 Ubuntu VM 发起 **ssh** 会话。 转到你的 VM，选择“概述”边栏选项卡，然后选择“连接”。 

2. 选择“SSH”选项卡，然后将 ssh 命令复制到剪贴板。

3. 使用凭据和所选的 [SSH 客户端](../../../linux/use-remote-desktop.md)登录到 VM。 本演示使用适用于 Windows 10 的 Linux 扩展，该扩展会将 bash shell 添加到 Windows 命令提示符。 也可以使用 PuTTY。

4. 登录后，创建要将 IBM 包上传到的目录。 请记住，Linux 区分大小写。 例如，本演示假设要将包上传到：

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. 使用 [WinSCP](https://winscp.net/eng/index.php) 等 SSH 客户端上传文件。 由于 SCP 是 SSH 的一部分，因此它使用了端口 22（SSH 使用此端口）。 如果本地计算机不是 Windows，可以在 SSH 会话中键入 [scp 命令](http://man7.org/linux/man-pages/man1/scp.1.html)。

6. 开始上传到创建的 Azure VM 目录，该目录将成为 zD&T 的映像存储。

    > [!NOTE]
    > 确保上传到 **home/MyUserID/ZDT/adcd/nov2017** 目录的文件包括 **ADCDTOOLS.XML**。 稍后需要用到此值。

7. 等待文件完成上传，这可能需要一段时间，具体取决于与 Azure 之间的连接状况。

8. 上传完成后，导航到 volumes 目录并解压缩所有 **gz** 卷：

    ```console
    gunzip \*.gz
    ```
    
![显示已解压缩的 gz 卷的文件资源管理器](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>配置映像存储

下一步是将 zD&T 配置为使用上传的包。 zD&T 中的映像存储进程让你可以装载和使用映像。 它可以使用 SSH 或 FTP。

1. 启动 **zDTServer**。 必须在根级别执行此操作。 按顺序输入以下两个命令：
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. 记下命令的 URL 输出，并使用此 URL 来访问 Web 服务器。 此 URL 类似于：
     > https://(你的 VM 名称或 IP 地址):9443/ZDTMC/index.html
     >
     > 请记住，你的 Web 访问使用端口 9443。 使用此端口登录到 Web 服务器。 ZD&T 的用户 ID 是 **zdtadmin**，密码是 **password**。

    ![IBM zD&T Enterprise Edition 的“Welcome”（欢迎）屏幕](media/02-welcome.png)

3. 在“Quick Start”（快速开始）页上的“Configure”（配置）下，选择“Image Storage”（映像存储）。  

     ![IBM zD&T Enterprise Edition 的“Quick Start”（快速开始）屏幕](media/03-quickstart.png)

4. 在“Configure image storage”（配置映像存储）页上，选择“SSH File Transfer Protocol”（SSH 文件传输协议）。 

5. 对于“Host name”（主机名），请键入 **Localhost**，然后输入映像上传到的目录路径。 例如 /home/MyUserID/ZDT/adcd/nov2017/volumes。

6. 输入 VM 的“User ID”（用户 ID）和“password”（密码）。  请不要使用 ZD&T 用户 ID 和密码。

7. 测试连接以确保能够访问，然后选择“Save”（保存）以保存配置。

## <a name="configure-the-target-environments"></a>配置目标环境

下一步是配置 zD&T 目标环境。 此模拟托管环境就是运行映像的位置。

1. 在“Quick Start”（快速开始）页上的“Configure”（配置）下，选择“Target environments”（目标环境）。  

2. 在“Configure target environments”（配置目标环境）页上选择“Add Target”（添加目标）。 

3. 选择“Linux”。 IBM 支持两种类型的环境：Linux 和云 (OpenStack)，但本演示在 Linux 上运行。

4. 在“Add target environment”（添加目标环境）页上，为“Host name”（主机名）输入 **localhost**。  将“SSH port”（SSH 端口）设置为 **22**。

5. 在“Target Environment label”（目标环境标签）框中输入一个标签，例如 **MyCICS**。

     ![“Add target environment”（添加目标环境）屏幕](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>配置 ADCD 并部署

完成上述配置步骤后，必须将 zD&T 配置为使用包和目标环境。 同样，请使用 zD&T 中的映像存储进程，以便能够装载和使用映像。 它可以使用 SSH 或 FTP。

1. 在“Quick Start”（快速开始）页上的“Configure”（配置）下，选择“ADCD”。   此时会显示一系列说明，告知需要完成哪些步骤才能装载 ADCD 包。 这也解释了我们为何按前面所述的方式命名了目标目录。

2. 假设所有映像已上传到正确的目录，请单击右下方显示的“IMAGE from ADCD”（ADCD 中的映像）链接（如以下屏幕截图中的步骤 7 所示）。

     ![IBM zD&T Enterprise Edition -“Configure ADCD”（配置 ADCD）屏幕](media/05-adcd.png)

## <a name="create-the-image"></a>创建映像

上一配置步骤完成后，将显示“Create an image using ADCD Components”（使用 ADCD 组件创建映像）页。

1. 选择卷（在本例中为 Nov 2017）以显示该卷中的不同包。

2. 对于本演示，请选择“Customer Information Control System (CICS) - 5.3”（客户信息控制系统 (CICS) -5.3）。

3. 在“Image name”（映像名称）框中键入映像的名称，例如 **MyCICS image**。

4. 选择右下角的“Create Image”（创建映像）按钮。

     ![IBM zD&T Enterprise Edition -“Create an image using ADCD Components”（使用 ADCD 组件创建映像）屏幕](media/06-adcd.png)

5. 在出现的窗口（告知已成功部署了映像）中，选择“Deploy images”（部署映像）。

6. 在“Deploy an image to a target environment”（将映像部署到目标环境）页上，选择在前一页上创建的映像 (**MyCICS Image**) 以及前面创建的目标环境 (**MyCICS**)。

7. 在下一个屏幕上，提供 VM 的凭据（不是 ztadmin 凭据）。

8. 在“Properties”（属性）窗格中，输入“Central processors (CPs)”（中央处理器）的数量、“System memory (GB)”（系统内存）量 (GB)，以及正在运行的映像的“Deployment directory”（部署目录）。   由于这只是一个演示，因此请保持较小的值。

9. 确保已选中“Automatically issue IPL command to z/OS after deploy”（在部署后自动向 z/OS 发出 IPL 命令）对应的框。

     ![“Properties”（属性）屏幕](media/07-properties.png)

10. 选择“Complete”（完成）。

11. 在“Deploy an image to a target environment”（将映像部署到目标环境）页中选择“Deploy Image”（部署映像）。 

映像现在可以部署，并已准备好供 3270 终端仿真器装载。

> [!NOTE]
> 如果出现一条指出没有足够磁盘空间的错误，请注意该区域需要 151 Gb。

祝贺你！ 你目前正在 Azure 上运行 IBM 大型机环境。

## <a name="learn-more"></a>了解更多

- [大型机迁移：虚构和事实](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure 上的 IBM DB2 pureScale](ibm-db2-purescale-azure.md)
- [故障排除](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [揭秘大型机到 Azure 的迁移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md