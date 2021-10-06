---
title: 排查自承载集成运行时问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 管道中的自承载集成运行时问题。
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 09/09/2021
ms.author: lle
ms.openlocfilehash: d1b3770d236c7f88090840720e8f88fd453e70cf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755931"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>排查自承载集成运行时问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨 Azure 数据工厂和 Synapse 工作区中的自承载集成运行时 (IR) 的常用故障排除方法。

## <a name="gather-self-hosted-ir-logs"></a>收集自承载 IR 日志

对于在自承载 IR 或共享 IR 上运行的失败活动，此服务支持查看和上传错误日志。 要获取错误报表 ID，请按照此处的说明操作，然后输入报表 ID 以搜索相关的已知问题。

1. 在服务 UI 的“监视”页上，选择“管道运行”。

1. 在“活动运行”下的“错误”列中，选择突出显示的按钮以显示活动日志，如以下屏幕截图所示：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)
    
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png" alt-text="“所有管道运行”&quot;&quot;窗格上的“活动运行”&quot;&quot;部分的屏幕截图。":::
    
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page-synapse.png" alt-text="“所有管道运行”&quot;&quot;窗格上的“活动运行”&quot;&quot;部分的屏幕截图。":::
    
    ---
    
    将显示失败的活动运行的活动日志。
    
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png" alt-text="失败活动的活动日志的屏幕截图。"::: 
    
3. 要获得进一步的帮助，请选择“发送日志”。
 
   将打开“与 Microsoft 共享自承载集成运行时 (IR) 日志”窗口。

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png" alt-text="“与 Microsoft 共享自承载集成运行时 (IR) 日志”&quot;&quot;窗口的屏幕截图。":::

1. 选择要发送的日志。 
    * 对于“自承载 IR”，你可以上传与失败的活动相关的日志或自承载 IR 节点上的所有日志。 
    * 对于“共享 IR”，只能上传与失败的活动相关的日志。

1. 上传日志时，如果需要进一步的帮助以解决问题，请保留报表 ID 的记录供以后使用。

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png" alt-text="IR 日志的上传进度窗口中显示的报表 ID 的屏幕截图。":::

> [!NOTE]
> 日志查看和上传请求在所有联机自承载 IR 实例上执行。 如果缺少任何日志，请确保所有自承载 IR 实例都处于联机状态。 


## <a name="self-hosted-ir-general-failure-or-error"></a>自承载 IR 一般性故障或错误

### <a name="out-of-memory-issue"></a>内存不足问题

#### <a name="symptoms"></a>症状

尝试使用链接 IR 或自承载 IR 运行查找活动时，出现 OutOfMemoryException (OOM) 错误。

#### <a name="cause"></a>原因

如果 IR 计算机瞬时内存占用量高，新活动可能会引发 OOM 错误。 该问题可能是由大量的并发活动导致的，该错误是设计使然。

#### <a name="resolution"></a>解决方法

检查 IR 节点上的资源使用情况和并发活动执行情况。 调整活动运行的内部和触发器时间，以避免在单个 IR 节点上同时执行太多操作。

### <a name="concurrent-jobs-limit-issue"></a>并发作业限制问题

#### <a name="symptoms"></a>症状

尝试增加 UI 的并发作业限制时，相关进程将以“正在更新”状态挂起。

示例场景：并发作业数的最大值当前设置为 24，而你希望增加该值，使作业运行速度更快。 可输入的最小值为 3，可输入的最大值为 32。 将值从 24 增加到 32，然后选择“更新”按钮。 此进程停滞在“正在更新”状态，如下面的屏幕截图所示。 刷新页面，该值仍显示为 24。 它未如你预期的那样更新为 32。

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png" alt-text="集成运行时“节点”窗格的屏幕截图，其中显示了进程停滞在“正在更新”&quot;&quot;状态。":::

#### <a name="cause"></a>原因

并发作业数的限制取决于计算机的逻辑核心和内存。 尝试将值下调（如调整为 24），然后查看结果。

