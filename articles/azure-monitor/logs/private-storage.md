---
title: 在 Azure Monitor Log Analytics 中使用客户管理的存储帐户
description: 为 Log Analytics 方案使用自己的存储帐户
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: b8fa3c329afe26979e6f557d075aff478e79d10e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727226"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>在 Azure Monitor Log Analytics 中使用客户管理的存储帐户

Log Analytics 在各种方案中依赖于 Azure 存储。 通常自动管理这种用法。 但是，某些情况要求你提供和管理你自己的存储帐户（也称为客户管理的存储帐户）。 本文档介绍了如何将客户管理的存储用于 WAD/LAD 日志、专用链接和客户管理的密钥 (CMK) 加密。 

> [!NOTE]
> 建议不要与 Log Analytics 上传到客户管理的存储的内容有依赖关系，考虑到格式化可能会使内容发生更改。

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>引入 Azure 诊断扩展日志 (WAD/LAD)
Azure 诊断扩展代理（也分别称为适用于 Windows 的 WAD 代理和适用于 Linux 的 LAD 代理）收集各种操作系统日志，并将它们存储在客户管理的存储帐户中。 之后，你可以将这些日志引入 Log Analytics 来查看和分析它们。
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>如何从存储帐户收集 Azure 诊断扩展日志
使用 [Azure 门户](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage)或通过调用[存储见解 API](/rest/api/loganalytics/storage-insights/create-or-update)，将存储帐户作为存储数据源连接到 Log Analytics 工作区。

支持的数据类型：
* Syslog
* Windows 事件
* Service Fabric
* ETW 事件
* IIS 日志

## <a name="using-private-links"></a>使用专用链接
当使用专用链接连接到 Azure Monitor 资源时，客户管理的存储帐户用于引入自定义日志或 IIS 日志。 这些数据类型的引入过程是首先将日志上传到中间 Azure 存储帐户，然后才将其引入到工作区。 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>通过专用链接使用客户管理的存储帐户
#### <a name="workspace-requirements"></a>工作区要求
通过专用链接连接到 Azure Monitor 时，Log Analytics 代理只能将日志发送到通过专用链接访问的工作区。 此要求意味着你应：
* 配置 Azure Monitor 专用链接范围 (AMPLS) 对象
* 将其连接到工作区
* 通过专用链接将 AMPLS 连接到网络。 

有关 AMPLS 配置过程的详细信息，请参阅[使用 Azure 专用链接将网络安全地连接到 Azure Monitor](./private-link-security.md)。 

