---
title: 将非 Azure 计算机连接到 Azure 安全中心
description: 了解如何将非 Azure 计算机连接到安全中心
author: memildin
ms.author: memildin
ms.date: 07/12/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: fc844a3f0f13db3520f0a8302a98392301030d19
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228736"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>将非 Azure 计算机连接到安全中心

安全中心可以监视非 Azure 计算机的安全状态，但首先需要将其连接到 Azure。

可以通过以下任何一种方式连接非 Azure 计算机：

- 使用启用了 Azure Arc 的服务器（推荐）
- 使用 Azure 门户中的安全中心的页面（“入门”和“库存”）

此页上对上述每种方式都进行了介绍。

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>使用 Azure Arc 添加非 Azure 计算机

若要将非 Azure 计算机添加到 Azure 安全中心，首选方法是使用[启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)。

启用了 Azure Arc 的服务器的计算机将成为一项 Azure 资源，并且（在计算机上安装日志分析代理后）会显示在安全中心，并与其他 Azure 资源一样提供建议。

此外，启用了 Azure Arc 的服务器还提供增强功能，例如在计算机上启用来宾配置策略、使用其他 Azure 服务简化部署等选项。 有关这些优势的概述，请参阅[支持的云操作](../azure-arc/servers/overview.md#supported-cloud-operations)。

> [!NOTE]
> 安全中心的自动部署工具（用于部署日志分析代理）不支持运行 Azure Arc 的计算机。使用 Azure Arc 连接计算机后，请采用安全中心的相关建议部署代理，以便获得安全中心提供的全面保护：
>
> - [日志分析代理应安装在基于 Linux 的 Azure Arc 计算机上](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/720a3e77-0b9a-4fa9-98b6-ddf0fd7e32c1)
> - [日志分析代理应安装在基于 Windows 的 Azure Arc 计算机上](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/27ac71b1-75c5-41c2-adc2-858f5db45b08)

详细了解[启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)。

**若要部署 Azure Arc，请执行以下操作：**

- 对于一台计算机，请按照[快速入门：使用启用了 Azure Arc 的服务器连接混合计算机](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)中的说明进行操作。
- 若要大规模地将多台计算机连接到启用了 Azure Arc 的服务器，请参阅[将混合计算机大规模地连接到 Azure](../azure-arc/servers/onboard-service-principal.md)

> [!TIP]
> 若要加入在 Amazon Web Services (AWS) 上运行的计算机，安全中心的 AWS 连接器将为你以透明方式处理 Azure Arc 部署。 在[将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md)中了解详细信息。

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>从 Azure 门户添加非 Azure 计算机

1. 在安全中心的菜单中，打开“开始使用”页。
1. 选择“入门”选项卡。

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="“开始使用”页中的“开始使用”选项卡。" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. 在“添加非 Azure 服务器”下方，选择“配置”。

    > [!TIP]
    > 还可以通过“库存”页面的“添加非 Azure 服务器”按钮打开“添加计算机”。
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="从“资产清单”页添加非 Azure 计算机。":::

    此时将显示 Log Analytics 工作区的列表。 该列表包含启用自动预配时由安全中心创建的默认工作区（如果适用）。 选择此工作区或要使用的其他工作区。

    可以将计算机添加到现有的工作区，也可以新建一个工作区。

1. 如果要新建一个工作区，请选择“新建工作区”。

1. 在工作区列表中，为相关工作区选择“添加服务器”。
    随即会显示“代理管理”页。

    在这里，根据要加入的计算机类型，在下方选择相关过程：

    - [加入 Azure Stack Hub VM](#onboard-your-azure-stack-hub-vms)
    - [加入 Linux 计算机](#onboard-your-linux-machines)
    - [加入 Windows 计算机](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-hub-vms"></a>加入 Azure Stack Hub VM

若要添加 Azure Stack Hub VM，你需要“代理管理”页上的信息，并在虚拟机（在 Azure Stack Hub 实例上运行）上配置“Azure Monitor、更新和配置管理”虚拟机扩展。

1. 从“代理管理”页上，将工作区 ID和主密钥复制到记事本中。
1. 登录到“Azure Stack Hub”门户，然后打开“虚拟机”页。
1. 选择要使用安全中心保护的虚拟机。
    >[!TIP]
    > 若要了解如何在 Azure Stack Hub 上创建虚拟机，请参阅[此适用于 Windows 虚拟机的快速入门](/azure-stack/user/azure-stack-quick-windows-portal)或[此适用于 Linux 虚拟机的快速入门](/azure-stack/user/azure-stack-quick-linux-portal)。
1. 选择“扩展”。 此时将显示此虚拟机上安装的虚拟机扩展列表。
1. 选择“添加”选项卡。“新建资源”菜单会显示可用虚拟机扩展的列表。
1. 选择“Azure 监视、更新和配置管理”扩展，然后选择“创建”。  此时会打开“安装扩展”配置页。
    >[!NOTE]
    > 如果在市场中没有看到“Azure Monitor、更新和配置管理”扩展，请联系 Azure Stack Hub 操作员，让该操作员将其列出。
1. 在“安装扩展”配置页上，粘贴在前面步骤中复制到记事本的“工作区 ID” 和“工作区密钥(主密钥)”  。
1. 完成配置后，选择“确定”。 扩展的状态将显示为“预配成功”。 最长可能需要经过一小时，该虚拟机才会显示在安全中心中。

### <a name="onboard-your-linux-machines"></a>加入 Linux 计算机

若要添加 Linux 计算机，需要“代理管理”页中的 WGET 命令。

1. 从“代理管理”页上，将 WGET 命令复制到记事本中。 将此文件保存到可以从你的 Linux 计算机访问的位置。
1. 在 Linux 计算机上，打开包含 WGET 命令的文件。 选择整个内容，并将其复制并粘贴到终端控制台。
1. 在安装完成后，可以通过运行 pgrep 命令验证 omsagent 是否已安装。 该命令将返回 omsagent PID。
    可以在以下位置找到该代理的日志：/var/opt/microsoft/omsagent/\<workspace id>/log/ 最多可能需要等待 30 分钟，之后新的 Linux 计算机会出现在安全中心。

### <a name="onboard-your-windows-machines"></a>加入 Windows 计算机

若要添加 Windows 计算机，需要“代理管理”页上的信息，并下载合适的代理文件（32/64 位）。
1. 选择适用于计算机处理器类型的“下载 Windows 代理”链接，以下载安装程序文件。
1. 从“代理管理”页上，将工作区 ID和主密钥复制到记事本中。
1. 将设置文件复制并下载到目标计算机并运行。
1. 按照安装向导的步骤操作（“下一步”，“我同意”，“下一步”，“下一步”   ）。
    1. 在 **Azure Log Analytics** 页上，粘贴复制到记事本的“工作区 ID” 和“工作区密钥（主密钥）” 。
    1. 如果计算机应向 Azure 政府云中的 Log Analytics 工作区报告，请从“Azure 云”下拉列表中选择“Azure 美国政府” 。
    1. 如果计算机需要通过代理服务器来与 Log Analytics 服务通信，请选择“高级”并提供代理服务器的 URL 和端口号。
    1. 输入所有配置设置后，选择“下一步”。
    1. 在“准备安装”页上，查看要应用的设置，并选择“安装” 。
    1. 在“配置已成功完成”页上，选择“完成”。

完成后，Microsoft Monitoring Agent 将显示在“控制面板”中 。 可以在该处检查配置，并验证代理是否已连接。

有关安装和配置代理的详细信息，请参阅[连接 Windows 计算机](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)。

::: zone-end

## <a name="verifying"></a>验证

恭喜！ 现在，可以在同一个位置查看 Azure 和非 Azure 计算机了。 打开[“资产清单”页](asset-inventory.md)，并筛选到相关的资源类型。 这些图标区分类型：

  ![指示非 Azure 计算机的 ASC 图标。](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) 非 Azure 计算机

  ![指示 Azure 计算机的 ASC 图标。](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

  ![指示 Azure Arc 服务器的 ASC 图标。](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) 启用了 Azure Arc 的服务器

## <a name="next-steps"></a>后续步骤

本页介绍了如何将非 Azure 计算机添加到 Azure 安全中心。 若要监视其状态，请使用以下页面中介绍的清单工具：

- [使用资产清单浏览和管理资源](asset-inventory.md)