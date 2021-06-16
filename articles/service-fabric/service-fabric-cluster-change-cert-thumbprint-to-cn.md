---
title: 更新群集以使用证书公用名称
description: 了解如何将 Azure Service Fabric 群集证书从基于指纹的声明转换为公用名。
ms.topic: conceptual
ms.date: 09/06/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 88a2dc070dd1443e7d91f9d7e3f641c8e74f5a82
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671074"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>将群集证书从基于指纹的声明转换为公用名

证书的签名（通常称为指纹）是唯一的。 指纹声明的群集证书指的是证书的特定实例。 由于这种特异性，证书滚动更新和常规管理变得很困难，必须采取显式方式。 每个更改都需要协调群集和基础计算主机的升级。

将 Azure Service Fabric 群集的证书声明从基于指纹转换为基于证书使用者公用名 (CN) 的声明可显著简化管理。 特别是，滚动更新证书不再需要群集升级。 本文介绍了如何在不停机的情况下将现有群集转换为基于 CN 的声明。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>迁移到证书颁发机构签名的证书

其证书由指纹声明的群集的安全性依赖于以下事实：伪造与另一证书具有相同签名的证书是不可能的，或者在计算上是不可行的。 在这种情况下，证书的来源不太重要，因此，自签名证书就足够了。

相对而言，其证书通过 CN 进行声明的群集的安全性源自群集所有者对其证书提供者的隐式信任。 提供者是颁发证书的公钥基础结构 (PKI) 服务。 除其他因素外，信任还基于 PKI 的认证实践、是否由其他受信任方审核和批准其操作安全性，等等。

群集所有者还必须详细了解哪些证书颁发机构 (CA) 在颁发其证书，因为这是按使用者验证证书的基本要求。 这也意味着自签名证书完全不适用。 不夸张地说，任何人都可以生成具有给定使用者的证书。

在以下情况下，由 CN 声明的证书通常被视为有效证书：

* 它的链可以成功生成。
* 使用者具有预期的 CN 元素。
* 执行验证的代理信任其颁发者（链中的直接颁发者或更高级别颁发者）。

Service Fabric 支持以两种方式通过 CN 声明证书：

* 使用隐式颁发者，这意味着链必须以信任定位点结束。
* 使用由指纹声明的颁发者（称为颁发者固定）。

有关详细信息，请参阅[基于公用名的证书验证声明](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)。

若要使用由指纹声明的自签名证书将群集转换为 CN（目标），必须先通过指纹将 CA 签名的证书引入到群集中。 只有这样才可以从指纹转换为 CN。

对于测试用途，自签名证书可以由 CN 声明，但前提是颁发者已固定到其自己的指纹。 从安全角度来看，此操作几乎相当于通过指纹声明相同的证书。 此类型的成功转换不保证能够使用 CA 签名的证书成功地从指纹转换为 CN。 建议使用正确的由 CA 签名的证书来测试转换。 此测试存在免费选项。

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>上传证书并将其安装在规模集中

