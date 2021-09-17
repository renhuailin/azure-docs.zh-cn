---
title: 预览版：部署受信任启动 VM
description: 部署一个使用受信任启动的 VM。
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: bcc91283c29aaef251c2a18422e90090c56a6298
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688916"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>部署已启用受信任启动的 VM（预览版）

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集

[受信任启动](trusted-launch.md)是一种提高[第 2 代](generation-2.md) VM 安全性的方法。 受信任启动通过组合基础结构技术（例如 vTPM 和安全启动）来防范高级攻击和持续性攻击方法。

> [!IMPORTANT]
> 受信任启动目前以公共预览版提供。
>
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="deploy-using-the-portal"></a>使用门户进行部署

创建已启用受信任启动的虚拟机。

1. 登录 Azure [门户](https://aka.ms/TL_preview)。
   > [!NOTE]
   > 门户链接对于受信任启动预览版是独一无二的。
   >
2. 搜索“虚拟机”。
3. 在“服务”下，选择“虚拟机” 。
4. 在“虚拟机”页中选择“添加”，然后选择“虚拟机”。 
5. 在“项目详细信息”下，确保选择了正确的订阅。
6. 在“资源组”下选择“新建”并键入资源组的名称，或者从下拉列表中选择现有的资源组。
7. 在“实例详细信息”下，键入一个名称作为虚拟机名称，并选择一个支持[受信任启动](trusted-launch.md#public-preview-limitations)的区域。
8. 在“映像”下，选择一个[支持受信任启动的第 2 代映像](trusted-launch.md#public-preview-limitations)。 确保看到以下消息：“此映像支持受信任启动预览版。请在‘高级’选项卡中配置”。
   > [!TIP]
   > 如果下拉列表中未显示你需要的第 2 代版本映像，请选择“查看所有映像”，然后将“VM 代系”筛选器更改为仅显示第 2 代映像 。 在列表中查找映像，然后使用“选择”下拉列表选择第 2 代版本。

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="屏幕截图，其中显示的消息确认这是支持受信任启动的第 2 代映像。":::

13. 选择支持受信任启动的 VM 大小。 请查看[受支持的大小](trusted-launch.md#public-preview-limitations)的列表。
14. 填写“管理员帐户”信息，然后填写“入站端口规则”。
1. 通过在页面顶部选择“高级”选项卡，切换到“高级”选项卡。
1. 向下滚动到“VM 代系”部分。 确保选中“第 2 代”。
1. 仍在“高级”选项卡上时，向下滚动到“受信任启动”，然后选中“受信任启动”复选框。   这样会显示另外两个选项 -“安全启动”和“vTPM”。 选择适合你的部署的选项。

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="屏幕截图，显示受信任启动的选项。":::

15. 在页面底部，选择“查看 + 创建”
16. 在“创建虚拟机”页上，可以查看要部署的 VM 的详细信息。 准备好以后，选择“创建”。

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="“验证”页的屏幕截图，显示已包含了受信任启动选项。":::


部署 VM 需要数分钟。

## <a name="deploy-using-a-template"></a>使用模板进行部署

可以使用快速启动模板部署受信任启动 VM：

Linux：[![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2FcreateUiDefinition.json)

Windows：[![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>查看和更新

可以通过在门户中访问 VM 的“概述”页，查看现有 VM 的受信任启动配置。

若要更改受信任启动配置，请在左侧菜单中选择“设置”部分下的“配置”。 你可以从“受信任启动”部分启用或禁用“安全启动”和“vTPM”。 完成操作后，选择页面顶部的“保存”。

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="屏幕截图，显示如何更改受信任启动配置。":::

如果 VM 正在运行，你会收到一条消息，指出 VM 将重启以应用修改的受信任启动配置。 选择“是”，然后等待 VM 重启，使更改生效。


## <a name="verify-secure-boot-and-vtpm"></a>验证安全启动和 vTPM

你可以验证是否已在虚拟机上启用安全启动和 vTPM。

### <a name="linux-validate-if-secure-boot-is-running"></a>Linux：验证安全启动是否正在运行

通过 SSH 连接到 VM，然后运行以下命令：

```bash
mokutil --sb-state
```

如果启用了安全启动，则该命令会返回以下内容：

```bash
SecureBoot enabled
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux：验证是否已启用 vTPM

通过 SSH 连接到 VM。 检查 tpm0 设备是否存在：

```bash
ls /dev/tpm0
```

如果启用了 vTPM，则该命令会返回以下内容：

```output
/dev/tpm0
```

如果禁用了 vTPM，则该命令会返回以下内容：

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows：验证安全启动是否正在运行

使用远程桌面连接到 VM，然后运行 `msinfo32.exe`。

在右窗格中，检查“安全启动状态”是否为“已启用”。

## <a name="enable-the-azure-security-center-experience"></a>启用 Azure 安全中心体验

若要使 Azure 安全中心能够显示有关受信任启动 VM 的信息，需要启用多个策略。 启用这些策略的最简单方法是将此[资源管理器模板](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies)部署到订阅。

选择下面的按钮以将策略部署到订阅：

[![部署到 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

每个订阅只需将此模板部署一次。 它会自动在所有受支持的 VM 上安装 `GuestAttestation` 和 `AzureSecurity` 扩展。 如果出现错误，请尝试重新部署此模板。

若要获取有关受信任启动 VM 的 vTPM 和安全启动建议，请参阅[将自定义计划添加到订阅](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)。

## <a name="sign-things-for-secure-boot-on-linux"></a>在 Linux 上为安全启动签名

在某些情况下，可能需要为 UEFI 安全启动签名。  例如，你可能需要了解[如何为 Ubuntu 的安全启动签名](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot)。 在这些情况下，需要为 VM 输入 MOK 实用工具注册密钥。 为此，需要使用 Azure 串行控制台来访问 MOK 实用工具。

1. 启用适用于 Linux 的 Azure 串行控制台。 有关详细信息，请参阅[适用于 Linux 的串行控制台](/troubleshoot/azure/virtual-machines/serial-console-linux)。
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“虚拟机”，然后从列表中选择你的 VM。
1. 在左菜单中的“支持 + 故障排除”下，选择“串行控制台” 。 此时会在右侧打开一个页面，其中包含串行控制台。
1. 使用 Azure 串行控制台登录到 VM。 对于“登录名”，请输入创建 VM 时使用的用户名。 例如 azureuser。 出现提示时，输入与用户名关联的密码。
1. 登录后，使用 `mokutil` 导入公钥 `.der` 文件。

    ```bash
    sudo mokutil –import <path to public key.der>
    ```
1. 通过键入 `sudo reboot` 从 Azure 串行控制台重启计算机。 此时会开始 10 秒倒计时。
1. 按向上键或向下键可中断倒计时并在 UEFI 控制台模式下等待。 如果计时器未中断，则启动过程会继续，所有 MOK 更改都会丢失。
1. 从 MOK 实用工具菜单中选择适当的操作。

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="屏幕截图，显示串行控制台中 MOK 管理菜单上的可用选项。":::


## <a name="next-steps"></a>后续步骤

详细了解[受信任启动](trusted-launch.md)和[第 2 代](generation-2.md) VM。
