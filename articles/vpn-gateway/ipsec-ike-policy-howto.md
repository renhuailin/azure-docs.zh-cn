---
title: 用于 S2S VPN 连接和 VNet 到 VNet 连接的 IPsec/IKE 策略：Azure 门户
titleSuffix: Azure VPN Gateway
description: 使用 Azure 资源管理器和 Azure 门户通过 Azure VPN 网关为 S2S 连接或 VNet 到 VNet 连接配置 IPsec/IKE 策略。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: 2b298185866d16da02fe8d3b3fdb41f0b0b1f726
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878538"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>为 S2S VPN 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略：Azure 门户

本文分步讲解了如何使用 Azure 门户为 VPN 网关的站点到站点 VPN 连接或 VNet 到 VNet 连接配置 IPsec/IKE 策略。 以下各部分帮助你创建和配置 IPsec/IKE 策略，并将该策略应用于新的或现有的连接。

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>关于 IPsec 和 IKE 策略参数

IPsec 和 IKE 协议标准支持采用各种组合的各种加密算法。 请参阅[关于加密要求和 Azure VPN 网关](vpn-gateway-about-compliance-crypto.md)，了解如何据此确保跨界的和 VNet 到 VNet 的连接满足符合性或安全性要求。

本文说明了如何创建和配置 IPsec/IKE 策略，并将其应用于新的或现有的 VPN 网关连接。

### <a name="considerations"></a>注意事项

* IPsec/IKE 策略仅适用于以下网关 SKU：
  * VpnGw1~5 和 VpnGw1AZ~5AZ
  * *Standard 和 HighPerformance*
* 一个给定的连接只能指定一个策略组合。
* 必须指定 IKE（主模式）和 IPsec（快速模式）的所有算法和参数。 不允许指定部分策略。
* 请查阅 VPN 设备供应商规范，确保本地 VPN 设备支持该策略。 如果策略不兼容，则无法建立 S2S 或 VNet 到 VNet 的连接。

## <a name="workflow"></a><a name ="workflow"></a>工作流

本部分概述了对 S2S VPN 或 VNet 到 VNet 的连接创建和更新 IPsec/IKE 策略的工作流程：

1. 创建虚拟网络和 VPN 网关。
2. 创建用于跨界连接的本地网关，或用于 VNet 到 VNet 的连接的另一虚拟网络和网关。
3. 创建连接（IPsec 或 VNet2VNet）。
4. 在连接资源上配置/更新/删除 IPsec/IKE 策略。

可以根据本文中的说明，按下图所示设置和配置 IPsec/IKE 策略：

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 策略图示" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>支持的加密算法和密钥强度

### <a name="algorithms-and-keys"></a><a name ="table1"></a>算法和密钥

下表列出了支持的加密算法和密钥强度，客户可自行配置：

| **IPsec/IKE**    | **选项**    |
| ---              | ---            |
| IKE 加密   | AES256、AES192、AES128、DES3、DES                  |
| IKE 完整性    | SHA384、SHA256、SHA1、MD5                          |
| DH 组         | DHGroup24、ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、无 |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、无    |
| IPsec 完整性  | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5 |
| PFS 组        | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、无   |
| QM SA 生存期   | （**可选**：如果未指定，则使用默认值）<br>秒（整数；至少为 300 秒/默认为 27000 秒）<br>KB（整数；至少为 1024 KB/默认为 102400000 KB）    |
| 流量选择器 | UsePolicyBasedTrafficSelectors**（$True/$False; **可选**，如果未指定，则使用默认值 $False）    |
| DPD 超时      | 秒（整数：最小值为 9/最大值为 3600；默认值为 45 秒） |
|  |  |

#### <a name="important-requirements"></a>重要的要求

