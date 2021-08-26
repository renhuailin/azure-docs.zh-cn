---
title: 排查 Azure Migrate 设备诊断问题
description: 获取相关帮助信息来诊断和排查 Azure Migrate 设备可能会发生的问题。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.openlocfilehash: 03be4d6a4b5afb15b5820c1c8b334e3fcb80bbbb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182682"
---
# <a name="diagnose-and-solve-issues-with-azure-migrate-appliance"></a>诊断并解决 Azure Migrate 设备的问题

Azure Migrate 设备上的“诊断并解决”功能可帮助用户查明并自我评估设备配置中可能会阻止启动发现的问题，或者查明并自我评估设备中正在进行的发现、评估和/或复制等 Migrate 操作的问题（如果使用的是 VMware 设备）。 

你随时都可以从设备配置管理器运行“诊断并解决”以生成诊断报告。 该报告提供的信息涉及执行的检查、检查状态、查明的问题以及解决问题的建议步骤。 

> [!IMPORTANT]
> 适用于 Azure Migrate 设备的“诊断并解决”功能目前为预览版。
> 此预览版属于客户支持范围，可用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="diagnostic-checks"></a>诊断检查

“诊断并解决”功能会运行一些预验证来查明所需配置文件是否未缺失或未被设备的防病毒软件阻止，然后执行以下检查： 

**类别** | 诊断检查 |**说明**
--- | --- | --- |
**先决条件检查** | 连接性检查 | 检查设备与 Azure 之间是否存在连接（直接连接或通过代理进行的连接）。
|| 时间同步检查 | 检查设备服务器时间是否与网络时间同步。
|| 自动更新检查 | 检查是否已启用自动更新，以及设备上运行的所有代理是否都是最新的。
||VDDK 检查 | 检查所需的 VDDK 文件是否已下载并复制到设备服务器上的所需位置。
服务运行状况检查 |操作状态 |检查设备上的代理是否处于正在运行状态。 <br>如果没有正在运行，则设备会通过重启代理来自动解决该问题。 
||服务终结点连接性 |检查代理能否直接与 Azure 上的相应服务通信或通过代理来通信。
特定于 Azure 的检查 |AAD 应用可用性* | 检查在设备注册期间创建的 AAD 应用是否可用，以及是否可从该设备进行访问。
||Migrate 项目可用性* | 检查设备已注册到的 Migrate 项目是否仍然存在，以及是否可从设备对其进行访问。
||基本资源可用性*| 检查在设备注册期间创建的 Migrate 资源是否仍然存在，以及是否可从设备对其进行访问。
特定于设备的检查 | Key Vault 证书可用性* | 检查在设备注册期间从 Key Vault 下载的证书在设备服务器上是否仍然可用。 <br> 如果该证书不可用，但是 Key Vault 可用且可访问，则设备会通过再次下载证书来自动解决问题。
|| 凭据存储可用性 | 检查设备服务器上的凭据存储资源是否尚未被移动/删除/编辑。
|| 复制设备/ASR 组件 | 检查是否还使用了同一服务器来安装任何 ASR/复制设备组件。 目前不支持在同一服务器上同时安装 Azure Migrate 和复制设备（用于基于代理的迁移）。
|| OS 许可证可用性 | 检查从 OVA/VHD 创建的设备服务器上的评估许可证是否仍然有效。 Windows Server 2016 评估许可证的有效期为 180 天。
|| CPU 和内存利用率 | 检查设备服务器上的 Migrate 代理使用的 CPU 和内存。  

*只有在设备已注册的情况下，才会进行检查和报告。这些检查在已登录到设备的当前 Azure 用户的上下文中运行。

## <a name="running-diagnostic-checks"></a>运行诊断检查

如果在配置设备期间遇到任何问题，或者在门户上看到发现、评估和/或复制等正在进行的 Migrate 操作出现问题（如果使用的是 VMware 设备），则可以转到设备配置管理器并运行诊断。

> [!NOTE]
> 目前，“诊断并解决”功能可以执行与设备到 Azure 的连接、设备服务器和/或 Azure 上必需资源的可用性相关的检查。 “诊断并解决”功能目前没有涵盖源环境（例如 vCenter Server/ESXi 主机/Hyper-V 主机/VM/物理服务器）的连接或发现问题。
 
1. 从配置管理器顶部的功能区中选择“诊断并解决”。

    ![选择“诊断并解决”](./media/troubleshoot-appliance-diagnostic-solve/appliance-configuration-manager-diagnose-solve.png)
    
    在选择“诊断并解决”后，设备会自动开始运行诊断检查。 此过程可能需要大约 5 分钟才能完成。
    如果你之前运行过检查，则会看到上一个诊断报告的时间戳。
     
    ![诊断报告](./media/troubleshoot-appliance-diagnostic-solve/diagnostic-report.png)

1. 完成诊断检查后，可以在另一个选项卡中查看报告。在该选项卡中，你可以选择以 PDF 格式保存报告，也可以转到设备服务器上以 HTML 格式自动保存报告的位置，即 C:\Users\Public\Desktop\DiagnosticsReport。

    ![查看诊断报告](./media/troubleshoot-appliance-diagnostic-solve/view-diagnostic-report.png)

1. 该报告提供的信息涉及执行的检查、检查状态、查明的问题以及解决问题的建议步骤。

    ![查看诊断报告的状态](./media/troubleshoot-appliance-diagnostic-solve/view-status.png)

1. 你可以按照报告中的修正步骤来解决问题。 如果无法解决问题，建议在创建 Microsoft 支持案例时附加诊断报告，这样可加快问题的解决。

## <a name="next-steps"></a>后续步骤
如果你遇到的问题未涵盖在“诊断并解决”中，则可以转到[排查 Azure Migrate 设备问题](./troubleshoot-appliance.md)来查找修正步骤。