> [!TIP] 
> -    若要详细了解逻辑核心计数以及如何确定计算机的逻辑核心计数，请参阅[在 Windows 10 上查找 CPU 中的核心数的四种方法](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)。
> -    要了解如何计算 math.log，请转到[对数计算器](https://www.rapidtables.com/calc/math/Log_Calculator.html)。


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>自承载 IR 高可用性 (HA) SSL 证书问题

#### <a name="symptoms"></a>症状

自承载 IR 工作节点已报告以下错误：

“从主节点 net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/ 拉取共享状态失败。 活动 ID:XXXXX X.509 证书 CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft 链生成失败。 所使用的证书具有无法验证的信任链。 请替换该证书或更改 certificateValidationMode。 吊销功能无法检查吊销，因为吊销服务器已脱机。”

#### <a name="cause"></a>原因

当你处理与 SSL/TLS 握手相关的事例时，可能会遇到一些与证书链验证相关的问题。 

#### <a name="resolution"></a>解决方法

- 下面是对 X.509 证书链生成失败进行故障排除的快速且直观的方法：
 
    1. 导出需要验证的证书。 为此，请执行以下操作：
    
       a. 在 Windows 中，选择“开始”，开始键入“证书”，然后选择“管理计算机证书”  。
       
       b. 在文件资源管理器的左侧窗格中，搜索要检查的证书，右键单击它，然后选择“所有任务” > “导出” 。
    
        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png" alt-text="“管理计算机证书”&quot;&quot;窗格中证书的“所有任务” > &quot;&quot;“导出”&quot;&quot;控件的屏幕截图。":::

    2. 将导出的证书复制到客户端计算机。 
    3. 在客户端的命令提示符窗口中运行以下命令。 请务必将 \<certificate path> 和 \<output txt file path> 替换为实际路径 。
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        例如：

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. 在 TXT 输出文件中检查是否有错误。 可在 TXT 文件末尾找到错误摘要。

        例如： 

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png" alt-text="TXT 文件末尾的错误摘要的屏幕截图。":::

        如果日志文件末尾未显示任何错误（如以下屏幕截图所示），可认为已在客户端计算机中成功生成了证书链。
        
        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png" alt-text="显示没有错误的日志文件的屏幕截图。":::      

- 如果证书文件中配置了 AIA（授权信息访问）、CDP（CRL 分发点）或 OCSP（联机证书状态协议）文件扩展名，可采用更直观的方式查看它：
 
    1. 查看证书详细信息即可获取此信息，如以下屏幕截图所示：
    
        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png" alt-text="证书详细信息的屏幕截图。":::
    
    1. 运行以下命令。 务必将 \<certificate path> 替换为证书的实际路径。
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        URL 检索工具在此时打开。 
        
    1. 要验证带 AIA、CDP 和 OCSP 文件扩展名的证书，请选择“检索”。

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png" alt-text="URL 检索工具和检索按钮的屏幕截图。":::
 
        如果来自 AIA 的证书状态为“已验证”，来自 CDP 或 OCSP 的证书状态也为“已验证”，说明已成功生成证书链 。

        如果在尝试检索 AIA 或 CDP 时出现故障，请与网络团队合作，使客户端计算机可以连接到目标 URL。 如果可以验证 HTTP 路径或轻型目录访问协议 (LDAP) 路径，就足够了。

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>自承载 IR 未能加载文件或程序集

#### <a name="symptoms"></a>症状

您会看到以下错误信息：

“无法加载文件或程序集‘XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX’或其依赖项之一。 系统找不到指定的文件。 活动 ID:92693b45-b4bf-4fc8-89da-2d3dc56f27c3”
 
下面是更为具体的错误消息： 

“无法加载文件或程序集‘System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX’或其依赖项之一。 系统找不到指定的文件。 活动 ID:92693b45-b4bf-4fc8-89da-2d3dc56f27c3”

#### <a name="cause"></a>原因

在进程监视器中，可以看到以下结果：

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox" lightbox="media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png" alt-text="进程监视器中的路径列表的屏幕截图。":::

> [!TIP] 
> 在进程监视器中，你可以按以下屏幕截图所示设置筛选器。
>
> 前面的错误消息告诉我们 DLL System.ValueTuple 不在“全局程序集缓存”(GAC) 文件夹中，不在 C:\Program Files\Microsoft Integration Runtime\4.0\Gateway 文件夹中，也不在 C:\Program Files\Microsoft Integration Runtime\4.0\Shared 文件夹中  。
>
> 简单来说，该进程加载 DLL 的顺序是：首先从 GAC 文件夹加载，再从共享文件夹加载，最后从网关文件夹加载  。 因此，可以从任何有用的路径加载 DLL。

<br>

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png" alt-text="“进程监视器筛选器”&quot;&quot;页的屏幕截图，其中列出了 DLL 的筛选器。":::

#### <a name="resolution"></a>解决方法

可以在 C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan 文件夹中发现 System.ValueTuple.dll 。 若要解决此问题，请将 System.ValueTuple.dll 文件复制到 C:\Program Files\Microsoft Integration Runtime\4.0\Gateway 文件夹中 。

你可以使用相同的方法来解决其他文件或程序集缺失问题。

#### <a name="more-information-about-this-issue"></a>有关此问题的详细信息

System.ValueTuple.dll 位于 %windir%\Microsoft.NET\assembly 和 %windir%\assembly 下，因为这是 .NET 行为  。 

在以下错误中，你可以清楚地了解到 System.ValueTuple 程序集缺失。 应用程序尝试检查 System.ValueTuple.dll 程序集时会出现此问题。
 
"\<LogProperties>\<ErrorInfo>[{"Code":0,"Message":"Npgsql.PoolManager 的类型初始值设定项引发了一个错误。","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"无法加载文件或程序集 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' 或其依赖项之一。 系统找不到指定的文件。","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]\</ErrorInfo>\</LogProperties>"
 
有关 GAC 的详细信息，请参阅[全局程序集缓存](/dotnet/framework/app-domains/gac)。


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>自承载集成运行时身份验证密钥缺失

#### <a name="symptoms"></a>症状

自承载集成运行时在无身份验证密钥的情况下突然脱机，事件日志显示以下错误消息： 

“尚未分配身份验证密钥”

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png" alt-text="显示“尚未分配身份验证密钥”的集成运行时事件窗格的屏幕截图。":::

#### <a name="cause"></a>原因

- 在 Azure 门户中删除了自承载 IR 节点或逻辑自承载 IR。
- 执行了完全卸载。

#### <a name="resolution"></a>解决方法

如果上述两个原因均不适用，你可前往 %programdata%\Microsoft\Data Transfer\DataManagementGateway 文件夹，检查是否删除了 Configurations 文件 。 如果已删除，请按照 Netwrix 文章[检测从 Windows 文件服务器中删除文件的人员](https://www.netwrix.com/how_to_detect_who_deleted_file.html)中的说明进行操作。

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png" alt-text="用于检查 Configurations 文件的事件日志详细信息窗格的屏幕截图。":::


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>无法使用自承载 IR 桥接两个本地数据存储

#### <a name="symptoms"></a>症状

为源数据存储和目标数据存储创建自承载 IR 后，需要连接这两个 IR 以完成复制活动。 如果数据存储是在不同的虚拟网络中配置的，或者数据存储无法理解网关机制，你将收到以下错误消息之一： 

* “在目标 IR 中找不到源的驱动程序”
* “目标 IR 无法访问源”
 
#### <a name="cause"></a>原因

自承载 IR 设计为复制活动的中心节点，而不是需要为每个数据存储安装的客户端代理。
 
在这种情况下，应使用相同的 IR 为每个数据存储创建链接服务，并且 IR 应该能够通过网络访问这两个数据存储。 IR 的安装位置（源数据存储、目标数据存储或第三方计算机）并不重要。 如果两个链接服务是使用不同 IR 创建的，却在同一复制活动中使用，则使用目标 IR，并且你需要在目标 IR 计算机上安装两个数据存储的驱动程序。

#### <a name="resolution"></a>解决方法

在目标 IR 上为源和目标数据存储安装驱动程序，并确保它可访问源数据存储。
 
如果流量无法通过网络在两个数据存储间传递（例如是在两个虚拟网络中配置的这两个数据存储），即使安装了 IR，也不能在一个活动中完成复制。 如果无法在一个活动中完成复制，可以使用两个 IR 创建两个复制活动，分别位于一个 VENT 中： 
* 将一个 IR 从数据存储 1 复制到 Azure Blob 存储
* 将另一个 IR 从 Azure Blob 存储复制到数据存储 2。 

此解决方案可以模拟以下要求：使用 IR 创建一个网桥来连接两个已断开连接的数据存储。


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>凭据同步问题导致凭据从 HA 丢失

#### <a name="symptoms"></a>症状

如果从具有有效负载的当前集成运行时节点中删除了数据源凭据“XXXXXXXXXX”，你将收到以下错误消息：

“删除 Azure 门户上的链接服务，或者任务的有效负载错误时，请再次使用凭据创建新的链接服务。”

#### <a name="cause"></a>原因

自承载 IR 是在 HA 模式下构建的，包含两个节点，但节点不处于凭据同步状态。 这意味着存储在调度程序节点中的凭据不会同步到其他工作器节点。 如果从调度程序节点到工作器节点发生了故障转移，并且凭据仅存在于以前的调度程序节点中，则任务将在尝试访问凭据时失败，并且你将会收到上述错误。

#### <a name="resolution"></a>解决方法

避免此问题的唯一方法是确保两个节点处于凭据同步状态。 如果它们不同步，你必须重新输入新调度程序的凭据。


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>由于私钥缺失而无法选择证书

#### <a name="symptoms"></a>症状

* 你已将 PFX 文件导入到证书存储中。
* 通过 IR 配置管理器 UI 选择证书时，收到以下错误消息：

   “更改 Intranet 通信加密模式失败。 证书 \<*certificate name*> 可能没有能够进行密钥交换的私钥，或者进程可能不具有对私钥的访问权限。 有关详细信息，请参阅内部异常。”

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png" alt-text="“Integration Runtime 配置管理器设置”窗格的屏幕截图，其中显示“私钥缺失”&quot;&quot;错误消息。":::

#### <a name="cause"></a>原因

- 用户帐户的权限级别较低，无法访问私钥。
- 此证书是作为签名证书（而不是密钥交换证书）生成的。

#### <a name="resolution"></a>解决方法

* 若要操作 UI，请使用具有适当权限的帐户访问私钥。  
* 通过运行以下命令导入证书：
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

### <a name="self-hosted-integration-runtime-nodes-out-of-the-sync-issue"></a>自承载集成运行时节点不同步问题

#### <a name="symptoms"></a>症状

自承载集成运行时节点尝试跨节点同步凭据，但在此过程中卡住并在一段时间后遇到以下错误消息：

“集成运行时（自承载）节点正在尝试跨节点同步凭据。 此过程可能需要几分钟时间。”

>[!Note]
>如果此错误出现超过 10 分钟，请检查与调度程序节点的连接。

#### <a name="cause"></a>原因

原因是工作器节点无权访问私钥。 这可以从下面的自承载集成运行时日志中确认：

`[14]0460.3404::05/07/21-00:23:32.2107988 [System] A fatal error occurred when attempting to access the TLS server credential private key. The error code returned from the cryptographic module is 0x8009030D. The internal error state is 10001.`

在链接服务中使用服务主体身份验证时，同步过程没有问题。 但是，将身份验证类型切换为帐户密钥时，同步问题就出现了。 这是因为自承载集成运行时服务在服务帐户 (NT SERVICE\DIAHostService) 下运行，需要将其添加到私钥权限中。
 

#### <a name="resolution"></a>解决方法

若要解决此问题，你需要将自承载集成运行时服务帐户 (NT SERVICE\DIAHostService) 添加到私钥权限中。 你可以应用以下步骤：

1. 打开 Microsoft 管理控制台 (MMC) 运行命令。

    :::image type="content" source="./media/self-hosted-integration-runtime-troubleshoot-guide/management-console-run-command.png" alt-text="显示 MMC 运行命令的屏幕截图":::

1. 在 MMC 窗格中，应用以下步骤：

    :::image type="content" source="./media/self-hosted-integration-runtime-troubleshoot-guide/add-service-account-to-private-key-1.png" alt-text="显示将自承载 IR 服务帐户添加到私钥权限的第二步的屏幕截图。" lightbox="./media/self-hosted-integration-runtime-troubleshoot-guide/add-service-account-to-private-key-1-expanded.png":::

    1. 选择“文件”。
    1. 在下拉菜单中选择“添加/删除管理单元”。
    1. 在“可用管理单元”窗格中选择“证书”。
    1. 选择 **添加** 。
    1. 在弹出的“证书管理单元”窗格中，选择“计算机帐户”。
    1. 选择“**下一页**”。
    1. 在“选择计算机”窗格中，选择“本地计算机: 运行此控制台的计算机”。
    1. 选择“完成”  。
    1. 在“添加或删除管理单元”窗格中选择“确定”。

1. 在 MMC 窗格中，继续执行以下步骤：

    :::image type="content" source="./media/self-hosted-integration-runtime-troubleshoot-guide/add-service-account-to-private-key-2.png" alt-text="显示将自承载 IR 服务帐户添加到私钥权限的第三步的屏幕截图。" lightbox="./media/self-hosted-integration-runtime-troubleshoot-guide/add-service-account-to-private-key-2-expanded.png":::

    1. 从左侧文件夹列表中，选择“控制台根节点 -> 证书(本地计算机) -> 个人 -> 证书”。
    1. 右键单击“Microsoft Intune Beta MDM”。
    1. 在下拉列表中选择“所有任务”。
    1. 选择“管理私钥”。
    1. 在“组或用户名”下选择“添加”。
    1. 选择“NT SERVICE\DIAHostService”，授予其对此证书的完全控制访问权限。 
    1. 选择“检查名称”，然后选择“确定” 。
    1. 在“权限”窗格中，选择“应用”，然后选择“确定”。

## <a name="self-hosted-ir-setup"></a>自承载 IR 设置

### <a name="integration-runtime-registration-error"></a>Integration Runtime 注册错误 

#### <a name="symptoms"></a>症状

出于以下原因之一，你可能偶尔希望在不同的帐户中运行自承载 IR：
- 公司策略不允许使用服务帐户。
- 需要进行一些身份验证。

在服务窗格中更改服务帐户后，你可能会发现集成运行时停止工作，并收到以下错误消息：

“Integration Runtime (自承载)节点在注册期间遇到错误。 无法连接到 Integration Runtime (自承载)主机服务。”

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png" alt-text="“Integration Runtime 配置管理器”窗口的屏幕截图，其中显示 IR 注册错误。":::

#### <a name="cause"></a>原因

许多资源仅授予服务帐户访问。 将服务帐户更改为另一个帐户时，所有从属资源的权限保持不变。

#### <a name="resolution"></a>解决方法

查看 Integration Runtime 事件日志以检查错误。

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png" alt-text="IR 事件日志的屏幕截图，其中显示发生了运行时错误。":::

* 如果事件日志中的错误为“UnauthorizedAccessException”，请执行以下操作：

    1. 在 Windows 服务面板中，勾选 DIAHostService 登录服务帐户。

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png" alt-text="“登录服务帐户属性”窗格的屏幕截图。":::

    1. 检查登录服务帐户是否具有对 %programdata%\Microsoft\DataTransfer\DataManagementGateway 文件夹的读取/写入权限：。

        - 默认情况下，如果未更改服务登录帐户，则它应具有读取/写入权限。

            :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png" alt-text="服务权限窗格的屏幕截图。":::

        - 如果更改了服务登录帐户，请执行以下操作来缓解此问题：
 
            a. 对当前自承载 IR 执行完全卸载。   
            b. 安装自承载 IR 位。  
            c. 通过执行以下操作更改服务帐户：  

             i. 转到自承载 IR 安装文件夹，然后切换到 Microsoft Integration Runtime\4.0\Shared 文件夹。  
             ii. 使用提升的权限打开命令提示符窗口。 将 \<user> 和 \<password> 替换为自己的用户名和密码，然后运行以下命令 ：   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. 如果要更改为 LocalSystem 帐户，请确保为此帐户使用正确的格式：`dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                请勿使用此格式：`dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. （可选）由于 Local System 具有高于管理员的权限，因此你也可以直接在“服务”中进行更改。  
             v. 你可以使用本地/域用户作为 IR 服务登录帐户。            

            d. 注册 Integration Runtime。

* 如果错误为“Integration Runtime 服务 (DIAHostService) 未能启动。 请确保有足够的权限启动系统服务”，请执行以下操作：

    1. 在 Windows 服务面板中，勾选 DIAHostService 登录服务帐户。
    
        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png" alt-text="服务帐户的“登录”&quot;&quot;窗格的屏幕截图。":::

    1. 检查登录服务帐户是否具有用于启动 Windows 服务的“以服务形式登录”权限：

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png" alt-text="“以服务形式登录”&quot;&quot;属性窗格的屏幕截图。":::

#### <a name="more-information"></a>详细信息

如果上述两种解决方法模式都不适用于你的情况，请尝试收集以下 Windows 事件日志： 
- 应用程序和服务日志 > Integration Runtime
- Windows 日志 > 应用程序

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>找不到“注册”按钮来注册自承载 IR    

#### <a name="symptoms"></a>症状

注册自承载 IR 时，“注册”按钮未显示在“配置管理器”窗格中。

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png" alt-text="“配置管理器”窗格的屏幕截图，其中显示一条表明未注册集成运行时节点的消息。":::

#### <a name="cause"></a>原因

自 Integration Runtime 3.0 发行以来，为了实现更干净、更安全的环境，删除了现有集成运行时节点上的“注册”按钮。 如果某个节点已注册到某个集成运行时（无论是否联机），则必须卸载之前的节点，然后安装并注册该节点，才能将该节点重新注册到另一个集成运行时。

#### <a name="resolution"></a>解决方法

1. 在控制面板中，卸载现有的 Integration Runtime。

    > [!IMPORTANT] 
    > 在下面的过程中，选择“是”。 在卸载过程中请勿保留数据。

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png" alt-text="用于从集成运行时删除所有用户数据的 &quot;是&quot; 按钮的屏幕截图。":::

1. 如果没有 Integration Runtime 安装程序 MSI 文件，请转到[下载中心](https://www.microsoft.com/en-sg/download/details.aspx?id=39717)以下载最新的 Integration Runtime。
1. 安装 MSI 文件，并注册 Integration Runtime。


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>因 localhost 而无法注册自承载 IR    

#### <a name="symptoms"></a>症状

使用 get_LoopbackIpOrName 时，无法在新计算机上注册自承载 IR。

**调试：** 出现运行时错误。
“Microsoft.DataTransfer.DIAgentHost.DataSourceCache”的类型初始化表达式引发了异常。
数据库查找期间发生不可恢复的错误。
 
**异常详细信息：** System.TypeInitializationException：“Microsoft.DataTransfer.DIAgentHost.DataSourceCache”的类型初始化表达式引发了异常。 ---> System.Net.Sockets.SocketException：数据库查找 System.Net.Dns.GetAddrInfo(String name) 期间发生不可恢复的错误。

#### <a name="cause"></a>原因

此问题通常在解决 localhost 时出现。

#### <a name="resolution"></a>解决方法

使用 localhost IP 地址 127.0.0.1 托管文件，然后解决问题。

### <a name="self-hosted-setup-failed"></a>自承载设置失败    

#### <a name="symptoms"></a>症状

无法卸载现有 IR，无法安装新 IR，或无法将现有 IR 升级为新 IR。

#### <a name="cause"></a>原因

集成运行时的安装取决于 Windows Installer 服务。 你可能会遇到安装问题，原因如下：
- 可用磁盘空间不足。
- 权限不足。
- Windows NT 服务已锁定。
- CPU 使用率太高。
- MSI 文件托管位置的网络慢。
- 意外改动了某些系统文件或注册表。

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>IR 服务帐户无法获取证书访问权限

#### <a name="symptoms"></a>症状

通过 Microsoft Integration Runtime 配置管理器安装自承载 IR 时，会生成一个包含受信任证书颁发机构 (CA) 的证书。 无法将证书应用于加密两个节点之间的通信，并显示以下错误消息： 

更改 Intranet 通信加密模式失败:未能向 Integration Runtime 服务帐户授予访问证书 "\<*certificate name*>" 的权限。 错误代码 103

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png" alt-text="屏幕截图显示以下错误消息：“…未能向 Integration Runtime 服务帐户授予证书访问权限。”&quot;&quot;":::

#### <a name="cause"></a>原因

证书使用的是尚不受支持的密钥存储提供程序 (KSP) 存储。 到目前为止，自承载 IR 仅支持加密服务提供程序 (CSP) 存储。

#### <a name="resolution"></a>解决方法

在这种情况下，建议使用 CSP 证书。

**解决方案 1** 

若要导入证书，请运行以下命令：

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png" alt-text="用于导入证书的 certutil 命令的屏幕截图。":::

**解决方案 2** 

若要转换证书，请运行以下命令：

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

转换前后：

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png" alt-text="证书转换前的结果的屏幕截图。":::

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png" alt-text="证书转换后的结果的屏幕截图。":::

### <a name="self-hosted-integration-runtime-version-5x"></a>自承载集成运行时版本 5.x
若要升级为自承载集成运行时版本 5.x，我们需要 .NET Framework 运行时 4.7.2 或更高版本。 在下载页中，你会发现最新的 4.x 版本和最新的两个 5.x 版本的下载链接。 

对于 Azure 数据工厂 v2 和 Azure Synapse 客户：
- 如果自动更新已打开，并且你已将 .NET Framework 运行时升级到 4.7.2 或更高版本，则自承载集成运行时会自动升级到最新的 5.x 版本。
- 如果自动更新已打开，并且你尚未将 .NET Framework 运行时升级到 4.7.2 或更高版本，则自承载集成运行时不会自动升级到最新的 5.x 版本。 自承载集成运行时仍然保留为当前的 4.x 版本。 你可以在门户和自承载集成运行时客户端中看到有关 .NET Framework 运行时升级的警告。
- 如果自动更新已关闭，并且你已将 .NET Framework 运行时升级到 4.7.2 或更高版本，则可以手动下载最新的 5.x，并将其安装在计算机上。
- 如果自动更新已关闭，并且你尚未将 .NET Framework 运行时升级到 4.7.2 或更高版本。 则当你尝试手动安装自承载集成运行时 5.x 并注册密钥时，你需要先升级 .NET Framework 运行时版本。


对于 Azure 数据工厂 v1 客户：
- 自承载集成运行时 5.X 不支持 Azure 数据工厂 v1。
- 自承载集成运行时将自动升级到最新版本 4.x。 并且最新版本 4.x 不会过期。 
- 如果尝试手动安装自承载集成运行时 5.x 并注册密钥，将收到自承载集成运行时 5.x 不支持 Azure 数据工厂 v1 的通知。


## <a name="self-hosted-ir-connectivity-issues"></a>自承载 IR 连接问题

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>自承载集成运行时无法连接到云服务

#### <a name="symptoms"></a>症状

尝试注册自承载集成运行时的时候，配置管理器显示以下错误消息：

“Integration Runtime (自承载)节点在注册期间遇到错误。”

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png" alt-text="“Integration Runtime (自承载)节点在注册期间遇到错误”消息的屏幕截图。&quot;&quot;":::

#### <a name="cause"></a>原因 

自承载 IR 无法连接到服务后端。 此问题通常是防火墙中的网络设置导致的。

#### <a name="resolution"></a>解决方法

1. 检查集成运行时服务是否正在运行。 如果是，转到步骤 2。
    
   :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png" alt-text="显示自承载 IR 服务正在运行的屏幕截图。":::
    
1. 如果自承载 IR 上没有配置代理（这是默认设置），请在安装了自承载集成运行时的计算机上运行以下 PowerShell 命令：

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```      

   > [!NOTE]     
   > 服务 URL 可能会有所不同，具体取决于数据工厂或 Synapse 工作区实例的位置。 若要查找服务 URL，请使用数据工厂或 Azure Synapse 实例中 UI 的“管理”页查找“集成运行时”，并单击自承载 IR 对其进行编辑。  然后选择“节点”选项卡，并单击“查看服务 URL” 。
            
    下面是预期的响应：
            
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png" alt-text="PowerShell 命令响应的屏幕截图。":::
            
1. 如果未收到预期的响应，请根据情况使用以下方法之一：
            
    * 如果收到“无法解析远程名称”消息，则表明存在域名系统 (DNS) 问题。 请联系你的网络团队来修复此问题。
    * 如果收到“ssl/tls 证书不受信任”消息，请[检查证书](https://wu2.frontend.clouddatahub.net/)在计算机上是否受信任，然后使用证书管理器安装公共证书。 此操作应该会缓解此问题。
    * 转到“Windows” > “事件查看器(日志)” > “应用程序和服务日志” > “Integration Runtime”，检查是否存在由 DNS、防火墙规则或公司网络设置导致的故障   。 如果发现此类故障，请强行关闭连接。 每个公司都有自己自定义的网络设置，因此请与网络团队联系以排查这些问题。

1. 如果在自承载集成运行时上配置了“代理”，请验证代理服务器是否可以访问服务终结点。 有关示例命令，请参阅 [PowerShell, web requests, and proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)（PowerShell、Web 请求和代理）。    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

下面是预期的响应：
            
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png" alt-text="预期的 PowerShell 命令响应的屏幕截图。":::

> [!NOTE] 
> 代理注意事项：
> * 检查是否需要将代理服务器放在“安全接收方”列表中。 如果需要，请确保[这些域](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network)在“安全接收方”列表中。
> * 检查 SSL/TLS 证书“wu2.frontend.clouddatahub.net/”在代理服务器上是否受信任。
> * 如果在代理上使用 Active Directory 身份验证，请将服务帐户更改为可作为“Integration Runtime 服务”访问该代理的用户帐户。

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>错误消息：自承载集成运行时节点/逻辑自承载 IR 处于“非活动”/“正在运行(受限)”状态

#### <a name="cause"></a>原因 

自承载集成运行时节点的状态可能为“非活动”，如以下屏幕截图所示：

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png" alt-text="自承载集成运行时节点处于非活动状态的屏幕截图":::

当节点无法相互通信时，会出现此行为。

#### <a name="resolution"></a>解决方法

1. 登录到节点托管的虚拟机 (VM)。 在“应用程序和服务日志” > “Integration Runtime”下，打开事件查看器并筛选错误日志 。

1. 检查错误日志是否包含以下错误： 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. 如果看到此错误，请在命令提示符窗口中运行以下命令： 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 如果收到以下屏幕截图中显示的命令行错误“无法打开与主机的连接”，请联系 IT 部门帮忙修复此问题。 能成功通过 telnet 连接后，如果集成运行时节点状态仍然存在问题，请联系 Microsoft 支持部门。
        
   :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png" alt-text="“无法打开与主机的连接”命令行错误的屏幕截图。&quot;&quot;":::
        
1. 检查错误日志是否包含以下条目：

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. 若要解决此问题，请尝试下面的一种或两种方法：
    - 将所有节点置于同一域中。
    - 在所有托管 VM 的主机文件中添加 IP 到主机映射。

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-or-azure-synapse-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>自承载 IR 与数据工厂/Azure Synapse 实例，或者自承载 IR 与数据源/接收器之间的连接问题

若要排查网络连接问题，应知道如何收集网络跟踪，了解如何使用它，并在通过自承载 IR 在实际案例中应用 Netmon 工具之前[分析 Microsoft 网络监视器 (Netmon) 跟踪](#analyze-the-netmon-trace)。

#### <a name="symptoms"></a>症状

有时可能需要对自承载 IR 和数据工厂/Azure Synapse 实例之间的某些连接问题进行故障排除（如以下屏幕截图所示），或者对自承载 IR 与数据源或接收器之间的连接问题进行故障排除。 

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png" alt-text="“处理的 HTTP 请求失败”&quot;&quot;消息的屏幕截图":::

在任一种情况中，你都可能会遇到以下错误：

* “复制失败，出现错误: Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=无法连接到 SQL Server:IP 地址”

* “发生了一个或多个错误。 发送请求时出错。 基础连接已关闭:接收时出现意外错误。 无法从传输连接中读取数据:远程主机强行关闭了现有连接。 远程主机活动 ID 强行关闭了现有连接。”

#### <a name="resolution"></a>解决方法

遇到上述错误时，请按照本部分中的说明进行故障排除。

- 收集用于分析的 Netmon 跟踪： 

    1. 你可以设置筛选器来查看从服务器到客户端的任何重置。 在下面的示例屏幕截图中，可以看到服务器端是数据工厂服务器。

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png" alt-text="数据工厂服务器的屏幕截图。":::

    1. 当你获得重置包时，可以按传输控制协议 (TCP) 来查找对话。

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png" alt-text="TCP 对话的屏幕截图。":::

    1. 通过删除筛选器来获取客户端与数据工厂服务器之间的对话。

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png" alt-text="对话详细信息的屏幕截图。":::

- 对已收集的 Netmon 跟踪的分析表明，生存时间 (TTL) 总计为 64。 根据 [IP 生存时间 (TTL) 和跃点限制基础知识](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/)一文中提及的值（摘录在以下列表中），你可以看到是 Linux 系统重置了包并导致连接断开。

    默认 TTL 和跃点限制值在不同的操作系统中有所不同，如下所示：
    - Linux 内核 2.4 (circa 2001)：对于 TCP、用户数据报协议 (UDP) 和 Internet 控制消息协议 (ICMP)，该值为 255
    - Linux 内核 4.10 (2015)：对于 TCP、UDP 和 ICMP，该值为 64
    - Windows XP (2001)：对于 TCP、UDP 和 ICMP，该值为 128
    - Windows 10 (2015)：对于 TCP、UDP 和 ICMP，该值为 128
    - Windows Server 2008：对于 TCP、UDP 和 ICMP，该值为 128
    - Windows Server 2019 (2018)：对于 TCP、UDP 和 ICMP，该值为 128
    - macOS (2001)：对于 TCP、UDP 和 ICMP，该值为 64

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png" alt-text="显示 TTL 值 61 的屏幕截图。":::
    
    在上面的示例中，TTL 显示为 61 而不是 64，因为在网络包到达目标时，它需要经过各个跃点（如路由器或网络设备）。 扣除这些路由器或网络设备的数量以获得最终 TTL。
    
    在本例中，可以看到可从 Linux 系统以 TTL 64 发送重置。

-  若要确认重置设备可能来自何处，请检查从自承载 IR 算起的第四个跃点。
 
    *来自 Linux 系统 A 的网络包的 TTL 64 -> B 的 TTL (64 - 1 = 63) -> C 的 TTL (63 - 1 = 62) -> 自承载 IR 的 TTL (62 - 1 = 61)*

- 在理想情况下，TTL 跃点数为 128，这意味着 Windows 操作系统正在运行你的数据工厂实例。 如以下示例所示，128 减去 107 得 21 个跃点，这意味着在 TCP 3 握手期间，将包的 21 个跃点从数据工厂实例发送到了自承载 IR。
 
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png" alt-text="显示 TTL 值 107 的屏幕截图。":::

    因此，你需要与网络团队合作，以检查从自承载 IR 算起的第四个跃点是什么。 如果它是作为 Linux 系统存在的防火墙，请检查日志来确认设备为何在 TCP 3 握手后重置了包。 
    
    如果你不确定从何处开始着手调查，请尝试在有问题的期间从自承载 IR 和防火墙获取 Netmon 跟踪。 此方法将帮助你确定哪个设备可能重置了包且造成了连接断开。 在这种情况下，你还需要与网络团队合作才能继续进行调查。

### <a name="analyze-the-netmon-trace"></a>分析 Netmon 跟踪

> [!NOTE] 
> 以下说明适用于 Netmon 跟踪。 目前不支持 Netmon 跟踪，可以改为使用 Wireshark。

尝试通过 telnet 连接 8.8.8.8 888 和收集的 Netmon 跟踪时，会看到以下屏幕截图中的跟踪：

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png" alt-text="显示错误消息“无法打开与端口 888 上的主机的连接”的屏幕截图。&quot;&quot;":::

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png" alt-text="显示 Netmon 跟踪的说明的屏幕截图。":::
 

前面的图像显示你无法在端口 888 上与服务器端 8.8.8.8 建立 TCP 连接，因此你会看到两个额外的 SynReTransmit 包  。 由于源 SELF-HOST2 无法通过第一个包连接到 8.8.8.8，它将继续尝试建立连接 。

> [!TIP]
> 若要进行此连接，请尝试以下解决方案：
> 1. 选择“加载筛选器” > “标准筛选器” > “地址” > “IPv4 地址”   。
> 1. 若要应用筛选器，请输入“IPv4.Address == 8.8.8.8”，然后选择“应用” 。 然后应该能看到从本地计算机到目标 8.8.8.8 的通信。

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png" alt-text="显示筛选器地址的屏幕截图。":::
        
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png" alt-text="显示更多筛选器地址的屏幕截图。":::

成功的方案如以下示例所示： 

- 如果可以在没有任何问题的情况下通过 telnet 连接 8.8.8.8 53，则 TCP 3 握手成功，会话以 TCP 4 握手结束。

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png" alt-text="显示成功连接方案的屏幕截图。":::
     
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png" alt-text="显示成功连接方案详细信息的屏幕截图。":::

- 前面的 TCP 3 握手产生以下工作流：

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png" alt-text="TCP 3 握手工作流的关系图。":::
 
- 以下工作流说明了完成会话的 TCP 4 握手：

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png" alt-text="TCP 4 握手详细信息的屏幕截图。":::

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png" alt-text="TCP 4 握手工作流的关系图。"::: 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>有关更新网络配置的 Microsoft 电子邮件通知

你可能会收到以下电子邮件通知，该通知建议于 2020 年 11 月 8 日之前更新网络配置，以允许与 Azure 数据工厂的新 IP 地址进行通信：

   :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png" alt-text="请求更新网络配置的 Microsoft 电子邮件通知的屏幕截图。":::

#### <a name="determine-whether-this-notification-affects-you"></a>确定此通知是否影响你

此通知适用于以下场景：

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>应用场景 1：在公司防火墙后本地运行的自承载集成运行时的出站通信

如何确定你是否受到影响：

- 如果基于完全限定的域名 (FQDN) 定义防火墙规则，且使用[设置防火墙配置和 IP 地址的允许列表](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-addresses)中所述的方法，则你不受影响。

- 如果在企业防火墙上显式启用出站 IP 的允许列表，你会受到影响。

   如果受到影响，请执行以下操作：在 2020 年 11 月 8 日之前，通知网络基础设施团队更新网络配置，以使用最新的数据工厂 IP 地址。 若要下载最新的 IP 地址，请转到[使用可下载的 JSON 文件发现服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>应用场景 2：在客户管理的 Azure 虚拟网络内的 Azure VM 上运行的自承载集成运行时的出站通信

如何确定你是否受到影响：

- 检查在包含自承载集成运行时的专用网络中是否有任何出站网络安全组 (NSG) 规则。 如果没有出站限制，则不会受到影响。

- 如果有出站规则限制，请检查是否是在使用服务标记。 如果在使用服务标记，则不会受到影响。 无需更改或添加任何内容，因为新 IP 范围包含在现有服务标记之内。 

  :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png" alt-text="显示数据工厂作为目标的目标检查的屏幕截图。":::

- 如果在 Azure 虚拟网络的 NSG 规则设置上显式启用出站 IP 地址的允许列表，你会受到影响。

   如果受到影响，请执行以下操作：在 2020 年 11 月 8 日之前，通知网络基础设施团队更新 Azure 虚拟网络配置上的 NSG 规则，以使用最新的数据工厂 IP 地址。 若要下载最新的 IP 地址，请转到[使用可下载的 JSON 文件发现服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>应用场景 3：客户管理的 Azure 虚拟网络中 SSIS Integration Runtime 的出站通信

如何确定你是否受到影响：

- 检查在包含 SQL Server Integration Services (SSIS) Integration Runtime 的专用网络中是否有任何出站 NSG 规则。 如果没有出站限制，则不会受到影响。

- 如果有出站规则限制，请检查是否是在使用服务标记。 如果在使用服务标记，则不会受到影响。 无需更改或添加任何内容，因为新 IP 范围包含在现有服务标记之内。

- 如果在 Azure 虚拟网络的 NSG 规则设置上显式启用出站 IP 地址的允许列表，你会受到影响。

  如果受到影响，请执行以下操作：在 2020 年 11 月 8 日之前，通知网络基础设施团队更新 Azure 虚拟网络配置上的 NSG 规则，以使用最新的数据工厂 IP 地址。 若要下载最新的 IP 地址，请转到[使用可下载的 JSON 文件发现服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>无法建立 SSL/TLS 安全通道的信任关系 

#### <a name="symptoms"></a>症状

自承载 IR 无法连接到 Azure 数据工厂或 Azure Synapse 服务。

当你在 CustomLogEvent 表中检查自承载 IR 事件日志或客户端通知日志时，你会发现以下错误消息：

“The underlying connection was closed:无法建立 SSL/TLS 安全通道的信任关系。 根据验证过程，远程证书无效。”

检查服务的服务器证书最简单方法是在浏览器中打开服务 URL。 例如，在安装了自承载 IR 的计算机上打开[检查服务器证书链接](https://eu.frontend.clouddatahub.net/)，然后查看服务器证书信息。

  :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png" alt-text="Azure 数据工厂服务的“检查服务器证书”窗格的屏幕截图。":::

  :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png" alt-text="用于检查服务器认证路径的窗口的屏幕截图。":::

#### <a name="cause"></a>原因

此问题有两个可能的原因：

- 原因 1：服务的服务器证书的根 CA 在安装了自承载 IR 的计算机上不受信任。 
- 原因 2：你在环境中使用了代理，代理替代了服务的服务器证书，而安装了自承载 IR 的计算机不信任已替换的服务器证书。

#### <a name="resolution"></a>解决方法

- 对于原因 1：请确保服务的服务器证书及其证书链在安装了自承载 IR 的计算机上受信任。
- 对于原因 2：请在自承载 IR 计算机上信任被替换的根 CA，或者将代理配置为不替换服务的服务器证书。

有关在 Windows 上信任证书的详细信息，请参阅[安装受信任的根证书](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate)。

#### <a name="additional-information"></a>其他信息
我们推出了一个新的 SSL 证书，该证书从 DigiCert 签名。 查看 DigiCert 全局根 G2 是否在受信任的根 CA 中。

  :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png" alt-text="显示“受信任的根证书颁发机构”目录中的“DigiCert 全局根 G2”文件夹的屏幕截图。":::

如果此证书不在受信任的根 CA 中，请在[此处下载](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt )。 


## <a name="next-steps"></a>后续步骤

有关故障排除的更多帮助，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](/answers/topics/azure-data-factory.html)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter 中有关数据工厂的信息](https://twitter.com/hashtag/DataFactory)
*  [映射数据流性能指南](concepts-data-flow-performance.md)