#### <a name="storage-account-requirements"></a>存储帐户要求
要使存储帐户成功连接到专用链接，该存储帐户必须：
* 位于 VNet 或对等互连网络中，并通过专用链接连接到 VNet。
* 与其所链接到的工作区位于同一区域。
* 允许 Azure Monitor 访问存储帐户。 如果选择仅允许选定网络访问存储帐户，则应选择例外：“允许受信任的 Microsoft 服务访问此存储帐户”。
![存储帐户信任 MS 服务映像](./media/private-storage/storage-trust.png)
* 如果工作区也处理来自其他网络的流量，则应将存储帐户配置为允许来自相关网络/Internet 的传入流量。
* 在代理与存储帐户之间协调 TLS 版本 - 建议使用 TLS 1.2 或更高版本将数据发送到 Log Analytics。 查看[特定于平台的指南](./data-security.md#sending-data-securely-using-tls-12)，并[将代理配置为使用 TLS 1.2](../agents/agent-windows.md#configure-agent-to-use-tls-12)（如果需要）。 如果由于某种原因无法实现此目的，请将存储帐户配置为接受 TLS 1.0。

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>使用客户管理的存储帐户进行 CMK 数据加密
Azure 存储可对存储帐户中的所有数据进行静态加密。 默认情况下，它使用 Microsoft 托管密钥 (MMK) 来加密数据；但是，Azure 存储还允许你使用 Azure 密钥保管库中的 CMK 来加密存储数据。 可以将自己的密钥导入 Azure 密钥保管库中，也可以使用 Azure 密钥保管库 API 来生成密钥。
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>需要客户管理的存储帐户的 CMK 方案
* 使用 CMK 加密日志警报查询
* 使用 CMK 加密保存的查询

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>如何将 CMK 应用于客户管理的存储帐户
##### <a name="storage-account-requirements"></a>存储帐户要求
存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。 有关 Azure 存储加密和密钥管理的详细信息，请参阅[静态数据的 Azure 存储加密](../../storage/common/storage-service-encryption.md)。

##### <a name="apply-cmk-to-your-storage-accounts"></a>将 CMK 应用于存储帐户
若要将 Azure 存储帐户配置为将 CMK 与 Azure 密钥保管库一起使用，请使用 [Azure 门户](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)、[PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) 或 [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)。 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>将存储帐户链接到 Log Analytics 工作区
### <a name="using-the-azure-portal"></a>使用 Azure 门户
在 Azure 门户中打开“工作区”菜单，选择“链接的存储帐户”。 随即打开一个边栏选项卡，其中显示了上述用例中链接的存储帐户（通过专用链接进行数据引入，将 CMK 应用到已保存的查询或警报）。
![“链接的存储帐户”边栏选项卡图像](./media/private-storage/all-linked-storage-accounts.png)在边栏选项卡中选择一项将打开其存储帐户的详细信息，你可以在其中设置或更新此类型的链接的存储帐户。 
![链接存储帐户边栏选项卡图像](./media/private-storage/link-a-storage-account-blade.png)如果需要，你可以将同一帐户用于不同用例。

### <a name="using-the-azure-cli-or-rest-api"></a>使用 Azure CLI 或 REST API
你也可以通过 [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) 或 [REST API](/rest/api/loganalytics/linkedstorageaccounts) 将存储帐户链接到工作区。

适用的 dataSourceType 值包括：
* CustomLogs – 将存储帐户用于自定义日志和 IIS 日志引入
* Query - 将存储帐户用于存储已保存的查询（CMK 加密所需）
* Alerts - 将存储帐户用于存储基于日志的警报（CMK 加密所需）


## <a name="managing-linked-storage-accounts"></a>管理链接的存储帐户

### <a name="create-or-modify-a-link"></a>创建或修改链接
将存储帐户链接到工作区后，Log Analytics 将开始使用该存储帐户，而不是使用服务所拥有的存储帐户。 可以 
* 注册多个存储帐户以分散它们之间的日志负载
* 为多个工作区重用同一个存储帐户

### <a name="unlink-a-storage-account"></a>取消存储帐户链接
若要停止使用存储帐户，请取消存储与工作区的链接。 取消所有存储帐户与工作区之间的链接意味着 Log Analytics 将尝试依赖服务托管存储帐户。 如果网络对 Internet 的访问权限有限，则这些存储可能不可用，且任何依赖于存储的方案都将失败。

### <a name="replace-a-storage-account"></a>替换存储帐户
若要替换用于引入数据的存储帐户，
1.  **创建与新存储帐户的链接。** 日志记录代理将获取更新配置，并也会开始将数据发送到新的存储。 此过程可能需要几分钟时间。
2.  **之后取消与旧存储帐户的链接，以便代理停止写入已删除的帐户。** 数据引入过程将一直读取此帐户中的数据，直到全部引入。 在所有日志引入完之前，请不要删除存储帐户。

### <a name="maintaining-storage-accounts"></a>维护存储帐户
#### <a name="manage-log-retention"></a>管理日志保留期
使用自己的存储帐户时，保留期取决于你。 Log Analytics 不会删除专用存储中存储的日志。 但你应根据偏好设置策略来处理负载。

#### <a name="consider-load"></a>考虑负载
存储帐户可以处理一定数量的读取和写入请求负载，之后会开始限制请求（有关详细信息，请参阅 [Blob 存储的可伸缩性和性能目标](../../storage/common/scalability-targets-standard-account.md)）。 限制请求会延长引入日志所花费的时间。 如果存储帐户已超载，请注册额外的存储帐户，以在帐户之间分散负载。 若要监视存储帐户的容量和性能，请查看其在 [Azure 门户中的见解](../../storage/common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)。

### <a name="related-charges"></a>相关费用
存储帐户按存储数据量、存储类型和冗余类型收费。 有关详细信息，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs)和[表存储定价](https://azure.microsoft.com/pricing/details/storage/tables)。


## <a name="next-steps"></a>后续步骤

- 了解有关[使用 Azure 专用链接将网络安全地连接到 Azure Monitor](private-link-security.md) 的信息
- 了解有关 [Azure Monitor 客户管理的密钥](../logs/customer-managed-keys.md)的信息