---
title: 更新群集以使用证书公用名称
description: 了解如何将 Azure Service Fabric 群集证书从基于指纹的声明转换为公用名。
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 013b8190390a4b05791b0a56072487f249956ec5
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495199"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>将群集证书从基于指纹的声明转换为公用名

 (通常称为指纹) 的证书签名是唯一的。 指纹声明的群集证书是指证书的特定实例。 此确切功能使得证书滚动更新和常规管理都非常困难。 每个更改都需要协调群集和基础计算主机的升级。

将 Azure Service Fabric 群集的证书声明从基于指纹的声明转换为基于证书的使用者公用名 (CN) 可大大简化管理。 特别是，回滚证书不再需要群集升级。 本文介绍如何在不停机的情况下将现有群集转换为基于 CN 的声明。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>移动到证书颁发机构签名的证书

其证书由指纹声明的群集的安全，而不可能（或计算不可行）使用与另一证书相同的签名伪造证书。 在这种情况下，证书的 provenance 不太重要，因此，自签名证书就足够了。

相反，由 CN 声明的群集的安全将由群集所有者在其证书提供程序中的隐式信任。 提供程序是颁发证书 (PKI) 服务的公钥基础结构。 信任基于 PKI 的认证做法，无论其操作安全是否由其他受信任方审核并批准，等等。

群集所有者还必须详细了解 (Ca 颁发证书的证书颁发机构) ，因为这是按主题验证证书的基本环节。 这也意味着自签名证书完全不适合此目的。 任何人都可以使用给定的主题生成证书。

在以下情况下，由 CN 声明的证书通常被视为有效证书：

* 它的链可以成功生成。
* 主题具有所需的 CN 元素。
* 它在链中 (立即或更高的颁发者) 受执行验证的代理信任。

Service Fabric 支持以两种方式通过 CN 声明证书：

* 使用 *隐式* 颁发者，这意味着链必须以信任密钥结束。
* 使用指纹声明的颁发者（称为颁发者固定）。

有关详细信息，请参阅 [基于名称的证书验证声明](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)。

若要使用由指纹声明的自签名证书转换群集，必须先通过指纹将目标、CA 签名证书引入群集。 只有这样才可以从指纹转换到 CN。

出于测试目的，自签名证书可由 CN 声明，但 *前提是颁发* 者已固定到其自己的指纹。 从安全角度来看，此操作几乎相当于按指纹声明相同的证书。 此类型的成功转换不保证使用 CA 签名的证书成功地从指纹转换为 CN。 建议使用正确的 CA 签名证书测试转换。 此测试存在可用选项。

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>上传证书并将其安装在规模集中

在 Azure 中，用于获取和设置证书的推荐机制涉及 Azure Key Vault 及其工具。 与群集证书声明匹配的证书必须预配到构成群集的虚拟机规模集的每个节点。 有关详细信息，请参阅 [虚拟机规模集上的机密](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm)。

