---
title: 将 Azure 自动化 runbook 添加到 Site Recovery 恢复计划中
description: 了解如何使用 Azure 自动化扩展恢复计划以使用 Azure Site Recovery 进行灾难恢复。
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.author: sharrai
ms.date: 07/15/2021
ms.openlocfilehash: b6adcfb9a6756872bb7180606acdbbdceeb16d2f
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219461"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>将 Azure 自动化 Runbook 添加到恢复计划

本文介绍如何集成 Azure 自动化 runbook，以扩展 [Azure Site Recovery](site-recovery-overview.md) 恢复计划。 我们将介绍如何自动执行以前需要手动干预的基本任务，并了解如何将多步骤恢复转换成单击恢复操作。

## <a name="recovery-plans"></a>恢复计划 

对本地计算机或 Azure VM 进行故障转移时，可以使用恢复计划。 恢复计划可帮助你定义系统恢复过程，该过程可定义计算机如何进行故障转移，以及如何在故障转移后启动和恢复。 

恢复大型应用可能很复杂。 恢复计划可帮助维持秩序，以实现恢复的准确性、可重复性和自动化。 可以使用脚本和 Azure 自动化 runbook 来自动执行恢复计划中的任务。 典型示例包括故障转移后在 Azure VM 上配置设置，或者重新配置在 VM 上运行的应用。

- [详细了解](recovery-plan-overview.md)恢复计划。
- [详细了解](../automation/automation-runbook-types.md) Azure 自动化 runbook。



## <a name="runbooks-in-recovery-plans"></a>恢复计划中的 runbook

将 Azure 自动化帐户和 runbook 添加到恢复计划。 恢复计划运行时将调用 runbook。

- 自动化帐户可位于任意 Azure 区域，但必须与 Site Recovery 保管库位于同一订阅中。 
- 在从主要位置故障转移到辅助位置或从辅助位置故障恢复到主要位置期间，runbook 可以在恢复计划中运行。
- 恢复计划中的 runbook 按设置顺序依次运行。
- 如果恢复计划中的 runbook 将 VM 配置为在不同的组中启动，则只有当 Azure 将所有 VM 报告为运行时，恢复计划才会继续。
- 即使脚本失败，恢复计划仍将继续运行。

### <a name="recovery-plan-context"></a>恢复计划上下文

脚本在运行时，会向 runbook 注入恢复计划上下文。 上下文包含表中汇总的变量。

| **变量名称** | **说明** |
| --- | --- |
| RecoveryPlanName |恢复计划名称。 用于基于名称的操作。 |
| FailoverType |指定它是测试故障转移还是生产故障转移。 
| FailoverDirection | 指定是恢复到主要位置，还是恢复到辅助位置。 |
| GroupID |计划运行时，在恢复计划内标识组编号。 |
| VmMap |组中所有 VM 的数组。 |
| VMMap 键 |每个 VM 的唯一键 (GUID)。 |
| SubscriptionId |其中创建了 VM 的 Azure 订阅的 ID。 |
| ResourceGroupName | VM 所在的资源组的名称。
| CloudServiceName |以其名义创建了 VM 的 Azure 云服务名称。 |
| RoleName |Azure VM 的名称。 |
| RecoveryPointId|VM 恢复的时间戳。 |

>[!Note]
>变量“FailoverDirection”的值在故障转移时为“PrimaryToSecondary”，在故障恢复时为“SecondaryToPrimary”。

下面的示例展示了上下文变量：

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

