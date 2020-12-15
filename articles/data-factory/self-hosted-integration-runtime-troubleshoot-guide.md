---
title: 在 Azure 数据工厂中排查自承载集成运行时问题
description: 了解如何在 Azure 数据工厂中排查自承载集成运行时问题。
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: lle
ms.openlocfilehash: ccebdbf428180f8ff4ab10dc6007c3ec35a66362
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503567"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>排查自承载集成运行时问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了 Azure 数据工厂中的自承载集成运行时 (IR) 的常见故障排除方法。

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>从 Azure 数据工厂收集自承载 IR 日志

对于在自承载 IR 或共享 IR 上运行的失败活动，Azure 数据工厂支持查看和上载错误日志。 若要获取错误报告 ID，请按照此处的说明操作，然后输入报表 ID 以搜索相关的已知问题。

1. 在数据工厂中，选择 " **管道运行**"。

1. 在 " **活动运行**" 下的 " **错误** " 列中，选择突出显示的按钮以显示活动日志，如以下屏幕截图所示：

    !["所有管道运行" 窗格上的 "活动运行" 部分的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    将显示失败的活动运行的活动日志。

    ![失败活动的活动日志的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. 要获得进一步的帮助，请选择 " **发送日志**"。
 
   " **共享自承载集成运行时" ("带有 Microsoft 的 IR) 日志** " 窗口打开。

    !["与 Microsoft 共享自承载集成运行时 (IR) 日志" 窗口的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 选择要发送的日志。 
    * 对于 *自承载 ir*，你可以上载与失败的活动相关的日志或自承载 ir 节点上的所有日志。 
    * 对于 *共享 IR*，只能上载与失败的活动相关的日志。

1. 当上传日志时，如果需要进一步的帮助来解决问题，请保留报表 ID 的记录供以后使用。

    ![红外日志的 "上载进度" 窗口中显示的报表 ID 的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> 日志查看和上载请求在所有联机的自承载 IR 实例上执行。 如果缺少任何日志，请确保所有自承载 IR 实例都处于联机状态。 


## <a name="self-hosted-ir-general-failure-or-error"></a>自承载 IR 一般性故障或错误

### <a name="out-of-memory-issue"></a>内存不足问题

#### <a name="symptoms"></a>症状

尝试使用链接的 IR 或自承载 IR 运行查找活动时，会发生 OutOfMemoryException (OOM) 错误。

#### <a name="cause"></a>原因

如果 IR 计算机的内存使用率过高，则新活动可能会引发 OOM 错误。 此问题可能是由大量并发活动引起的，并且错误是由设计决定的。

#### <a name="resolution"></a>解决方法

检查 IR 节点上的资源使用情况和并发活动执行情况。 调整活动运行的内部和触发时间，以避免在同一时间对单个 IR 节点执行太多的操作。


### <a name="ssltls-certificate-issue"></a>SSL/TLS 证书问题

#### <a name="symptoms"></a>症状

尝试启用安全套接字层 (SSL) /传输层安全 (TLS) 证书 (高级) 选择证书后，选择 "**自承载 IR (**  >  **从 intranet Configuration Manager 远程访问**") ，会收到以下错误：

"远程访问设置无效。 传出消息的标识检查失败。 远程终结点的预期 DNS 标识是 "abc.microsoft.com"，但是远程终结点提供的 DNS 声明为 "microsoft.com"。 如果这是合法的远程终结点，则可以通过在创建通道代理时将 DNS 标识 "microsoft.com" 显式指定为 EndpointAddress 的 "标识" 属性来解决此问题。

在前面的示例中，所选证书后面追加了 "microsoft.com"。

#### <a name="cause"></a>原因

这是 Windows Communication Foundation (WCF) 的已知问题。 WCF SSL/TLS 验证仅检查 " **使用者备用名称** " (SAN) "字段中的最后一个 DNSName。 

#### <a name="resolution"></a>解决方法

Azure 数据工厂 v2 自承载 IR 支持通配符证书。 发生此问题的原因通常是 SSL 证书不正确。 SAN 中的最后一个 DNSName 应该是有效的。 

若要验证并更正 DNSName，请执行以下操作： 

1. 打开管理控制台。
1. 在 " **证书详细信息**" 下，仔细检查 " **使用者** 和 **使用者可选名称** " 框中的值。 例如，"DNS Name = microsoft.com.com" 不是有效的名称。
1. 请与证书颁发公司联系，以删除不正确的 DNSName。

### <a name="concurrent-jobs-limit-issue"></a>并发作业限制问题

#### <a name="symptoms"></a>症状

尝试增加 Azure 数据工厂接口的并发作业限制时，该进程将在 " *正在更新* " 状态下挂起。

示例方案：最大并发作业值当前设置为24，你需要增加计数，以便作业能够更快地运行。 可以输入的最小值为3，最大值为32。 将值从24增加到32，然后选择 " **更新** " 按钮。 此过程会停滞在 *更新* 状态，如以下屏幕截图所示。 刷新页面后，该值仍显示为24。 它未按预期更新为32。

![集成运行时 "节点" 窗格的屏幕截图，显示进程停滞在 "正在更新" 状态。](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>原因

并发作业数的限制取决于计算机的逻辑核心和内存。 尝试将值向下调整到值（如24），然后查看结果。

> [!TIP] 
> - 若要详细了解逻辑核心计数，并确定计算机的逻辑核心计数，请参阅 [在 Windows 10 上查找 CPU 中的内核数的四种方法](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)。
> - 若要了解如何计算 math，请参阅 [对数计算器](https://www.rapidtables.com/calc/math/Log_Calculator.html)。


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>自承载 IR 高可用性 (HA) SSL 证书问题

#### <a name="symptoms"></a>症状

自承载 IR 工作节点报告了以下错误：

"无法从主节点 net.tcp：//abc.cloud.corp.Microsoft.com： 8060/ExternalService 获取共享状态。 活动 ID： XXXXX x.509 证书 CN = abc。 .com，OU = test，O = Microsoft 链生成失败。 所使用的证书具有无法验证的信任链。 请替换该证书或更改 certificateValidationMode。 吊销功能无法检查吊销，因为吊销服务器已脱机。 "

#### <a name="cause"></a>原因

当你处理与 SSL/TLS 握手相关的事例时，你可能会遇到一些与证书链验证相关的问题。 

#### <a name="resolution"></a>解决方法

- 下面是对 x.509 证书链生成失败进行故障排除的快速、直观的方式：
 
    1. 导出需要验证的证书。 为此，请执行以下操作：
    
       a. 在 Windows 中，选择 " **开始**"，开始键入 " **证书**"，然后选择 " **管理计算机证书**"。
       
       b. 在文件资源管理器的左窗格中，搜索要检查的证书，右键单击该证书，然后选择 "**所有任务**" "  >  **导出**"。
    
        !["管理计算机证书" 窗格上的证书的 "所有任务" > "导出" 控件的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. 将导出的证书复制到客户端计算机。 
    3. 在客户端上的命令提示符窗口中运行以下命令。 请确保将和替换为 *\<certificate path>* *\<output txt file path>* 实际路径。
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        例如：

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. 在输出 TXT 文件中检查是否有错误。 可以在 TXT 文件末尾找到错误摘要。

        例如： 

        ![TXT 文件末尾的错误摘要的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        如果在日志文件的末尾看不到错误（如以下屏幕截图所示），则可以考虑在客户端计算机上已成功生成证书链。
        
        ![显示没有错误的日志文件的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- 如果在证书文件中配置了 AIA (颁发机构信息访问) 、CDP (CRL 分发点) 或 OCSP (联机证书状态协议) 文件扩展名，则可以通过更直观的方式对其进行检查：
 
    1. 通过检查证书详细信息获取此信息，如以下屏幕截图所示：
    
        ![证书详细信息的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. 运行以下命令。 请确保将替换为 *\<certificate path>* 证书的实际路径。
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        此时将打开 URL 检索工具。 
        
    1. 若要验证具有 AIA、CDP 和 OCSP 文件扩展名的证书，请选择 " **检索**"。

        ![URL 检索工具和 "检索" 按钮的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        如果 *验证* 了 AIA 的证书状态并 *验证* 了 CDP 或 OCSP 的证书状态，则已成功生成证书链。

        如果尝试检索 AIA 或 CDP 时失败，请与网络团队合作，使客户端计算机可以连接到目标 URL。 如果可以验证 HTTP 路径或轻型目录访问协议 (LDAP) 路径，则足够。

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>自承载 IR 未能加载文件或程序集

#### <a name="symptoms"></a>症状

您会看到以下错误信息：

"无法加载文件或程序集" XXXXXXXXXXXXXXXX，Version = 4.0.2.0，Culture = 中立，PublicKeyToken = XXXXXXXXX "或其依赖项之一。 系统找不到指定的文件。 活动 ID： 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "
 
下面是更为具体的错误消息： 

"无法加载文件或程序集" ValueTuple，Version = 4.0.2.0，Culture = 中立，PublicKeyToken = XXXXXXXXX "或其依赖项之一。 系统找不到指定的文件。 活动 ID： 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>原因

在进程监视器中，可以查看以下结果：

[![进程监视器中的路径列表屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> 在进程监视器中，可以设置筛选器，如以下屏幕截图所示。
>
> 前面的错误消息指出，DLL ValueTuple 不在相关的 *全局程序集缓存* (GAC) 文件夹、 *C:\Program Files\Microsoft integration Runtime\4.0\Gateway* 文件夹或 *C:\Program Files\Microsoft integration Runtime\4.0\Shared* 文件夹中。
>
> 基本上，该进程首先从 *GAC* 文件夹、从 *共享* 文件夹到 *网关* 文件夹加载 DLL。 因此，可以从任何有用的路径加载 DLL。

<br>

!["进程监视器筛选器" 页的屏幕截图，其中列出了 DLL 的筛选器。](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>解决方法

你将在 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* 文件夹中找到 *System.ValueTuple.dll* 文件。 若要解决此问题，请将 *System.ValueTuple.dll* 文件复制到 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* 文件夹中。

您可以使用相同的方法解决其他缺少的文件或程序集问题。

#### <a name="more-information-about-this-issue"></a>有关此问题的详细信息

在 *%windir%\Microsoft.NET\assembly* 和 *%windir%\assembly* 下看到 *System.ValueTuple.dll* 的原因是这是 .net 行为。 

出现以下错误时，可以清楚地看到缺少 *ValueTuple* 程序集。 当应用程序尝试检查 *System.ValueTuple.dll* 程序集时，会出现此问题。
 
" \<LogProperties> \<ErrorInfo> [{" Code "：0，" Message "：" "Npgsql" 的类型初始值设定项引发了异常。 "，" 事件类型 "：0，" 类别 "：5，" Data "： {} ，" MsgId "： Null，" ExceptionType "：" system.typeinitializationexception "，" Source "：" Npgsql "，" StackTrace "：" "，" InnerEventInfos "： [{" 代码 "：0，" Message "：" 无法加载文件或程序集 "ValueTuple，Version = 4.0.2.0，Culture = 中性，PublicKeyToken = XXXXXXXXX" 或其依赖项之一。 系统找不到指定的文件。 "，" 事件类型 "：0，" 类别 "：5，" Data "： {} ，" MsgId":null，"ExceptionType":"System system.io.filenotfoundexception "，" Source "：" Npgsql "，" StackTrace "：" "，" InnerEventInfos "： []}]}] \</ErrorInfo> \</LogProperties> "
 
有关 GAC 的详细信息，请参阅 [全局程序集缓存](https://docs.microsoft.com/dotnet/framework/app-domains/gac)。


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>缺少自承载集成运行时身份验证密钥

#### <a name="symptoms"></a>症状

自承载集成运行时在没有身份验证密钥的情况下突然脱机，事件日志显示以下错误消息： 

"尚未分配身份验证密钥"

![集成运行时事件窗格的屏幕截图，显示尚未分配身份验证密钥。](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>原因

- 已删除 Azure 门户中的自承载 IR 节点或逻辑自承载 IR。
- 执行了干净卸载。

#### <a name="resolution"></a>解决方法

如果上述原因均不适用，可以访问 *%Programdata%\Microsoft\Data Transfer\DataManagementGateway* 文件夹来查看 *配置* 文件是否已删除。 如果删除了该文件，请按照 Netwrix 文章 [检测从 Windows 文件服务器中删除文件的文档](https://www.netwrix.com/how_to_detect_who_deleted_file.html)中的说明进行操作。

![用于检查配置文件的 "事件日志详细信息" 窗格的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>无法使用自承载 IR 桥接两个本地数据存储

#### <a name="symptoms"></a>症状

创建源和目标数据存储的自承载 IRs 后，需要连接两个 IRs 来完成复制活动。 如果数据存储是在不同的虚拟网络中配置的，或者数据存储不能了解网关机制，则会收到以下错误之一： 

* "在目标 IR 中找不到源的驱动程序"
* "目标 IR 无法访问源"
 
#### <a name="cause"></a>原因

自承载 IR 设计为复制活动的中心节点，而不是需要为每个数据存储安装的客户端代理。
 
在这种情况下，应为具有相同 IR 的每个数据存储创建链接服务，并且 IR 应该能够通过网络访问这两个数据存储。 无论是在源数据存储还是在目标数据存储或第三台计算机上安装 IR 都不重要。 如果使用不同的 IRs 创建两个链接的服务，但在同一复制活动中使用该服务，则使用目标 IR，并需要在目标 IR 计算机上安装这两个数据存储的驱动程序。

#### <a name="resolution"></a>解决方法

在目标 IR 上安装源和目标数据存储的驱动程序，并确保它可以访问源数据存储。
 
如果流量无法通过网络在两个数据存储之间传递 (例如，它们在两个虚拟网络) 中进行配置，即使安装了 IR，也无法在一个活动中完成复制。 如果无法在单个活动中完成复制，则可以创建两个包含两个 IRs 的复制活动，每个活动都在一张通风管中： 
* 将数据存储1中的一个 IR 复制到 Azure Blob 存储
* 将另一个 IR 从 Azure Blob 存储复制到 ddatastore 2。 

此解决方案可以模拟使用 IR 创建连接两个已断开连接的数据存储的桥的要求。


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>凭据同步问题导致 HA 凭据丢失

#### <a name="symptoms"></a>症状

如果从当前集成运行时节点中删除了负载为的数据源凭据 "XXXXXXXXXX"，则会收到以下错误消息：

"删除 Azure 门户上的链接服务或该任务具有错误的负载时，请重新创建包含凭据的新链接服务。"

#### <a name="cause"></a>原因

自承载 IR 在 HA 模式下生成，其中包含两个节点，但节点未处于凭据同步状态。 这意味着，存储在调度程序节点中的凭据不会同步到其他辅助角色节点。 如果从调度程序节点到辅助角色节点发生故障转移，并且凭据仅存在于以前的调度程序节点中，则当你尝试访问凭据时任务将失败，并且你会收到前面的错误。

#### <a name="resolution"></a>解决方法

避免此问题的唯一方法是确保两个节点处于凭据同步状态。 如果它们不同步，则必须重新输入新调度程序的凭据。


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>由于缺少私钥，因此无法选择证书

#### <a name="symptoms"></a>症状

* 已将 PFX 文件导入到证书存储中。
* 通过 IR Configuration Manager UI 选择证书时，收到以下错误消息：

   "无法更改 intranet 通信加密模式。 证书 " \<*certificate name*> " 可能没有能够进行密钥交换的私钥，或者该进程可能没有私钥的访问权限。 有关详细信息，请参阅内部异常。 "

    ![Integration Runtime Configuration Manager 设置 "窗格的屏幕截图，显示" 缺少私钥 "错误消息。](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>原因

- 用户帐户具有低特权级别，无法访问私钥。
- 此证书是作为签名生成的，而不是作为密钥交换。

#### <a name="resolution"></a>解决方法

* 若要操作 UI，请使用具有适当权限的帐户访问私钥。  
* 通过运行以下命令导入证书：
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>自承载 IR 设置

### <a name="integration-runtime-registration-error"></a>集成运行时注册错误 

#### <a name="symptoms"></a>症状

有时可能需要在不同的帐户中运行自承载 IR，原因如下：
- 公司策略不允许使用服务帐户。
- 需要进行一些身份验证。

在服务窗格中更改服务帐户后，你可能会发现集成运行时停止工作，并收到以下错误消息：

"Integration Runtime (自承载) 节点在注册过程中遇到错误。 无法连接到 (自承载) 主机服务的 Integration Runtime。 "

![显示 IR 注册错误的 Integration Runtime Configuration Manager "窗口的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>原因

许多资源仅授予服务帐户。 当你将服务帐户更改为另一个帐户时，所有从属资源的权限将保持不变。

#### <a name="resolution"></a>解决方法

请中转到 integration runtime 事件日志来检查错误。

![红外事件日志的屏幕截图，显示出现运行时错误。](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* 如果事件日志中的错误为 "System.unauthorizedaccessexception"，请执行以下操作：

    1. 检查 Windows 服务面板中的 *DIAHostService* logon service 帐户。

        !["登录服务帐户属性" 窗格的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. 检查登录服务帐户是否对 *%programdata%\Microsoft\DataTransfer\DataManagementGateway* 文件夹具有读/写权限。

        - 默认情况下，如果服务登录帐户尚未更改，则它应具有读/写权限。

            !["服务权限" 窗格的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - 如果更改了服务登录帐户，请通过执行以下操作来缓解此问题：
 
            a. 执行当前自承载 IR 的完全卸载。   
            b. 安装自承载 IR 位。  
            c. 通过执行以下操作更改服务帐户：  

             i. 转到自承载 IR 安装文件夹，然后切换到 *Microsoft Integration Runtime\4.0\Shared* 文件夹。  
             ii. 使用提升的权限打开命令提示符窗口。 *\<user>* 将和替换 *\<password>* 为你自己的用户名和密码，并运行以下命令：   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. 如果要更改为 LocalSystem 帐户，请确保对此帐户使用正确的格式： `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                不要 *使用此* 格式： `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. （可选）因为本地系统具有比管理员更高的权限，所以你还可以在 "服务" 中直接更改。  
             v. 你可以使用 IR 服务登录帐户的本地/域用户。            

            d. 注册集成运行时。

* 如果错误为 "服务" Integration Runtime 服务 " (DIAHostService) 无法启动。 请确保您有足够的权限来启动系统服务，"执行以下操作：

    1. 检查 Windows 服务面板中的 *DIAHostService* logon service 帐户。
    
        ![服务帐户的 "登录" 窗格的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. 检查登录服务帐户是否具有启动 Windows 服务的 **"作为服务登录"** 权限：

        !["作为服务登录" 属性窗格的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>详细信息

如果前面的两种解决模式均适用于你的情况，请尝试收集以下 Windows 事件日志： 
- 应用程序和服务日志 > Integration Runtime
- Windows 日志 > 应用程序

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>找不到注册按钮来注册自承载 IR    

#### <a name="symptoms"></a>症状

注册自承载 IR 时，" **注册** " 按钮不会显示在 "Configuration Manager" 窗格中。

!["Configuration Manager" 窗格的屏幕截图，显示集成运行时节点未注册的消息。](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>原因

从 3.0 Integration Runtime 版本起，已删除现有 Integration Runtime 节点上的 " **注册** " 按钮，以启用更清晰和更安全的环境。 如果某个节点已注册到 integration runtime，无论该节点是否处于联机状态，都可以通过卸载上一个节点将其重新注册到另一个集成运行时，然后安装并注册该节点。

#### <a name="resolution"></a>解决方法

1. 在 "控制面板" 中，卸载现有的集成运行时。

    > [!IMPORTANT] 
    > 在下面的过程中，选择 **"是"**。 不要在卸载过程中保留数据。

    ![用于从集成运行时中删除所有用户数据的 "是" 按钮的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. 如果没有集成运行时安装程序 MSI 文件，请使用 [下载中心](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) 下载最新的集成运行时。
1. 安装 MSI 文件并注册集成运行时。


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>由于 localhost，无法注册自承载 IR    

#### <a name="symptoms"></a>症状

使用 get_LoopbackIpOrName 时，无法在新计算机上注册自承载 IR。

**调试：** 出现运行时错误。
“Microsoft.DataTransfer.DIAgentHost.DataSourceCache”的类型初始化表达式引发了异常。
数据库查找期间发生不可恢复的错误。
 
**异常详细信息：** System.TypeInitializationException：“Microsoft.DataTransfer.DIAgentHost.DataSourceCache”的类型初始化表达式引发了异常。 ---> System.Net.Sockets.SocketException：数据库查找 System.Net.Dns.GetAddrInfo(String name) 期间发生不可恢复的错误。

#### <a name="cause"></a>原因

此问题通常在正在解析 localhost 时出现。

#### <a name="resolution"></a>解决方法

使用 localhost IP 地址127.0.0.1 来托管文件并解决问题。

### <a name="self-hosted-setup-failed"></a>自承载设置失败    

#### <a name="symptoms"></a>症状

无法卸载现有 IR、安装新的 IR，或将现有 IR 升级到新的 IR。

#### <a name="cause"></a>原因

集成运行时安装取决于 Windows Installer 服务。 由于以下原因，你可能会遇到安装问题：
- 可用磁盘空间不足。
- 权限不足。
- Windows NT 服务已锁定。
- CPU 利用率过高。
- MSI 文件托管在慢速网络位置。
- 某些系统文件或注册表被意外接触。

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>IR 服务帐户无法获取证书访问权限

#### <a name="symptoms"></a>症状

通过 Microsoft Integration Runtime Configuration Manager 安装自承载 IR 时，将生成具有受信任证书颁发机构 (CA) 的证书。 无法应用证书来加密两个节点之间的通信，并显示以下错误消息： 

"无法更改 Intranet 通信加密模式：未能向 Integration Runtime 服务帐户授予对证书" "的访问权限 \<*certificate name*> 。 错误代码 103 "

![显示错误消息 ".。。未能授予 Integration Runtime 服务帐户证书访问权限 "。](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>原因

证书正在使用密钥存储提供程序 (KSP) 存储，该提供程序尚不受支持。 迄今为止，自承载 IR 仅支持加密服务提供程序 (CSP) 存储。

#### <a name="resolution"></a>解决方法

建议在这种情况下使用 CSP 证书。

**解决方案 1** 

若要导入证书，请运行以下命令：

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![用于导入证书的 certutil 命令的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**解决方案 2** 

若要转换证书，请运行以下命令：

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

转换前后：

![在证书转换之前的结果的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![证书转换后的结果的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>自承载集成运行时版本1。x
对于升级到 Azure 数据工厂自承载集成运行时的版本4.x，我们需要 **.NET Framework 运行时 4.7.2** 或更高版本。 在下载页上，可以找到最新的4.x 版本和最新的两个版本的下载链接。 

对于 Azure 数据工厂 v2 客户：
- 如果启用了自动更新，并且已经将 .NET Framework 运行时升级到4.7.2 或更高版本，则自承载集成运行时将自动升级到最新的4.x 版本。
- 如果启用了自动更新，但尚未将 .NET Framework 运行时升级到4.7.2 或更高版本，则自承载集成运行时将不会自动升级到最新的4.x 版本。 自承载集成运行时将保留当前的4.x 版本。 你可以在门户和自承载集成运行时客户端中看到 .NET Framework 运行时升级的警告。
- 如果自动更新已关闭，并且已将 .NET Framework 运行时升级到4.7.2 或更高版本，则可以手动下载最新的6.x，并将其安装在计算机上。
- 如果自动更新已关闭，并且尚未将 .NET Framework 运行时升级到4.7.2 或更高版本。 当你尝试手动安装自承载集成运行时1.x 并注册密钥时，你将需要先升级 .NET Framework 运行时版本。


对于 Azure 数据工厂 v1 客户：
- 自承载集成运行时6.x 不支持 Azure 数据工厂 v1。
- 自承载集成运行时将自动升级到最新版本的4.x。 最新版本的4.x 不会过期。 
- 如果尝试手动安装自承载集成运行时1.x 并注册密钥，则会通知你自承载集成运行时的版本 5. x 不支持 Azure 数据工厂 v1。


## <a name="self-hosted-ir-connectivity-issues"></a>自承载 IR 连接问题

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>自承载集成运行时无法连接到云服务

#### <a name="symptoms"></a>症状

尝试注册自承载集成运行时时，Configuration Manager 会显示以下错误消息：

"Integration Runtime (自承载) 节点在注册过程中遇到错误。"

!["Integration Runtime (自承载) 节点在注册过程中遇到错误" 消息的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

自承载的 IR 无法连接到 Azure 数据工厂服务后端。 此问题通常是由防火墙中的网络设置导致的。

#### <a name="resolution"></a>解决方法

1. 检查集成运行时服务是否正在运行。 如果是，请跳到步骤2。
    
   ![显示自承载 IR 服务正在运行的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 如果在自承载 IR （这是默认设置）上未配置代理，请在安装了自承载集成运行时的计算机上运行以下 PowerShell 命令：

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 服务 URL 可能会有所不同，具体取决于数据工厂实例的位置。 若要查找服务 URL，请选择 " **ADF UI**  >  **连接**  >  **集成运行时**" "  >  **编辑自承载 IR**  >  **节点**" "  >  **查看服务 url**"。
            
    下面是预期的响应：
            
    ![PowerShell 命令响应的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 如果未收到预期的响应，请根据需要使用下列方法之一：
            
    * 如果收到“无法解析远程名称”消息，则表明存在域名系统 (DNS) 问题。 请与网络团队联系以解决此问题。
    * 如果收到 "ssl/tls 证书不受信任" 消息，请 [检查证书](https://wu2.frontend.clouddatahub.net/) 以查看它是否在计算机上受信任，然后使用证书管理器安装公共证书。 此操作应该会缓解此问题。
    * 请使用 **Windows**  >  **事件查看器 (日志)**  >  **应用程序和服务日志**  >  **Integration Runtime**，并检查是否是由 DNS、防火墙规则或公司网络设置导致的任何故障。 如果发现此类故障，请强行关闭连接。 由于每个公司都有其自己的自定义网络设置，因此请与您的网络团队联系以解决这些问题。

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
            
![预期 Powershell 命令响应的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> 代理注意事项：
> * 检查是否需要将代理服务器放在 "安全收件人" 列表中。 如果需要，请确保[这些域](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network)在“安全接收方”列表中。
> * 查看是否信任代理服务器上的 SSL/TLS 证书 "wu2.frontend.clouddatahub.net/"。
> * 如果在代理上使用 Active Directory 身份验证，请将服务帐户更改为可作为“Integration Runtime 服务”访问该代理的用户帐户。

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>错误消息：自承载集成运行时节点/逻辑自承载 IR 处于非活动/"正在运行 (受限) " 状态

#### <a name="cause"></a>原因 

自承载集成运行时节点的状态可能为 " **非活动**"，如以下屏幕截图所示：

![处于非活动状态的自承载集成运行时节点的屏幕截图](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

当节点无法相互通信时，会出现此行为。

#### <a name="resolution"></a>解决方法

1.  (VM) 登录到节点承载的虚拟机。 在 "**应用程序和服务日志**" 下  >  **Integration Runtime**，打开事件查看器并筛选错误日志。

1. 查看错误日志是否包含以下错误： 
    
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
   
1. 如果收到以下屏幕截图中所示的 "无法打开与主机的连接" 命令行错误，请联系你的 IT 部门以获取帮助以解决此问题。 成功 telnet 后，如果仍然遇到 integration runtime 节点状态问题，请联系 Microsoft 支持部门。
        
   !["无法打开与主机的连接" 命令行错误的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. 查看错误日志是否包含以下条目：

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. 若要解决此问题，请尝试下面的一种或两种方法：
    - 将所有节点置于同一域中。
    - 在所有托管 VM 的主机文件中添加 IP 到主机映射。

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>自承载 IR 与数据工厂实例或自承载 IR 与数据源或接收器之间的连接问题

若要解决网络连接问题，你应该知道如何收集网络跟踪，了解如何使用它，以及如何在将 Netmon 工具应用于自承载 IR 的真实情况下， [分析 Microsoft 网络监视器 (Netmon) 跟踪](#analyze-the-netmon-trace) 。

#### <a name="symptoms"></a>症状

有时，你可能需要对自承载 IR 和数据工厂实例之间的某些连接问题进行故障排除（如以下屏幕截图所示），或在自承载 IR 与数据源或接收器之间进行故障排除。 

!["处理的 HTTP 请求失败" 消息的屏幕截图](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

在任一情况下，你可能会遇到以下错误：

* "复制失败，出现错误：类型 = DataTransfer，消息 = 无法连接到 SQL Server：" IP 地址 "

* 发生了一个或多个错误。 发送请求时出错。 基础连接已关闭：接收时出现意外错误。 无法从传输连接中读取数据：现有连接被远程主机强行关闭。 远程主机活动 ID 强行关闭了现有连接。

#### <a name="resolution"></a>解决方法

如果遇到上述错误，请按照本部分中的说明进行故障排除。

- 收集用于分析的 Netmon 跟踪： 

    1. 你可以设置筛选器，以查看从服务器到客户端的重置。 在以下示例屏幕截图中，可以看到服务器端是数据工厂服务器。

        ![数据工厂服务器的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. 当你获取重置包时，你可以按照传输控制协议 (TCP) 来查找会话。

        ![TCP 会话的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. 通过删除筛选器，获取客户端和数据工厂服务器之间的会话。

        ![会话详细信息的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- 对已收集的 Netmon 跟踪的分析显示，生存时间 (TTL) # A2 总计是64。 按照以下列表中的 " [IP 生存时间 (TTL") 和 "跃点限制基础知识](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) " 一文中提到的值，你可以看到它是用于重置包并导致断开连接的 Linux 系统。

    默认 TTL 和跃点限制值在不同的操作系统之间有所不同，如下所示：
    - Linux 内核 2.4 (大约 2001) ： 255 TCP，用户数据报协议 (UDP) ，Internet 控制消息协议 (ICMP) 
    - Linux 内核 4.10 (2015)：对于 TCP、UDP 和 ICMP，该值为 64
    - Windows XP (2001)：对于 TCP、UDP 和 ICMP，该值为 128
    - Windows 10 (2015)：对于 TCP、UDP 和 ICMP，该值为 128
    - Windows Server 2008：对于 TCP、UDP 和 ICMP，该值为 128
    - Windows Server 2019 (2018)：对于 TCP、UDP 和 ICMP，该值为 128
    - macOS (2001)：对于 TCP、UDP 和 ICMP，该值为 64

    ![显示 TTL 值61的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    在前面的示例中，TTL 显示为61，而不是64，因为当网络包到达其目标时，它需要经过各种跃点，如路由器或网络设备。 将扣减路由器或网络设备的数量以生成最终的 TTL。
    
    在这种情况下，可以看到，可以从 Linux 系统使用 TTL 64 发送重置。

-  若要确认重置设备可能来自何处，请从自承载 IR 检查第四个跃点。
 
    *带有 TTL 64 的 Linux 系统 A 中的网络包-> B TTL 64 减 1 = 63-> C TTL 63 减 1 = 62-> TTL 62 减 1 = 61 自承载 IR*

- 在理想情况下，TTL 跃点数为128，表示 Windows 操作系统正在运行你的数据工厂实例。 如下面的示例中所示， *128 减去 107 = 21 个跃点*，这意味着在 TCP 3 握手期间，将包的21个跃点从数据工厂实例发送到自承载 IR。
 
    ![显示 TTL 值107的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    因此，你需要与网络团队合作，以查看第四个跃点是自承载 IR 的跃点。 如果它是防火墙（与 Linux 系统相同），请检查所有日志以了解设备在 TCP 3 握手后重置包的原因。 
    
    如果你不确定要调查到何处，请在问题时间内尝试从自承载 IR 和防火墙获取 Netmon 跟踪。 此方法可帮助你确定哪个设备可能已重置包并导致断开连接。 在这种情况下，你还需要与网络团队合作才能继续进行调查。

### <a name="analyze-the-netmon-trace"></a>分析 Netmon 跟踪

> [!NOTE] 
> 以下说明适用于 Netmon 跟踪。 由于 Netmon 跟踪当前不支持，因此你可以使用 Wireshark 来实现此目的。

尝试将 telnet **8.8.8.8 888** 与收集的 Netmon 跟踪一起运行时，应会在以下屏幕截图中看到该跟踪：

![显示 "无法在端口888上打开与主机的连接" 错误消息的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![显示 Netmon 跟踪说明的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

前面的图像显示你无法与端口 **888** 上的 **8.8.8.8** 服务器端建立 TCP 连接，因此你可以在其中看到两个 **SynReTransmit** 附加包。 由于源 **HOST2** 无法与第一个包连接到 **8.8.8.8** ，因此它将继续尝试建立连接。

> [!TIP]
> 若要进行此连接，请尝试以下解决方案：
> 1. 选择 "**加载筛选器**  >  **标准筛选器**  >  **地址**"  >  。
> 1. 若要应用筛选器，请输入 **IPv4. Address = = 8.8.8.8**，然后选择 " **应用**"。 然后，你应该会看到从本地计算机到目标 **8.8.8.8** 的通信。

![显示筛选器地址的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![显示更多筛选器地址的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

成功的方案如以下示例所示： 

- 如果可以在没有任何问题的情况下使用 telnet **8.8.8.8 53** ，则会成功进行 tcp 3 握手，并且会话将以 TCP 4 握手完成。

    ![显示成功连接方案的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![显示成功连接方案详细信息的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 前面的 TCP 3 握手生成以下工作流：

    ![TCP 3 握手工作流的关系图。](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- 以下工作流演示了完成会话的 TCP 4 握手：

    ![TCP 4 握手详细信息的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 握手工作流的关系图。](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>有关更新网络配置的 Microsoft 电子邮件通知

你可能会收到以下电子邮件通知，建议你更新网络配置，以允许与 Azure 数据工厂的新 IP 地址进行通信，2020年11月8日：

   ![请求更新网络配置的 Microsoft 电子邮件通知屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>确定此通知是否会影响你

此通知适用于以下情况：

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>方案1：从在企业防火墙后面运行的自承载集成运行时的出站通信

如何确定是否受影响：

- 如果基于完全限定的域名定义防火墙规则 (Fqdn) 使用 [设置防火墙配置和允许 IP 地址的允许列表](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway)中所述的方法），则 *不会* 受到影响。

- 如果在企业防火墙上显式启用了出站 Ip 允许列表，则 *会* 受到影响。

   如果受到影响，请执行以下操作：2020年11月8日，通知网络基础结构团队更新网络配置以使用最新的数据工厂 IP 地址。 若要下载最新的 IP 地址，请参阅 [使用可下载的 JSON 文件发现服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>方案2：从在客户管理的 Azure 虚拟网络中的 Azure VM 上运行的自承载集成运行时的出站通信

如何确定是否受影响：

- 查看是否有任何出站网络安全组在包含自承载集成运行时的专用网络中 (NSG) 规则。 如果没有出站限制，则不会受到影响。

- 如果有出站规则限制，请检查是否正在使用服务标记。 如果使用的是服务标记，则不会受到影响。 无需更改或添加任何内容，因为新 IP 范围处于现有服务标记下。 

  ![显示 DataFactory 作为目标的目标检查的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- 如果你在 Azure 虚拟网络上的 NSG 规则设置上显式启用了出站 IP 地址的允许列表，则 *会* 受到影响。

   如果受到影响，请执行以下操作：2020年11月8日，通知网络基础结构团队更新 Azure 虚拟网络配置中的 NSG 规则，以使用最新的数据工厂 IP 地址。 若要下载最新的 IP 地址，请参阅 [使用可下载的 JSON 文件发现服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>方案3：来自客户托管的 Azure 虚拟网络中 SSIS Integration Runtime 的出站通信

如何确定是否受影响：

- 查看 SQL Server Integration Services (SSIS) Integration Runtime 中是否有任何出站 NSG 规则。 如果没有出站限制，则不会受到影响。

- 如果有出站规则限制，请检查是否正在使用服务标记。 如果使用的是服务标记，则不会受到影响。 不需要更改或添加任何内容，因为新 IP 范围处于现有服务标记下。

- 如果你在 Azure 虚拟网络上的 NSG 规则设置上显式启用了出站 IP 地址的允许列表，则 *会* 受到影响。

  如果受到影响，请执行以下操作：2020年11月8日，通知网络基础结构团队更新 Azure 虚拟网络配置中的 NSG 规则，以使用最新的数据工厂 IP 地址。 若要下载最新的 IP 地址，请参阅 [使用可下载的 JSON 文件发现服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>无法为 SSL/TLS 安全通道建立信任关系 

#### <a name="symptoms"></a>症状

自承载 IR 无法连接到 Azure 数据工厂服务。

检查自承载的 IR 事件日志或 CustomLogEvent 表中的客户端通知日志时，会看到以下错误消息：

"基础连接已关闭：无法为 SSL/TLS 安全通道建立信任关系。 根据验证过程，远程证书无效。”

若要查看数据工厂服务的服务器证书，最简单的方法是在浏览器中打开数据工厂服务 URL。 例如，在安装了自承载 IR 的计算机上打开 " [检查服务器证书" 链接](https://eu.frontend.clouddatahub.net/) ，然后查看服务器证书信息。

  ![Azure 数据工厂服务的 "检查服务器证书" 窗格的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![用于检查服务器证书路径的窗口屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>原因

此问题有两个可能的原因：

- 原因1：在安装了自承载 IR 的计算机上，数据工厂服务服务器证书的根 CA 不受信任。 
- 原因2：在你的环境中使用代理时，数据工厂服务的服务器证书将被代理替换，并且已安装自承载 IR 的计算机不信任替换的服务器证书。

#### <a name="resolution"></a>解决方法

- 原因1：请确保数据工厂服务器证书及其证书链受安装了自承载 IR 的计算机的信任。
- 原因2：在自承载 IR 计算机上信任替换的根 CA，或将代理配置为不替换数据工厂服务器证书。

有关在 Windows 上信任证书的详细信息，请参阅 [安装受信任的根证书](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate)。

#### <a name="additional-information"></a>其他信息
我们推出了一个新的 SSL 证书，该证书已从 DigiCert 进行签名。 检查以查看 DigiCert 全局根 G2 是否在受信任的根 CA 中。

  ![显示 "受信任的根证书颁发机构" 目录中的 DigiCert 全局根 G2 文件夹的屏幕截图。](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

如果此证书不在受信任的根 CA 中，请在 [此处下载](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt )。 


## <a name="self-hosted-ir-sharing"></a>自承载 IR 共享

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>不支持从其他租户共享自承载 IR 

#### <a name="symptoms"></a>症状

你可能会注意到，当你尝试从 Azure 数据工厂 UI 共享自承载 IR 时，其他租户上 (的其他数据工厂) ，但不能在不同租户上的数据工厂之间共享它。

#### <a name="cause"></a>原因

不能跨租户共享自承载 IR。

## <a name="next-steps"></a>后续步骤

有关故障排除的更多帮助，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&页面](/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [映射数据流性能指南](concepts-data-flow-performance.md)