* 本地 VPN 设备配置必须匹配，或者必须包含可在 Azure IPsec/IKE 策略中指定的以下算法和参数：
  * IKE 加密算法（主模式 / 阶段 1）
  * IKE 完整性算法（主模式 / 阶段 1）
  * DH 组（主模式 / 阶段 1）
  * IPsec 加密算法（快速模式 / 阶段 2）
  * IPsec 完整性算法（快速模式 / 阶段 2）
  * PFS 组（快速模式/阶段 2）>    * 流量选择器（如果使用了 UsePolicyBasedTrafficSelectors）
  * SA 生存期是本地规范，不需匹配。

* 如果使用 GCMAES 作为 IPsec 加密算法，则必须选择相同的 GCMAES 算法和密钥长度以保证 IPsec 完整性，例如对这两者使用 GCMAES128。

* 在上面的[算法和密钥表](#table1)中：
  * IKE 对应于主模式或阶段 1
  * IPsec 对应于快速模式或阶段 2
  * DH 组指定在主模式或阶段 1 中使用的 Diffie-Hellmen 组
  * PFS 组指定在快速模式或阶段 2 中使用的 Diffie-Hellmen 组

* 在 Azure VPN 网关上，IKE 主模式 SA 生存期固定为 28,800 秒。

* 如果你在连接上将 **UsePolicyBasedTrafficSelectors** 设置为 $True，则它将配置 Azure VPN 网关，以连接到基于策略的本地 VPN 防火墙。 如果启用 PolicyBasedTrafficSelectors，则需确保对于本地网络（本地网关）前缀与 Azure 虚拟网络前缀的所有组合，VPN 设备都定义了与之匹配的流量选择器（而不是任意到任意）。 例如，如果本地网络前缀为 10.1.0.0/16 和 10.2.0.0/16，虚拟网络前缀为 192.168.0.0/16 和 172.16.0.0/16，则需指定以下流量选择器：
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   有关基于策略的流量选择器的详细信息，请参阅[连接多个基于策略的本地 VPN 设备](vpn-gateway-connect-multiple-policybased-rm-ps.md)。

* DPD 超时 - 在 Azure VPN 网关上，默认值为 45 秒。 将超时设置为较短的时长会导致 IKE 更主动地重新生成密钥，导致连接在某些实例中好像是断开的。 如果你的本地位置离 VPN 网关所在的 Azure 区域较远，或者物理链接条件可能会导致数据包丢失，则你可能不希望出现这种情况。 一般建议是将超时设置为 **30 到 45** 秒。

### <a name="diffie-hellman-groups"></a>Diffie-Hellman 组

下表列出了自定义策略支持的相应 Diffie-Hellman 组：

| **Diffie-Hellman 组**  | **DHGroup**              | **PFSGroup** | **密钥长度** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 位 MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 位 MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 位 MODP  |
| 19                        | ECP256                   | ECP256       | 256 位 ECP    |
| 20                        | ECP384                   | ECP384       | 384 位 ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 位 MODP  |

如需更多详细信息，请参阅 [RFC3526](https://tools.ietf.org/html/rfc3526) 和 [RFC5114](https://tools.ietf.org/html/rfc5114)。

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>具有 IPsec/IKE 策略的 S2S VPN

本部分分步介绍了如何创建采用 IPsec/IKE 策略的站点到站点 VPN 连接。 以下步骤将创建以下图示中所示的连接：

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="站点到站点策略" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>步骤 1 - 创建虚拟网络、VPN 网关和本地网关

创建以下资源，如以下屏幕截图所示。 有关步骤，请参阅[创建站点到站点 VPN 连接](./tutorial-site-to-site-portal.md)。

* **虚拟网络：** TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="VNet":::

* **VPN 网关：** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="网关":::

* **本地网络网关：** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="站点":::

* **Connection：** VNet1 到 Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="连接":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>步骤 2 - 在 S2S VPN 连接上配置 IPsec/IKE 策略

在本部分中，你将配置采用以下算法和参数的 IPsec/IKE 策略：

* IKE： AES256、SHA384、DHGroup24，DPD 超时 45 秒
* IPsec：AES256、SHA256、PFS 无、SA 生存期 30000 秒和 102400000KB

1. 在 Azure 门户中，导航到连接资源 **VNet1toSite6**。 选择“配置”页，然后选择“自定义”IPsec/IKE 策略以显示所有配置选项。 下面的屏幕截图根据列表显示配置：

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Site6":::

1. 如果将 GCMAES 用于 IPsec，必须为 IPsec 加密和完整性使用相同的 GCMAES 算法和密钥长度。 例如，下面的屏幕截图为 IPsec 加密和 IPsec 完整性都指定了 GCMAES128：

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="IPsec 的 GCMAES":::

1. 你还可以为“使用基于策略的流量选择器”选项选择“启用”，使 Azure VPN 网关能够连接到基于策略的本地 VPN 设备，如上所述。

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="基于策略的流量选择器":::

1. 选择所有选项后，选择“保存”以提交对连接资源所做的更改。 策略将在大约一分钟内实施。

> [!IMPORTANT]
>
> * 对连接指定 IPsec/IKE 策略后，Azure VPN 网关将仅发送或接收对特定连接采用指定的加密算法和密钥强度的 IPsec/IKE 提议。 确保连接的本地 VPN 设备使用或接受确切策略组合，否则无法建立 S2S VPN 隧道。
>
> * “基于策略的流量选择器”和“DPD 超时”选项可以随 **默认** 策略来指定，无需使用自定义 IPsec/IKE 策略，如上面的屏幕截图所示。
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>具有 IPsec/IKE 策略的 VNet 到 VNet 连接

创建采用 IPsec/IKE 策略的 VNet 到 VNet 连接时，其步骤与创建 S2S VPN 连接的步骤类似。

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="VNet 到 VNet 策略图示" border="false":::

1. 请使用[创建 VNet 到 VNet 连接](vpn-gateway-vnet-vnet-rm-ps.md)一文中的步骤创建 VNet 到 VNet 连接。

2. 完成这些步骤后，你会看到两个 VNet 到 VNet 连接，如以下屏幕截图中的 VNet2GW 资源所示：

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="VNet 到 VNet 连接":::

3. 在门户上导航到连接资源，然后转到“配置”页面。 在“IPsec/IKE 策略”上选择“自定义”以显示自定义策略选项。 选择具有相应密钥长度的加密算法。

   屏幕截图显示了采用以下算法和参数的另一个 IPsec/IKE 策略：
   * IKE：AES128、SHA1、DHGroup14、DPD 超时 45 秒
   * IPsec：GCMAES128、GCMAES128、PFS14、SA Lifetime 14400 seconds & 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="连接策略":::

4. 选择“保存”以应用对连接资源的策略更改。

5. 将同一策略应用于另一连接资源 VNet2toVNet1。 如果不应用，IPsec/IKE VPN 隧道会因为策略不匹配而无法连接。

   > [!IMPORTANT]
   > 对连接指定 IPsec/IKE 策略后，Azure VPN 网关将仅发送或接收对特定连接采用指定的加密算法和密钥强度的 IPsec/IKE 提议。 确保两个连接的 IPsec 策略相同，否则无法建立 VNet 到 VNet 的连接。

6. 完成这些步骤后，将在几分钟内建立连接，你会得到以下网络拓扑：

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 策略图示" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>从连接中删除自定义 IPsec/IKE 策略

1. 若要从连接中删除某个自定义策略，请导航到连接资源并转到“配置”页面来查看当前策略。

2. 为“IPsec/IKE 策略”选项选择“默认值”。 这将删除之前在连接上指定的所有自定义策略，并在此连接上还原默认的 IPsec/IKE 设置：

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="删除策略":::

3. 选择“保存”以在连接上删除自定义策略，并还原默认 IPsec/IKE 设置。

## <a name="next-steps"></a>后续步骤

有关基于策略的流量选择器的详细信息，请参阅[连接多个基于策略的本地 VPN 设备](vpn-gateway-connect-multiple-policybased-rm-ps.md)。