在 Azure 中，用于获取和预配证书的推荐机制涉及 Azure Key Vault 及其工具。 与群集证书声明匹配的证书必须预配到构成群集的虚拟机规模集的每个节点。 有关详细信息，请参阅[虚拟机规模集上的机密](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#how-do-i-securely-ship-a-certificate-to-the-vm-)。

在群集的证书声明中进行更改之前，必须在群集的每个节点类型的虚拟机上安装当前群集证书和目标群集证书。 [证书的旅程](cluster-security-certificate-management.md#the-journey-of-a-certificate)中深入讨论了从证书颁发到预配到 Service Fabric 节点中这一段旅程。

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>使群集进入最佳起始状态

将证书声明从基于指纹转换为基于 CN 将影响：

- 群集中的每个节点如何查找其凭据并将其提供给其他节点。
- 建立安全连接后，每个节点如何验证其对应节点的凭据。

继续操作之前，请查看[两种配置的表示和验证规则](cluster-security-certificates.md#certificate-configuration-rules)。 执行从指纹到 CN 的转换时，最重要的注意事项是已升级的和尚未升级的节点（即属于不同升级域的节点）必须能够在升级过程中的任何时候执行成功的相互身份验证。 实现此行为的建议方法是在初始升级时通过指纹声明目标证书。 然后，在后续升级中完成到 CN 的转换。 如果群集已处于建议的起始状态，你可以跳过此部分。

一个转换有多个有效的起始状态。 不变的是，在开始升级到 CN 时，群集已经在使用目标证书（通过指纹声明的）。 在本文中，我们会考虑 `GoalCert`、`OldCert1` 和 `OldCert2`。

#### <a name="valid-starting-states"></a>有效的起始状态

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`，其中的 `GoalCert` 具有比 `OldCert1` 的日期更晚的 `NotBefore` 日期
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`，其中的 `GoalCert` 具有比 `OldCert1` 的日期更晚的 `NotBefore` 日期

> [!NOTE]
> 在版本 7.2.445 (7.2 CU4) 之前，Service Fabric 选择了最后面的即将到期的证书（具有最远的“NotAfter”属性的证书），因此上述 7.2 CU4 之前的起始状态要求 GoalCert 的 `NotAfter` 日期晚于 `OldCert1`

如果你的群集未处于前面所述的有效状态之一，请参阅本文末尾部分关于如何实现该状态的内容。

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>选择所需的基于 CN 的证书验证方案

如前文所述，Service Fabric 支持通过 CN 和隐式信任定位点来声明证书，或者通过 CN 和显式固定颁发者指纹来声明证书。 有关详细信息，请参阅[基于公用名的证书验证声明](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)。

请确保你很好地了解选择任一机制的差别和影响。 从语法上讲，这种差异或选择取决于 `certificateIssuerThumbprintList` 参数的值。 为空表示依赖于受信任的根 CA （信任定位点），而一组指纹会限制允许的群集证书直接颁发者。

   > [!NOTE]
   > 使用 `certificateIssuerThumbprint` 字段，可以指定由使用者 CN 声明的证书的预期直接颁发者。 可接受的值为一个或多个以逗号分隔的 SHA1 指纹。 此操作会改进证书验证。
   >
   > 如果未指定颁发者或列表为空，并且证书链可以生成，则可使用证书进行身份验证。 然后，证书最终出现在验证程序信任的根中。 当指定了一个或多个颁发者指纹时，如果证书的直接颁发者的指纹（从链中提取）与此字段中指定的任意值匹配，则会接受该证书。 无论根是否可信，都会接受该证书。
   >
   > PKI 可能会使用不同的证书颁发机构（也称为“颁发者”）对具有给定使用者的证书进行签名。 因此，请务必为该使用者指定所有预期的颁发者指纹。 换句话说，证书续订不保证要续订的证书会由其颁发者签名。
   >
   > 指定颁发者被认为是最佳做法。 对于可以链接到受信任根的证书，省略颁发者也是可行的，但此行为存在限制，在不久的将来可能会被淘汰。 在 Azure 中部署并使用 X509 证书（由私有 PKI 颁发并通过使用者进行声明）保护的群集可能无法获得 Service Fabric 的验证，因此无法进行从群集到服务的通信。 进行验证时，要求 PKI 的证书策略是可发现的、可用的且可访问的。

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>更新群集的 Azure 资源管理器模板并进行部署

使用 Azure 资源管理器 (ARM) 模板管理 Service Fabric 群集。 另一种方法（也使用 JSON 项目）是 [Azure 资源浏览器（预览版）](https://resources.azure.com)。 Azure 门户目前未提供等效的体验。

如果与现有群集相对应的原始模板不可用，则可以在 Azure 门户中获取等效模板。 转到包含该群集的资源组，然后从左侧的“自动化”菜单中选择“导出模板”。 然后选择所需的资源。 至少应分别导出虚拟机规模集和群集资源。 还可以下载生成的模板。 此模板可能需要更改才能完全部署。 此模板也可能与原始模板不完全匹配。 它反映群集资源的当前状态。

必要的更改如下所示：

- 更新 Service Fabric 节点扩展（在虚拟机资源下）的定义。 如果群集定义了多个节点类型，则需要更新每个相应虚拟机规模集的定义。
- 更新群集资源定义。

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

在 **Microsoft.ServiceFabric/clusters** 资源中，添加一个具有 **commonNames** 设置的 **certificateCommonNames** 属性，然后删除 **certificate** 属性（及其所有设置）。

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

有关详细信息，请参阅[部署使用证书公用名称而非指纹的 Service Fabric 群集](./service-fabric-create-cluster-using-cert-cn.md)。

## <a name="deploy-the-updated-template"></a>部署已更新的模板

在进行更改后，请重新部署已更新的模板。

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>实现将群集转换为基于 CN 的证书声明的有效起始状态

| 开始状态 | 升级 1 | 升级 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 和 `GoalCert` 具有比 `OldCert1` 晚的 `NotBefore` 日期 | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` 和 `OldCert1` 具有比 `GoalCert` 晚的 `NotBefore` 日期 | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`，其中的 `OldCert1` 具有比 `GoalCert` 晚的 `NotBefore` 日期 | 升级到 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`，其中的 `OldCert1` 具有比 `GoalCert` 晚的 `NotBefore` 日期 | 升级到 `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | 删除 `OldCert1` 或 `OldCert2` 以达到状态 `Thumbprint: OldCertx, ThumbprintSecondary: None` | 从新的起始状态继续 |

> [!NOTE]
> 对于 7.2.445 (7.2 CU4) 之前版本上的群集，在上述状态下，将 `NotBefore` 替换为 `NotAfter`。

有关如何执行这些升级中的任一升级的说明，请参阅[管理 Azure Service Fabric 群集中的证书](service-fabric-cluster-security-update-certs-azure.md)。

## <a name="next-steps"></a>后续步骤

* 了解[群集安全性](service-fabric-cluster-security.md)。
* 了解如何[通过公用名滚动更新群集证书](service-fabric-cluster-rollover-cert-cn.md)。
* 了解如何[为无接触自动滚动更新配置群集](cluster-security-certificate-management.md)。

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
