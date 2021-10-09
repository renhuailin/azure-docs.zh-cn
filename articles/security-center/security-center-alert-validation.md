---
title: Azure 安全中心的警报验证 | Microsoft Docs
description: 了解如何验证在 Azure 安全中心中正确配置了安全警报
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: 48a695f4217c7300c4d7db6edc23452ad4eaa55f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631697"
---
# <a name="alert-validation-in-azure-security-center"></a>Azure 安全中心的警报验证
本文档介绍如何验证系统是否已针对 Azure 安全中心警报进行了适当的配置。

## <a name="what-are-security-alerts"></a>什么是安全警报？
警报是安全中心在检测到资源遭受威胁时生成的通知。 安全中心会按优先级列出警报，同时还会提供所需信息以快速调查问题。 安全中心还提供有关如何针对攻击采取补救措施的建议。
有关详细信息，请参阅[安全中心中的安全警报](security-center-alerts-overview.md)和[管理和响应安全警报](security-center-managing-and-responding-alerts.md)


## <a name="generate-sample-azure-defender-alerts"></a>生成示例 Azure Defender 警报

如果你使用的是[在 Azure 安全中心内管理和响应安全警报](security-center-managing-and-responding-alerts.md)中所述的新的预览警报体验，则可以通过在 Azure 门户的“安全警报”页中单击几下鼠标来创建示例警报。

使用示例警报可：

- 评估 Azure Defender 的价值和功能
- 验证对安全警报所做的任何配置（例如 SIEM 集成、工作流自动化和电子邮件通知）

创建示例警报：

1. 以具有角色“订阅参与者”的用户身份，从“警报”页上的工具栏中选择“创建示例警报”。
1. 选择订阅。
1. 选择要查看其警报的相关 Azure Defender 计划。 
1. 选择“创建示例警报”。

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="在 Azure 安全中心创建示例警报的步骤。":::
    
    此时将显示一条通知，告知正在创建示例警报：

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="正在生成示例警报的通知。":::

    几分钟后，警报将出现在“安全警报”页中。 它们还将出现在已配置为接收 Azure 安全中心安全警报（连接的 SIEM、电子邮件通知等）的其他任何位置。

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="安全警报列表中的示例警报。":::

    > [!TIP]
    > 警报用于模拟资源。

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>模拟 Azure VM (Windows) 上的警报 <a name="validate-windows"></a>

在计算机上安装安全中心代理以后，请在想让其成为受攻击的警报资源的计算机上执行以下步骤：

1. 复制一个可执行文件（例如“calc.exe”）到计算机的桌面或方便操作的其他目录，然后将其重命名为“ASC_AlertTest_662jfi039N.exe” 。
1. 打开命令提示符，使用一个参数（假参数名称即可）执行此文件，例如：```ASC_AlertTest_662jfi039N.exe -foo```
1. 等待 5 到 10 分钟，然后打开安全中心警报。 应该会出现警报。

> [!NOTE]
> 查看此 Windows 的测试警报时，请确保“启用参数审核”字段为 true 。 如果为 false，则需启用命令行参数审核。 若要启用它，请使用以下命令：
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>模拟 Azure VM (Linux) 上的警报 <a name="validate-linux"></a>

在计算机上安装安全中心代理以后，请在想让其成为受攻击的警报资源的计算机上执行以下步骤：

1. 将可执行文件复制到一个方便的位置，并将其重命名为 `./asc_alerttest_662jfi039n`。 例如：

    `cp /bin/echo ./asc_alerttest_662jfi039n`

1. 打开命令提示符并执行此文件：

    `./asc_alerttest_662jfi039n testing eicar pipe`

1. 等待 5 到 10 分钟，然后打开“安全中心警报”。 应该会出现警报。

## <a name="simulate-alerts-on-kubernetes"></a>模拟 Kubernetes 上的警报 <a name="validate-kubernetes"></a>

如果已将 Azure Kubernetes 服务与安全中心集成，则可以使用以下 kubectl 命令测试警报是否正常运行：

`kubectl get pods --namespace=asc-alerttest-662jfi039n`

有关保护 Kubernetes 节点和群集的详细信息，请参阅 [Azure Defender for Kubernetes 简介](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>后续步骤
本文介绍了警报验证过程。 熟悉该验证以后，请尝试阅读以下文章：

* [Validating Azure Key Vault Threat Detection in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)（在 Azure 安全中心验证 Azure Key Vault 威胁检测）
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全中心的安全事件。
* [了解 Azure 安全中心的安全警报](./security-center-alerts-overview.md) - 了解各种类型的安全警报。