在群集的证书声明中进行更改之前，必须在群集的每个节点类型的虚拟机上安装当前和目标群集证书。 证书的 [旅程](cluster-security-certificate-management.md#the-journey-of-a-certificate)中深入讨论了从证书颁发到预配到 Service Fabric 节点的旅程。

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>使群集进入最佳启动状态

将证书声明从基于指纹的影响转换为基于 CN 的影响：

- 群集中的每个节点如何查找并将其凭据提供给其他节点。
- 建立安全连接时，每个节点如何验证其对应的凭据。

在继续操作之前，请查看 [这两种配置的显示和验证规则](cluster-security-certificates.md#certificate-configuration-rules) 。 执行指纹到 CN 转换时，最重要的注意事项是升级和未升级的节点 (即，属于不同升级域) 的节点必须能够在升级过程中的任何时候执行成功相互身份验证。 实现此行为的建议方法是在初始升级时按指纹声明目标或目标证书。 然后完成过渡到下一个 CN。 如果群集已处于建议的启动状态，则可以跳过此部分。

有多个有效的转换起始状态。 固定条件是在开始升级到 CN 时，群集已在使用指纹) 声明 (目标证书。 `GoalCert` `OldCert1` 本文介绍了、和 `OldCert2` 。

#### <a name="valid-starting-states"></a>有效的起始状态

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`，其中的 `GoalCert` 日期晚 `NotAfter` 于 `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`，其中的 `GoalCert` 日期晚 `NotAfter` 于 `OldCert1`

如果你的群集未处于前面所述的有效状态之一，请参阅本文末尾部分的关于如何实现该状态的信息。

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>选择所需的基于 CN 的证书验证方案

如前文所述，Service Fabric 支持通过 CN 和隐式信任定位点声明证书，或者通过显式固定颁发者指纹。 有关详细信息，请参阅 [基于名称的证书验证声明](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)。

请确保您对选择任何一种机制都有很好的了解。 从语法上讲，这种差异或选择取决于参数的值 `certificateIssuerThumbprintList` 。 空表示依赖受信任的根 CA (信任密钥) ，而一组指纹限制允许的群集证书直接颁发者。

   > [!NOTE]
   > `certificateIssuerThumbprint`使用 "字段" 字段，可以指定由使用者 CN 声明的证书的预期直接颁发者。 可接受的值为一个或多个以逗号分隔的 SHA1 指纹。 此操作会增强证书验证。
   >
   > 如果未指定颁发者或列表为空，则如果证书链可以生成，则接受身份验证。 然后，证书最终出现在验证程序信任的根中。 如果指定了一个或多个颁发者指纹，则将接受该证书，前提是该证书的直接颁发者的指纹与此字段中指定的任何值匹配。 无论根是否可信，都将接受该证书。
   >
   > PKI 可能会使用不同的证书颁发机构 (也称为 *颁发* 者) 使用给定的主题为证书签名。 出于此原因，请务必为该使用者指定所有预期的颁发者指纹。 换句话说，证书续订不保证由与要续订的证书相同的颁发者进行签名。
   >
   > 指定颁发者被视为最佳做法。 省略颁发者将继续适用于链接到受信任的根的证书，但此行为有限制，并可能会在不久的将来推出。 在 Azure 中部署的群集，使用专用 PKI 颁发的 X509 证书进行保护，并由使用者声明可能无法通过 Service Fabric (进行群集到服务通信) 验证。 验证要求 PKI 的证书策略可被发现、可用并可访问。

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>更新群集的 Azure 资源管理器模板并部署

通过 Azure 资源管理器 (ARM) 模板管理 Service Fabric 群集。 另一种方法（也使用 JSON 项目）是 [Azure 资源浏览器 (预览版) ](https://resources.azure.com)。 Azure 门户此时无法使用等效的体验。

如果与现有群集相对应的原始模板不可用，则可以在 Azure 门户中获取等效模板。 中转到包含该群集的资源组，然后从左侧的 "**自动化**" 菜单中选择 "**导出模板**"。 然后选择所需的资源。 至少应导出虚拟机规模集和群集资源。 还可以下载生成的模板。 此模板可能需要更改才能完全部署。 模板也可能与原始模板完全不匹配。 它反映群集资源的当前状态。

必要的更改如下所示：

- 正在) 虚拟机资源 (更新 Service Fabric 节点扩展的定义。 如果群集定义了多个节点类型，则需要更新每个相应虚拟机规模集的定义。
- 正在更新群集资源定义。

此处提供了详细的示例。

### <a name="update-the-virtual-machine-scale-set-resources"></a>更新虚拟机规模集资源
发件人：
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
到:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>更新群集资源

在**ServiceFabric/群集**资源中，添加具有**CommonNames**设置的**certificateCommonNames**属性，并)  (所有设置来完全删除**证书**属性。

发件人：
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
到:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

有关详细信息，请参阅 [部署使用证书公用名而不是指纹的 Service Fabric 群集](./service-fabric-create-cluster-using-cert-cn.md)。

## <a name="deploy-the-updated-template"></a>部署已更新的模板

进行更改后重新部署已更新的模板。

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>实现将群集转换为基于 CN 的证书声明的有效起始状态

| 开始状态 | 升级 1 | 升级 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 和的 `GoalCert` 日期晚 `NotAfter` 于 `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 和的 `OldCert1` 日期晚 `NotAfter` 于 `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`，其中的 `OldCert1` 日期晚 `NotAfter` 于 `GoalCert` | 升级到 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`，其中的 `OldCert1` 日期晚 `NotAfter` 于 `GoalCert` | 升级到 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | 删除 `OldCert1` 或中的一个 `OldCert2` 以获取状态 `Thumbprint: OldCertx, ThumbprintSecondary: None` | 从新的起始状态继续 |

有关如何执行这些升级的说明，请参阅 [管理 Azure Service Fabric 群集中的证书](service-fabric-cluster-security-update-certs-azure.md)。

## <a name="next-steps"></a>后续步骤

* 了解[群集安全性](service-fabric-cluster-security.md)。
* 了解如何 [按公用名滚动群集证书](service-fabric-cluster-rollover-cert-cn.md)。
* 了解如何为 [touchless autorollover 配置群集](cluster-security-certificate-management.md)。

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
