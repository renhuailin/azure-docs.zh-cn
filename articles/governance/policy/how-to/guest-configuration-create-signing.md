---
title: 如何签署来宾配置包
description: 可以选择对来宾配置内容包进行签名，并强制代理只允许已签名的内容
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 7c7d120f17e32b3ea9accb6697ac66f4afcbb879
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868315"
---
# <a name="how-to-sign-guest-configuration-packages"></a>如何签署来宾配置包

来宾配置自定义策略使用 SHA256 哈希来验证策略包是否没有更改。 客户还可以选择使用证书对包进行签名，并强制来宾配置扩展只允许已签名的内容。

若要启用此方案，需要完成两个步骤。 运行 cmdlet 对内容包进行签名，并将标记追加到应需要对代码进行签名的计算机。

## <a name="signature-validation-using-a-code-signing-certificate"></a>使用代码签名证书的签名验证

若要使用签名验证功能，请运行 `Protect-GuestConfigurationPackage` cmdlet，以在发布前对包进行签名。 此 cmdlet 需要“代码签名”证书。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` cmdlet 的参数：

- 路径：来宾配置包的完整路径。
- Certificate：用于对包进行签名的代码签名证书。 只有在对 Windows 内容进行签名时，才支持此参数。

## <a name="certificate-requirements"></a>证书要求

GuestConfiguration 代理要求证书公钥在 Windows 计算机上的“受信任的根证书颁发机构”和 Linux 计算机上的 `/usr/local/share/ca-certificates/extra` 路径中存在。 为了让节点能够验证已签名的内容，请先在计算机上安装证书公钥，再应用自定义策略。 可以使用 VM 内的任何技术或使用 Azure Policy 来完成此过程。 示例模板可用于[部署具有证书的计算机](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows)。
Key Vault 访问策略必须允许计算资源提供程序在部署过程中访问证书。 有关详细步骤，请参阅[在 Azure 资源管理器中为虚拟机设置 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

下面是从签名证书导出公钥以导入计算机的示例。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

## <a name="tag-requirements"></a>标记要求

在内容发布后，将名为 `GuestConfigPolicyCertificateValidation` 且值为 `enabled` 的标记追加到所有应需要进行代码签名的虚拟机。 请参阅[标记示例](../samples/built-in-policies.md#tags)，了解如何使用 Azure Policy 大规模传递标记。 在此标记就位后，使用 `New-GuestConfigurationPolicy` cmdlet 生成的策略定义通过来宾配置扩展启用要求。

## <a name="next-steps"></a>后续步骤

- 从开发环境[测试包项目](./guest-configuration-create-test.md)。
- [发布包项目](./guest-configuration-create-publish.md)，以便计算机可以访问它。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](./guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](./determine-non-compliance.md#compliance-details-for-guest-configuration)。
