---
title: 为 Azure VMware 解决方案配置 DNS 转发器
description: 了解如何使用 Azure 门户 为 Azure VMware 解决方案 配置 DNS 转发器。
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/15/2021
ms.openlocfilehash: 4dbf13234f29eb572519f6975e152f22b7093543
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323943"
---
# <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>在 Azure 门户中配置 DNS 转发器

>[!IMPORTANT]
>对于 2021 年 7 月 1 日或之后创建的 Azure VMware 解决方案私有云，现在能够配置专用 DNS 解析。 对于在 2021 年 7 月 1 日之前创建的需要专用 DNS 解析的私有云，请开启[支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)并请求专用 DNS 配置。 

默认情况下，Azure VMware 解决方案管理组件（如 vCenter）只能解析通过公共 DNS 提供的名称记录。 但是，某些混合用例要正常运行，需要 Azure VMware 解决方案管理组件来解析来自专用托管 DNS 的名称记录，包括客户管理的系统（例如，vCenter 和 Active Directory）。

Azure VMware 解决方案管理组件的专用 DNS 让你能够通过 NSX-T DNS 服务为所选专用 DNS 服务器集的所需域名定义条件转发规则。 

此功能使用 NSX-T 中的 DNS 转发器服务。 DNS 服务和默认 DNS 区域作为私有云的一部分提供。 为使 Azure VMware 解决方案管理组件能够解析来自专用 DNS 系统的记录，必须定义 FQDN 区域，并将其应用于 NSX-T DNS 服务。 DNS 服务根据该区域中定义的外部 DNS 服务器，有条件地转发每个区域的 DNS 查询。

>[!NOTE]
>DNS 服务最多与 5 个 FQDN 区域关联。 每个 FQDN 区域最多与 3 个 DNS 服务器关联。

>[!TIP]
>如果需要，还可通过将工作负载段上的虚拟机配置为使用 NSX-T DNS 服务 IP 地址作为 DNS 服务器，为这些段使用条件转发规则。


## <a name="architecture"></a>体系结构

该图显示 NSX-T DNS 服务可以将 DNS 查询转发到 Azure 和本地环境中托管的 DNS 系统。

:::image type="content" source="media/networking/dns/dns-forwarder-diagram.png" alt-text="图表：NSX-T DNS 服务可以将 DNS 查询转发到 Azure 和本地环境中托管的 DNS 系统。" border="false":::


## <a name="configure-dns-forwarder"></a>配置 DNS 转发器

1. 在 Azure VMware 解决方案私有云的“工作负载网络”下，选择“DNS” > “DNS 区域”  。 然后选择“添加”  。

   >[!NOTE]
   >对于在 2021 年 7 月 1 日或之后创建的私有云，会在私有云创建期间创建默认 DNS 区域。

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-1.png" alt-text="屏幕截图：如何将 DNS 区域添加到 Azure VMware 解决方案私有云。":::

