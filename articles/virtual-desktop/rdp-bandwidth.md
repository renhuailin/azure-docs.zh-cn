---
title: Azure 虚拟桌面的远程桌面协议带宽要求 - Azure
titleSuffix: Azure
description: 了解 Azure 虚拟桌面的 RDP 带宽要求。
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 1b43a76e417505d4894396503ca93fc87f508d4c
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753154"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>远程桌面协议(RDP) 带宽要求

远程桌面协议 (RDP) 是一种复杂的技术，它利用各种方法以求将服务器的远程图形完美传送到客户端设备。 RDP 根据用例、计算资源的可用性和网络带宽动态调整各种参数，以提供最佳用户体验。

远程桌面协议将多个动态虚拟通道 (DVC) 多路复用到通过不同网络传输发送的单个数据通道。 远程图形、输入、设备重定向、打印等具有单独的 DVC。 Azure 虚拟桌面合作伙伴还可以使用具有 DVC 接口的扩展。

通过 RDP 发送的数据量取决于用户活动。 例如，某个用户可能在大部分会话中使用基本文本内容并且只占用极少量的带宽，但后来又在本地打印机中生成了 200 页文档的打印输出。 此打印作业将使用大量的网络带宽。

使用远程 Windows 会话时，网络的可用带宽会极大地影响你的体验质量。 不同的应用程序和显示分辨率需要不同的网络配置，因此，请务必确保网络配置满足你的需求。

## <a name="estimating-bandwidth-utilization"></a>估算带宽使用量

RDP 对不同类型的数据使用不同的压缩算法。 下表可以指导你估算数据传输带宽：

