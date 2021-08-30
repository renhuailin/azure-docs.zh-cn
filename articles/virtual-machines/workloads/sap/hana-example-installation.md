---
title: 在 Azure SAP HANA（大型实例）上安装 HANA | Microsoft Docs
description: 了解如何在 Azure SAP HANA（大型实例）上安装 HANA。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/4/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e4e7aec0422d3dfe5772748215c350073155a30
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559781"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>在 Azure SAP HANA（大型实例）上安装 HANA

本文逐步介绍如何在 Azure SAP HANA 大型实例（也称为“ BareMetal 基础结构”）上安装 HANA。

## <a name="prerequisites"></a>先决条件

若要在 Azure SAP HANA（大型实例）上安装 HANA，首先请做好以下准备：

- 向 Microsoft 提供要在 SAP HANA 大型实例上部署的所有数据。
- 从 Microsoft 接收 SAP HANA 大型实例。
- 创建连接到本地网络的 Azure 虚拟网络。
- 将 HANA 大型实例的 ExpressRoute 线路连接到同一 Azure 虚拟网络。
- 安装用作 HANA 大型实例 Jump Box 的 Azure 虚拟机。
- 确保可以从 Jump Box 连接到 HANA 大型实例，反之亦然。
- 检查是否安装了所有必要的包和补丁。
- 阅读有关在要使用的操作系统上安装 HANA 的 SAP 说明和文档。 确保操作系统版本支持所选的 HANA 版本。

## <a name="download-the-sap-hana-installation-bits"></a>下载 SAP HANA 安装软件

现在，让我们将 HANA 安装包下载到 Jump Box 虚拟机。 在此示例中，操作系统是 Windows。

HANA 大型实例单元不会直接连接 Internet。 无法直接将安装包从 SAP 下载到 HANA 大型实例虚拟机。 应该将包下载到跳转盒虚拟机。

需要以 SAP S 用户或其他用户的身份访问 SAP市场。

1. 登录并转到 [SAP 服务市场](https://support.sap.com/en/index.html)。 选择“下载软件” > “安装和升级” > “按字母顺序排列的索引”。   然后选择“H 下 - SAP HANA 平台版本” > “SAP HANA 平台版 2.0” > “安装”。   下载以下屏幕截图中所示的文件。

   ![要下载的 HANA 安装文件的屏幕截图。](./media/hana-installation/image16_download_hana.PNG)

2. 在本示例中，我们下载了 SAP HANA 2.0 安装包。 在 Azure 跳转盒虚拟机中，将自解压存档展开到目录中，如下所示。

   ![自解压存档的屏幕截图。](./media/hana-installation/image17_extract_hana.PNG)

3. 解压缩存档后，复制解压缩程序创建的目录（在本例中为 51052030）。 将该目录从 HANA 大型实例单元的 /hana/shared 卷复制到创建的目录。

   > [!Important]
   > 请不要将安装包复制到根或启动 LUN 中。 空间是有限的，并且还需要供其他进程使用。


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>在 HANA 大型实例单元上安装 SAP HANA

1. 若要安装 SAP HANA，请以 root 用户身份登录。 只有当根拥有足够权限时，才能安装 SAP HANA。 设置对复制到 /hana/shared 的目录的权限。

    ```
    chmod –R 744 <Installation bits folder>
    ```
    
    若要使用图形用户界面安装程序安装 SAP HANA，需要在 HANA 大型实例上安装 gtk2 包。 若要检查是否已安装此包，请运行以下命令：
    
    ```
    rpm –qa | grep gtk2
    ```

    （在后续步骤中，我们将演示如何使用图形用户界面安装 SAP HANA。）         

2. 转到安装目录，并导航到子目录 HDB_LCM_LINUX_X86_64。 

    从该目录中启动：
    
    ```
    ./hdblcmgui 
    ```
3. 现在会依次打开一系列屏幕，你将在其中提供安装数据。 在此示例中，我们将安装 SAP HANA 数据库服务器和 SAP HANA 客户端组件。 因此我们选择了“SAP HANA 数据库”。

    ![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中已选择“SAP HANA 数据库”。](./media/hana-installation/image18_hana_selection.PNG)

4. 选择“安装新系统”。

    ![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中已选择“安装新系统”。](./media/hana-installation/image19_select_new.PNG)

5. 在可安装的其他多个组件中进行选择。

    ![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了附加组件的列表](./media/hana-installation/image20_select_components.PNG)

6. 选择“SAP HANA 客户端”和“SAP HANA Studio”。 另外，安装一个纵向扩展实例。 然后选择“单主机系统”。 

    ![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中已选择“单主机系统”。](./media/hana-installation/image21_single_host.PNG)

7. 接下来提供一些数据。 对于安装路径，请使用 /hana/shared 目录。

    ![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了要定义的系统属性字段。](./media/hana-installation/image22_provide_sid.PNG)

    > [!Important]
    > 提供的 HANA 系统 ID (SID) 必须与在订购 HANA 大型实例部署时向 Microsoft 提供的数据一致。 选择不同的 SID 会导致安装失败，原因是不同卷带来的访问权限问题。

8. 提供 HANA 数据文件和 HANA 日志文件的位置。

    ![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了数据和日志区域字段](./media/hana-installation/image23_provide_log.PNG)

    > [!Note]
    > 定义系统属性（前两个屏幕）时指定的 SID 应与装入点的 SID 相匹配。 如果不匹配，请返回并将 SID 调整为装入点上的值。

9. 检查主机名并根据需要予以更正。 

    ![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了主机名。](./media/hana-installation/image24_review_host_name.PNG)

10. 检索在订购 HANA 大型实例部署时向 Microsoft 提供的数据。 

    ![“SAP HANA 生命周期管理”的屏幕截图，其中显示了要定义的系统管理员字段](./media/hana-installation/image25_provide_guid.PNG)

    > [!Important]
    > 提供你在订购单元部署时向 Microsoft 提供的“系统管理员用户 ID”和“用户组 ID” 。 否则，无法在 HANA 大型实例单元上安装 SAP HANA。

11. 此处未显示接下来的两个屏幕。 在这两个屏幕中可以提供 SAP HANA 数据库的 SYSTEM 用户密码，以及 sapadm 用户密码。 后一个密码用于作为 SAP HANA 数据库实例的一部分安装的 SAP 主机代理。

    定义密码后，会看到确认屏幕。 检查列出的所有数据，并继续安装。 你将进入显示安装进度的屏幕，如下所示：

    ![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中显示了安装进度指示器。](./media/hana-installation/image27_show_progress.PNG)

12. 安装完成后，应会看到如下所示的屏幕：

    ![“SAP HANA 生命周期管理”屏幕的屏幕截图，其中指示安装已完成。](./media/hana-installation/image28_install_finished.PNG)

    现在，SAP HANA 实例应已正常运行，并可供使用。 可以从 SAP HANA Studio 连接到该实例。 请务必检查并应用最新的更新。


## <a name="next-steps"></a>后续步骤

了解 Azure 上的 SAP HANA 大型实例高可用性和灾难恢复。

> [!div class="nextstepaction"]
> [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)
