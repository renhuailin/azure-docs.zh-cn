---
title: 适用于 Windows 的 Azure N 系列 AMD GPU 驱动程序安装
description: 如何为 Azure 中运行 Windows Server 或 Windows 的 N 系列 VM 安装 AMD GPU 驱动程序
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: ec4f09249bda14eb29d7dc9704a6096848a78557
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122194985"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在运行 Windows 的 N 系列 VM 上安装 AMD GPU 驱动程序

若要利用运行 Windows 的全新 Azure NVv4 系列 VM 的 GPU 功能，必须安装 AMD GPU 驱动程序。 [AMD GPU 驱动程序扩展](../extensions/hpccompute-amd-gpu-windows.md)会在 NVv4 系列 VM 上安装 AMD GPU 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的操作系统和部署步骤，请参阅 [AMD GPU 驱动程序扩展文档](../extensions/hpccompute-amd-gpu-windows.md)。

如果选择手动安装 AMD GPU 驱动程序，请参阅本文，其中提供了受支持的操作系统、驱动程序以及安装和验证步骤。

NVv4 VM 仅支持 Microsoft 发布的 GPU 驱动程序。 请勿从任何其他源安装 GPU 驱动程序。

有关基本规范、存储容量和磁盘详细信息，请参阅 [GPU Windows VM 大小](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

| OS | 驱动程序 |
| -------- |------------- |
| Windows 10 - 版本 2009、2004、1909 <br/><br/>Windows 10 企业版多会话 - 版本 2009、2004、1909 <br/><br/>Windows Server 2016（版本 1607）<br/><br/>Windows Server 2019（版本 1909） | [21.Q2](https://download.microsoft.com/download/3/4/8/3481cf8d-1706-49b0-aa09-08c9468305ab/AMD-Azure-NVv4-Windows-Driver-21Q2.exe) (.exe) |

Windows 版本（1909 以前的版本）以前支持的驱动程序版本为 [20.Q4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exe) (.exe)

 > [!NOTE]
   >  如果使用版本 1903/1909，可能需要更新以下组策略才能获得最佳性能。 任何其他的 Windows 版本都不需要这些更改。
   >  
   >  [计算机配置->策略->Windows 设置->管理模板->Windows 组件->远程桌面服务->远程桌面会话主机->远程会话环境]，将策略 [使用 WDDM 图形显示驱动程序进行远程桌面连接] 设置为“禁用”。
   >  

 
## <a name="driver-installation"></a>驱动程序安装

1. 通过远程桌面连接到每个 NVv4 系列 VM。

2. 如果需要卸载以前的驱动程序版本，请下载 [AMD 清理实用工具](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)。请不要使用旧版驱动程序随附的实用工具。

3. 下载并安装最新版驱动程序。

4. 重启 VM。

## <a name="verify-driver-installation"></a>验证驱动程序安装

可以在设备管理器中验证驱动程序安装。 以下示例展示了如何在 Azure NVv4 VM 上成功配置 Radeon Instinct MI25 卡。
<br />

![此屏幕截图展示了如何在 Azure NVv4 VM 上成功配置 Radeon Instinct MI25 卡。](./media/n-series-amd-driver-setup/device-manager.png)

可以使用 dxdiag 验证 GPU 显示属性（包括视频 RAM）。 以下示例显示了 Azure NVv4 VM 上 Radeon Instinct MI25 卡的 1/2 分区。
<br />
![此屏幕截图显示了 Azure NVv4 VM 上 Radeon Instinct MI25 卡的 1/2 分区。](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

如果运行的是 Windows 10 内部版本 1903 或更高版本，则 dxdiag 不会在“显示”选项卡中显示任何信息。请使用底部的“保存所有信息”选项，输出文件会显示与 AMD MI25 GPU 相关的信息。

![GPU 驱动程序属性](./media/n-series-amd-driver-setup/dxdiag-details.png)