| 数据类型 | 方向 | 估算方法 |
|---|---|---|
| 远程图形 | 会话主机到客户端 | [参阅详细指导](#estimating-bandwidth-used-by-remote-graphics) |
| 检测信号 | 双向 | 每 5 秒传输大约 20 字节  |
| 输入 | 客户端到会话主机 | 数据量基于用户活动，对于大多数操作小于 100 字节  |
| 文件传输 | 双向 | 文件传输使用批量压缩。 使用 .zip 压缩可得出近似值 |
| 打印 | 会话主机到客户端 | 打印作业传输取决于驱动程序和使用批量压缩，使用 .zip 压缩可得出近似值 |

其他方案的带宽要求可能会根据你的使用方式而发生更改，例如：

* 语音或视频会议
* 实时通信
* 流式处理 4K 视频

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>估算远程图形使用的带宽

很难预测远程桌面使用的带宽。 大部分远程桌面流量由用户活动生成。 每个用户都是独特的，其工作模式的差异可能会显著改变网络使用量。

了解带宽要求的最佳方法是监视实际的用户连接。 可以通过内置性能计数器或网络设备执行监视。

但是，在许多情况下，可以通过了解远程桌面协议的工作原理并分析用户的工作模式来估算网络使用量。

远程协议将传递远程服务器生成的图形，以在本地监视器上显示。 更具体地说，它提供完全在服务器上构成的桌面位图。
初看之下，发送桌面位图似乎是一个简单的任务，但它却需要大量的资源。 例如，一个 1080p 桌面图像在未压缩时的大小约为 8 Mb。 在本地连接的、屏幕刷新率为适中的 30 Hz 的监视器上显示此图像需要大约 237 MB/秒的带宽。

为了减少通过网络传输的数据量，RDP 结合使用了多种方法，包括但不限于

* 帧速率优化
* 屏幕内容分类
* 内容特定的编解码器
* 渐进式图像编码
* 客户端缓存

为了更好地了解远程图形，请注意以下几点：

* 图形内容越丰富，占用的带宽越大
  * 文本、窗口 UI 元素和纯色区域占用的带宽低于其他任何内容。
  * 自然图像在带宽使用量中占比最大。 但是，客户端缓存有助于减少它们的带宽占用量。
* 只会传输已更改的屏幕部分。 如果屏幕上没有发生可见的更新，则不会发送更新。
* 视频播放和其他高帧速率内容实质上是图像幻灯片。 RDP 将动态使用适当的视频编解码器，以接近原始帧速率的速率传递这些内容。 但是，这些内容仍旧是图形，因此在带宽使用量中仍旧占比最大。
* 远程桌面处于空闲状态意味着屏幕没有更新或者只是发生极少量的更新；因此，在空闲期网络使用量极少。
* 当远程桌面客户端窗口最小化时，不会从会话主机发送任何图形更新。

请记住，施加于网络的压力取决于应用工作负荷的输出帧速率和显示分辨率。 如果帧速率或显示分辨率提高，带宽要求也会随之提高。 例如，具有高分辨率显示的一个轻型工作负荷比具有常规或低分辨率的轻型工作负荷需要更多的可用带宽。 不同的显示分辨率需要不同的可用带宽。

下表可指导你估算不同图形场景使用的数据带宽。 这些数字适用于采用 1920x1080 分辨率并同时采用默认图形模式和 H.264/AVC 444 图形模式的单一监视器配置。

| 方案 | 默认模式 | H.264/AVC 444 模式 | 缩略图 | 场景说明 |
|:---|---:|---:|---|---|
| 闲置 | 0.3 Kbps | 0.3 Kbps |:::image type="content" source="media/idle.png" alt-text="空闲连接的屏幕截图":::| 用户已暂停工作，未发生活跃的屏幕更新 |
| Microsoft Word | 100-150 Kbps | 200-300 Kbps |:::image type="content" source="media/word.gif" alt-text="Microsoft Word 动画":::| 用户正在活跃使用 Microsoft Word、打字、粘贴图形，并在文档之间切换 |
| Microsoft Excel | 150-200 Kbps | 400-500 Kbps |:::image type="content" source="media/excel.gif" alt-text="Microsoft Excel 动画":::| 用户正在活跃使用 Microsoft Excel，并同时更新多个包含公式和图表的单元格 |
| Microsoft PowerPoint | 4-4.5 Mbps | 1.6-1.8 Mbps |:::image type="content" source="media/powerpoint.gif" alt-text="Microsoft PowerPoint 动画":::| 用户正在活跃使用 Microsoft PowerPoint、打字、粘贴。 另外，用户正在修改内容丰富的图形，并使用幻灯片过渡效果 |
| Web 浏览 | 6-6.5 Mbps | 0.9-1 Mbps |:::image type="content" source="media/web.gif" alt-text="Web 浏览动画":::| 用户正在活跃浏览一个图形内容丰富的网站，其中包含多个静态图像和动画图像。 用户横向和纵向滚动页面 |
| 图库 | 3.3-3.6 Mbps | 0.7-0.8 Mbps |:::image type="content" source="media/image-gallery.gif" alt-text="图库动画":::| 用户正在活跃使用图库应用程序。 浏览、缩放、调整大小和旋转图像 |
| 视频播放 | 8.5-9.5 Mbps | 2.5-2.8 Mbps |:::image type="content" source="media/video.gif" alt-text="视频播放动画":::| 用户正在观看一段占用了半个屏幕的 30 FPS 视频 |
| 全屏视频播放 | 7.5-8.5 Mbps | 2.5-3.1 Mbps |:::image type="content" source="media/fullscreen-video.gif" alt-text="全屏视频播放动画":::| 用户正在观看一段已最大化为全屏的 30 FPS 视频 |

## <a name="dynamic-bandwidth-allocation"></a>动态带宽分配

远程桌面协议是一种新式协议，旨在根据不断变化的网络状况动态做出调整。
RDP 不是对带宽使用量施加硬性限制，而是使用持续网络检测来主动监视可用网络带宽和数据包往返时间。 RDP 根据检测结果动态选择图形编码选项，并为设备重定向和其他虚拟通道分配带宽。  
借助这种技术，RDP 可以利用整个网络管道（如果可用），并在需要将网络用于其他目的时迅速腾出网络管道。
RDP 将检测这种需求；如果其他应用程序请求网络，RDP 将调整图像质量、帧速率或压缩算法。

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>使用限制速率来限制网络带宽使用量

大多数情况下并不需要限制带宽使用量，因为限制可能会影响用户体验。 不过，在网络受约束的场合下，你可能想要限制网络使用量。 按使用的流量计费的租用网络就是一个例子。

在这种情况下，可以通过在 QoS 策略中指定限制速率来限制 RDP 出站网络流量。

  >[!NOTE]
  > [确保已启用 RDP Shortpath](./shortpath.md) - 反向连接传输不支持限制速率限制。

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>使用组策略在会话主机上实施限制速率限制

可以在组策略中使用基于策略的服务质量 (QoS) 来设置预定义的限制速率。

若要为已加入域的会话主机创建 QoS 策略，请先登录到装有组策略管理的计算机。 打开组策略管理（选择“开始”，指向“管理工具”，并选择“组策略管理”），然后完成以下步骤：

1. 在组策略管理中，找到要在其中创建新策略的容器。 例如，如果你的所有会话主机计算机都位于名为“会话主机”的 OU 中，则应在“会话主机”OU 中创建新策略。

2. 右键单击相应的容器，然后选择“在此域中创建 GPO 并将其链接到此处”。

3. 在“新建 GPO”对话框中的“名称”框内键入新组策略对象的名称，然后选择“确定”  。

4. 右键单击新建的策略，然后选择“编辑”。

5. 在组策略管理编辑器中，依次展开“计算机配置”、“Windows 设置”，右键单击“基于策略的 QoS”，然后选择“创建新策略”   。

6. 在打开的页上，在“基于策略的 QoS”对话框中的“名称”框内键入新策略的名称 。 选择“指定出站限制速率”并设置所需值，然后选择“下一步” 。

7. 在下一页上，选择“仅限具有此可执行文件名称的应用程序”，输入名称 svchost.exe，然后选择“下一步”  。 此设置将指示策略仅优先处理来自远程桌面服务的匹配流量。

8. 在第三页上，确保已选择“任何源 IP 地址”和“任何目标 IP 地址” 。 选择“**下一页**”。 这两个设置确保无论数据包由哪个计算机（IP 地址）发送、由哪个计算机（IP 地址）接收，都会对数据包进行管理。

9. 在第四页上，从“选择要将此 QoS 策略应用到的协议”下拉列表中选择“UDP” 。

10. 在“指定源端口号”标题下，选择“从此源端口或范围” 。 在附带的文本框中，键入 3390。 选择“完成”  。

只有在会话主机计算机上刷新组策略之后，创建的新策略才会生效。 尽管组策略会定期自行刷新，但你可以通过以下步骤强制立即刷新：

1. 在要刷新组策略的每个会话主机上，以管理员身份打开命令提示符（“以管理员身份运行”）。

2. 在命令提示符下，输入：

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>使用 PowerShell 在会话主机上实施限制速率限制

可使用以下 PowerShell cmdlet 为 RDP Shortpath 设置限制速率：

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>后续步骤

* 若要了解 Azure 虚拟桌面的带宽要求，请参阅[了解 Azure 虚拟桌面的远程桌面协议 (RDP) 带宽要求](rdp-bandwidth.md)。
* 若要了解 Azure 虚拟桌面网络连接，请参阅[了解 Azure 虚拟桌面网络连接](network-connectivity.md)。
* 若要开始使用 Azure 虚拟桌面的服务质量 (QoS)，请参阅[实现 Azure 虚拟桌面的服务质量 (QoS)](rdp-quality-of-service-qos.md)。
