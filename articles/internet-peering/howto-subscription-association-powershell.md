---
title: 使用 PowerShell 将对等 ASN 关联到 Azure 订阅
titleSuffix: Azure
description: 使用 PowerShell 将对等 ASN 关联到 Azure 订阅
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b463293899dc2586404d68145943caff3105e89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590182"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>使用 PowerShell 将对等 ASN 关联到 Azure 订阅

提交对等互连请求之前，应首先使用以下步骤将 ASN 与 Azure 订阅关联。

如果需要，可以使用[门户](howto-subscription-association-portal.md)完成本指南。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>创建 PeerASN 以将 ASN 与 Azure 订阅关联

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，然后选择订阅
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>注册对等互连资源提供程序
使用以下命令在订阅中注册对等资源提供程序。 如果未执行此操作，则设置对等互连所需的 Azure 资源将无法访问。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

可以使用以下命令检查注册状态：
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> 等待“RegistrationState”变为“Registered”，然后再继续。 执行命令后，可能需要 5 到 30 分钟的等待时间。

### <a name="update-the-peer-information-associated-with-this-subscription"></a>更新与此订阅关联的对等体信息

下面是一个更新对等体信息的示例。

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Name 对应于资源名称，可以是所选择的任何内容。 但是，-peerName 对应于公司名称，需要尽可能与 PeeringDB 配置文件接近。 请注意，-peerName 仅支持字符 a-z、A-Z 和空格。

一个订阅可以有多个 ASN。 更新每个 ASN 的对等互连信息。 请确保每个 ASN 的“name”都独一无二。

对等体应在 [PeeringDB](https://www.peeringdb.com) 上具有完整且最新的配置文件。 在注册过程中，我们使用这些信息来验证对等体的详细信息（例如 NOC 信息、技术联系信息以及它们在对等设施中是否存在，等等）。

请注意，将替换上面输出中的 {subscriptionId}，显示实际的订阅 ID。

## <a name="view-status-of-a-peerasn"></a>查看 PeerASN 的状态

使用以下命令检查 ASN 验证状态：

```powershell
Get-AzPeerAsn
```

下面是示例响应：
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> 在提交对等互连请求之前，等待 ValidationState 变成“Approved”。 此批准最多可能需要 12 小时。

## <a name="modify-peerasn"></a>修改 PeerAsn
可以随时修改 NOC 联系信息。

下面是一个示例：

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>删除 PeerAsn
当前不支持删除 PeerASN。 如果需要删除 PeerASN，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。

## <a name="next-steps"></a>后续步骤

* [创建或修改直接对等互连](howto-direct-powershell.md)
* [将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-powershell.md)
* [创建或修改 Exchange 对等互连](howto-exchange-powershell.md)
* [将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问 [Internet 对等互连常见问题解答](faqs.md)