1. 选择“FQDN 区域”，提供一个名称以及最多三个 DNS 服务器 IP 地址（格式为“10.0.0.53”） 。 然后选择“确定”。 

   :::image type="content" source="media/networking/dns/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="屏幕截图：添加 FQDN 区域所需的信息。":::

   >[!IMPORTANT]
   >虽然 NSX-T 允许 DNS 区域名称中存在空格和其他非字母数字字符，但某些 NSX 资源（如 DNS 区域）会映射到不允许在名称中使用特定字符的 Azure 资源。 
   >
   >因此，可能需要根据 [Azure 资源命名约定](../azure-resource-manager/management/resource-name-rules.md#microsoftresources)来调整某些 DNS 区域名称，使其在 NSX-T 中有效。

      需要几分钟才能完成这一操作，可以根据“通知”中的进度进行操作。 创建 DNS 区域后，“通知”中会显示一条消息。

1. 忽略有关默认 DNS 区域的消息，因为已在私有云中为你创建了一个该区域。

1. 选择“DNS 服务”选项卡，选择“添加” 。

   >[!TIP]
   >对于在 2021 年 7 月 1 日或之后创建的私有云，可以忽略有关默认 DNS 区域的消息，因为私有云创建期间会创建一个默认 DNS 区域。


   >[!IMPORTANT]
   >虽然私有云中的某些操作可能从 NSX-T Manager 执行，但对于在 2021 年 7 月 1 日或之后创建的私有云，若要对默认 Tier-1 网关做出任何配置更改，必须通过 Azure 门户中的“简化网络”体验来编辑 DNS 服务。  

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-2.png" alt-text="屏幕截图：已选择“编辑”按钮的“DNS 服务”选项卡。":::   

1. 从“FQDN 区域”下拉列表中，选择新创建的 FQDN，然后选择“确定” 。

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-3.png" alt-text="屏幕截图：为 DNS 服务选择的 FQDN。":::

   完成操作需要几分钟时间，完成后，你将在“通知”中看到“已完成”消息。 此时，私有云中的管理组件应该能够解析提供给 NSX-T DNS 服务的 FQDN 区域中的 DNS 条目。 

1. 对其他 FQDN 区域重复上述步骤（包括任何适用的反向查找区域）。


## <a name="verify-name-resolution-operations"></a>验证名称解析操作

配置 DNS 转发器后，可以使用几个选项来验证名称解析操作。 

### <a name="nsx-t-manager"></a>NSX-T Manager

NSX-T Manager 提供全局服务级别的每个区域的 DNS 转发器服务统计信息。 

1. 在 NSX-T Manager 中，选择“网络” > “DNS”，然后展开 DNS 转发器服务 。

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services.png" alt-text="屏幕截图：NSX-T Manager 中的“DNS 服务”选项卡。":::

1. 选择“查看统计信息”，然后从“区域统计信息”下拉列表中选择你的 FQDN 区域 。

   上半部分显示整个服务的统计信息，下半部分显示指定区域的统计信息。 在此示例中，可以看到对 FQDN 区域配置期间指定的 DNS 服务的转发查询。

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services-statistics.png" alt-text="屏幕截图：DNS 转发器统计信息。":::


### <a name="powercli"></a>PowerCLI

NSX-T 策略 API 让你可以从 NSX-T DNS 转发器服务运行 nslookup 命令。 所需的 cmdlet 是 PowerCLI 中的 `VMware.VimAutomation.Nsxt` 模块的一部分。 以下示例演示版本 12.3.0 的该模块的输出。

1. 连接到 NSX-T 服务器。 

   >[!TIP]
   >可以从 Azure 门户中的“管理” > “标识”下获取 NSX-T 服务器的 IP 地址 。
 
   ```powershell
   Connect-NsxtServer -Server 10.103.64.3
   ```

1. 获取 DNS 转发器的 nslookup 服务的代理。

   ```powershell
   $nslookup = Get-NsxtPolicyService -Name com.vmware.nsx_policy.infra.tier_1s.dns_forwarder.nslookup
   ```

1. 从 DNS 转发器服务执行查找。

   ```powershell
   $response = $nslookup.get('TNT86-T1', 'vc01.contoso.corp')
   ```

  命令的第一个参数是私有云的 T1 网关的 ID，可以从 Azure 门户中的“DNS 服务”选项卡获取它。

1. 使用响应的以下属性从查找中获取原始答案。

   ```powershell
   $response.dns_answer_per_enforcement_point.raw_answer; (()) DiG 9.10.3-P4-Ubuntu (()) @10.103.64.192 -b 10.103.64.192 vc01.contoso.corp +timeout=5 +tries=3 +nosearch ; (1 server found) ;; global options: +cmd ;; Got answer: ;; -))HEADER((- opcode: QUERY, status: NOERROR, id: 10684 ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1  ;; OPT PSEUDOSECTION: ; EDNS: version: 0, flags:; udp: 4096 ;; QUESTION SECTION: ;vc01.contoso.corp.  IN A  ;; ANSWER SECTION: vc01.contoso.corp. 3046 IN A 172.21.90.2  ;; Query time: 0 msec ;; SERVER: 10.103.64.192:53(10.103.64.192) ;; WHEN: Thu Jul 01 23:44:36 UTC 2021 ;; MSG SIZE  rcvd: 62
   ```

   此示例中，可以看到针对 vc01.contoso.corp 查询的回复，其中显示了地址为 172.21.90.2 的 A 记录。 此示例还显示来自 DNS 转发器服务的缓存响应，因此输出可能会略有不同。