若要循环访问 VMMap 中的所有 VM，可以使用以下代码：

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Aman Sharma 在其博客 [Harvesting Clouds](http://harvestingclouds.com) 上给出了一个有用的[恢复计划上下文脚本](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)示例。



## <a name="before-you-start"></a>开始之前

- 如果刚开始接触 Azure 自动化，可以[注册](https://azure.microsoft.com/services/automation/)并[下载示例脚本](https://azure.microsoft.com/documentation/scripts/)。
- 确保自动化帐户包含以下模块：
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    所有模块的版本都应兼容。 最简单的方法是始终使用所有模块的最新版本。



## <a name="customize-the-recovery-plan"></a>自定义恢复计划

1. 在保管库中，选择“恢复计划(Site Recovery)”
2. 要创建恢复计划，请单击“+恢复计划”。 [了解详细信息](site-recovery-create-recovery-plans.md)。 如果已有恢复计划，请选择并打开它。
3. 在恢复计划页中，单击“自定义”。

    ![单击“自定义”按钮](media/site-recovery-runbook-automation-new/custom-rp.png)

2. 单击“组 1:开始” > “添加后操作”旁边的省略号 (…)。
3. 在“插入操作”中，验证是否选择了“脚本”，并指定脚本的名称 (Hello World)  。
4. 指定自动化帐户并选择一个 runbook。 选择“确定”，保存脚本。 此时，脚本将添加到“组 1: 后步骤”。


## <a name="reuse-a-runbook-script"></a>重复使用 runbook 脚本

你可以使用外部变量，在多个恢复计划中使用同一个 runbook 脚本。 

- 使用 [Azure 自动化变量](../automation/shared-resources/variables.md)存储用于运行恢复计划的参数。
- 通过将恢复计划名称作为前缀添加到变量，可以为每个恢复计划单独创建变量。 然后，将变量用作参数。
- 可以在不更改脚本的情况下更改参数，但仍会改变脚本的工作方式。

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>在 Runbook 脚本中使用简单字符串变量

在以下示例中，脚本使用网络安全组 (NSG) 的输入，并将其应用于恢复计划的 VM。 

1. 为了让脚本能够检测出正在运行的恢复计划，可以使用恢复计划上下文：

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. 注意 NSG 名称和资源组。 将这些变量用作恢复计划脚本的输入。 
1. 在自动化帐户资产中。 创建用于存储 NSG 名称的变量。 使用恢复计划的名称，将前缀添加到变量名称中。

    ![创建 NSG 名称变量](media/site-recovery-runbook-automation-new/var1.png)

2. 创建用于存储 NSG 资源的资源组名称的变量。 使用恢复计划的名称，将前缀添加到变量名称中。

    ![创建 NSG 资源组名称](media/site-recovery-runbook-automation-new/var2.png)


3.  在脚本中，使用以下参考代码获取变量值：

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  在 Runbook 中使用这些变量，将 NSG 应用到已故障转移的 VM 的网络接口：

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


对于每个恢复计划，请创建独立变量，以便能够重用脚本。 使用恢复计划名称添加前缀。 

如需查看此方案的整个端到端脚本，请参阅[此脚本](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)。


### <a name="use-a-complex-variable-to-store-more-information"></a>使用复杂变量存储更多信息

在某些情况下，可能无法为每个恢复计划创建单独的变量。 设想这样一种情景：你需要使用一个脚本在特定 VM 上分配公共 IP 地址。 在另一种情况下，需要在不同的 VM 上（而不是在所有 VM 上）应用其他 NSG。 请注意：

- 你可以创建可在任何恢复计划中重用的脚本。
- 每个恢复计划包含的 VM 数量不定。
- 例如，SharePoint 恢复有两个前端。 基本业务线 (LOB) 应用程序只有一个前端。
- 在这种情况下，无法为每个恢复计划创建单独的变量。

在下面的示例中，我们在 Azure 自动化帐户中创建一个[复杂变量](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable)。

为此，我们可以使用 Azure PowerShell 指定多个值。

1. 在 PowerShell 中，登录 Azure 订阅：

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. 若要存储参数，请使用恢复计划名称创建复杂变量：

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. 在此复杂变量中，**VMDetails** 是受保护 VM 的 VM ID。 若要获取 VM ID，请在 Azure 门户中查看 VM 属性。 下面的屏幕截图展示了一个存储两个 VM 详细信息的变量：

    ![将 VM ID 用作 GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. 在 Runbook 中使用此变量。 如果在恢复计划上下文中找到了指定的 VM GUID，请在 VM 上应用 NSG：

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. 在 Runbook 中，循环访问恢复计划上下文的 VM。 检查 **$VMDetailsObj** 中是否有 VM。 如果有，请访问变量属性，以应用 NSG：

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

可以对不同的恢复计划使用同一脚本。 通过存储不同变量中与恢复计划对应的值，输入不同的参数。

## <a name="sample-scripts"></a>示例脚本

单击“部署到 Azure”按钮，将示例脚本部署到自动化帐户。

[![“部署到 Azure”](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

此视频提供了另一个示例。 它演示了如何将两层 WordPress 应用程序恢复到 Azure：


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>后续步骤

- 了解 [Azure 自动化运行方式帐户](../automation/manage-runas-account.md)
- 查看 [Azure 自动化示例脚本](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team)。
- [详细了解](site-recovery-failover.md)如何运行故障转移。
