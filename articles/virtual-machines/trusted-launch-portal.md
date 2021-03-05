---
title: 预览：部署受信任的启动 VM
description: 部署使用可信启动的虚拟机。
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
ms.date: 03/02/2021
ms.custom: template-how-to
ms.openlocfilehash: f8a6758cdf0a6da709a564c1e17c56a2fe5625b0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124646"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>使用启用了可信启动 (预览部署 VM) 

[可信发布](trusted-launch.md) 是提高 [第2代](generation-2.md) vm 安全性的一种方法。 受信任的发布通过组合基础结构技术（例如 vTPM 和安全启动）来防范高级和持续攻击技术。

> [!IMPORTANT]
> 可信发布目前为公共预览版。
> 
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="deploy-using-the-portal"></a>使用门户进行部署

创建启用了可信启动的虚拟机。

1. 登录 Azure [门户](https://aka.ms/TL_preview)。
1. 搜索 **虚拟机**。
1. 在“服务”下，选择“虚拟机” 。
1. 在 " **虚拟机** " 页上，选择 " **添加**"，然后选择 " **虚拟机**"。
1. 在 " **项目详细信息**" 下，确保选择了正确的订阅。
1. 在 " **资源组**" 下，选择 " **新建** " 并键入资源组的名称，或从下拉列表中选择现有的资源组。
1. 在 " **实例详细信息**" 下，键入虚拟机名称的名称，并选择支持可信启动的区域。
1. 在 " **映像**" 下，选择 [支持可信启动的映像](trusted-launch.md#public-preview-limitations)。 你可能只能看到映像的第1代版本，那就可以继续下一步。
1. 通过在页面顶部选择 "高级" 选项卡切换到 " **高级** " 选项卡。
1. 向下滚动到 " **VM 生成** " 部分，然后选择 " **第2代**"。
1. 仍在 " **高级** " 选项卡上时，向下滚动到 " **可信启动**"，然后选择 " **受信任的启动** " 复选框。 这会显示两个更多选项-安全启动和 vTPM。 为你的部署选择适当的选项。

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="显示可信启动选项的屏幕截图。":::

1. 返回到 " **基本** " 选项卡，在 " **映像**" 下，确保看到以下消息： **此图像支持受信任的启动预览。在 "高级" 选项卡中配置**。现在应选择第2代映像。

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="显示消息的屏幕截图，该消息确认这是支持受信任启动的 gen2 映像。":::

1.  选择支持受信任启动的 VM 大小。 请参阅支持的 [大小](trusted-launch.md#public-preview-limitations)列表。
1.  填写 **管理员帐户** 信息，然后填写 **入站端口规则**。
1.  在页面底部，选择 "**查看 + 创建**"
1.  在 " **创建虚拟机** " 页上，可以查看有关要部署的 VM 的详细信息。 准备好以后，选择“创建”。

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="&quot;验证&quot; 页的 Sceenshot，其中包含了受信任的启动选项。":::


部署 VM 需要数分钟。 

## <a name="deploy-using-a-template"></a>使用模板进行部署

可以使用快速入门模板部署受信任的启动 Vm：

Linux：    
[![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**：    
[![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>查看和更新

可以通过在门户中访问 VM 的 " **概述** " 页，查看现有 vm 的可信启动配置。

若要更改受信任的启动配置，请在左侧菜单中选择 "**设置**" 部分下的 "**配置**"。 你可以从 " **受信任的启动** " 部分启用或禁用安全启动和 vTPM。 完成操作后，选择页面顶部的 " **保存** "。 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="如何更改受信任的启动配置的屏幕截图。":::

如果 VM 正在运行，则你将收到一条消息，指出 VM 将重新启动以应用修改后的可信启动配置。 选择 **"是"** ，然后等待 VM 重新启动，更改才能生效。


## <a name="verify-secure-boot-and-vtpm"></a>验证安全启动和 vTPM

你可以验证是否已在虚拟机上启用安全启动和 vTPM。
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux：验证安全启动是否正在运行

通过 SSH 连接到 VM，并运行以下命令： 

```bash
mokutil --sb-state
```

如果启用 "安全启动"，则该命令将返回：
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux：验证是否已启用 vTPM

通过 SSH 连接到 VM。 检查 tpm0 设备是否存在： 

```bash
ls /dev/tpm0
```

如果启用了 vTPM，则该命令将返回：

```output
/dev/tpm0
```

如果禁用 vTPM，则该命令将返回：

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows：验证安全启动是否正在运行

使用远程桌面连接到 VM，然后运行 `msinfo32.exe` 。

在右侧窗格中，检查安全启动状态是否为 **"已启用**"。

## <a name="enable-the-azure-security-center-experience"></a>启用 Azure 安全中心体验

若要使 Azure 安全中心能够显示有关可信启动 Vm 的信息，需要启用多个策略。 启用策略的最简单方法是将此 [资源管理器模板](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) 部署到订阅。 

选择下面的按钮以将策略部署到订阅：

[![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

每个订阅仅需部署一次模板。 它会自动 `GuestAttestation` `AzureSecurity` 在所有支持的 vm 上安装和扩展。 如果出现错误，请尝试重新部署模板。

若要获取有关受信任启动 Vm 的 vTPM 和安全启动建议，请参阅 [将自定义计划添加到订阅](https://docs.microsoft.com/azure/security-center/custom-security-policies#to-add-a-custom-initiative-to-your-subscription)。
 
## <a name="sign-things-for-secure-boot-on-linux"></a>在 Linux 上对安全启动进行签名

在某些情况下，可能需要为 UEFI 安全启动签名。  例如，你可能需要了解如何对 Ubuntu [的安全启动进行签名](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) 。 在这些情况下，需要输入 VM 的 MOK 实用程序注册密钥。 要执行此操作，需要使用 Azure 串行控制台来访问 MOK 实用程序。

1. 启用适用于 Linux 的 Azure 串行控制台。
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索 " **虚拟机** "，并从列表中选择你的 VM。
1. 在左侧菜单中的 " **支持 + 故障排除**" 下，选择 **串行控制台**。 将使用串行控制台向右打开一个页面。
1. 使用 Azure 串行控制台登录到 VM。 对于 " **登录名**"，请输入创建 VM 时使用的用户名。 例如， *azureuser*。 出现提示时，输入与用户名关联的密码。
1. 登录后，请使用 `mokutil` 导入公钥 `.der` 文件。

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. 通过键入从 Azure 串行控制台重新启动计算机 `sudo reboot` 。 将开始10秒倒计时。
1. 按向上键或向下键可中断倒计时并在 UEFI 控制台模式下等待。 如果计时器未中断，则启动过程将继续，并且所有 MOK 更改都将丢失。
1. 从 "MOK 实用工具" 菜单中选择相应的操作。

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="显示串行控制台中 &quot;MOK 管理&quot; 菜单上的可用选项的屏幕截图。":::


## <a name="next-steps"></a>后续步骤

详细了解 [受信任的启动](trusted-launch.md) 和 [第2代](generation-2.md) vm。
