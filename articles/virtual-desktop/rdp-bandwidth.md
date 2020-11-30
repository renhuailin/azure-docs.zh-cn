---
title: 远程桌面协议带宽需求 Windows 虚拟桌面-Azure
titleSuffix: Azure
description: 了解 Windows 虚拟桌面的 RDP 带宽要求。
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: c9ef72241ac62efd8555de59bb52949321364035
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325173"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a> (RDP) 带宽要求远程桌面协议

远程桌面协议 (RDP) 是一种先进的技术，使用各种技术来完美地将服务器的远程图形交付到客户端设备。 RDP 会动态调整各种参数，以提供最佳用户体验，具体取决于用例、计算资源的可用性和网络带宽。

远程桌面协议多路复用多个动态虚拟通道 (DVCs) 到通过不同网络传输发送的单个数据通道。 对于远程图形、输入、设备重定向、打印等，有单独的 DVCs。 WVD 合作伙伴还可以实现使用 DVC 接口的扩展。
通过 RDP 发送的数据量取决于用户活动。 例如，用户可能会对大多数会话使用基本文本内容并使用最小带宽，但随后会将200页文档的打印输出到本地打印机。 此打印作业将使用大量的网络带宽。

使用远程会话时，网络的可用带宽会极大地影响你的体验质量。 不同的应用程序和显示分辨率需要不同的网络配置，因此，必须确保网络配置满足你的需求。

## <a name="estimating-bandwidth-utilization"></a>估计带宽利用率

RDP 对不同类型的数据使用不同的压缩算法。 下表对数据传输进行了评估：

| 数据类型 | 方向 | 如何估计 |
|---|---|---|
| 远程图形 | 会话主机到客户端 | [请参阅详细指南](#estimating-bandwidth-used-by-remote-graphics) |
|  检测信号 | 双向 | 每5秒约20个字节  |
| 输入 | 客户端到会话主机 | 数据量基于用户活动（对于大多数操作，小于100个字节）  |
| 文件传输 | 双向 | 文件传输正在使用大容量压缩。 对近似值使用 .zip 压缩 |
| 打印 | 会话主机到客户端 | 打印作业传输取决于驱动程序和使用大容量压缩，使用 .zip 压缩进行近似值 |

其他方案的带宽要求可能会根据你的使用方式而发生更改，例如：

* 语音或视频会议
* 实时通信
* 流式处理 4K 视频

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>估计远程图形使用的带宽

很难预测远程桌面的带宽使用。 用户活动会生成大多数远程桌面通信。 每个用户都是唯一的，其工作模式之间的差异可能会显著改变网络使用。

了解带宽要求的最佳方法是监视真正的用户连接。 可以通过内置的性能计数器或网络设备执行监视。

但是，在许多情况下，你可以通过了解远程桌面协议的工作方式和分析用户的工作模式来估计网络使用量。

远程协议会传递远程服务器生成的图形，以将其显示在本地监视器上。 更具体地说，它提供了在服务器上完全组合的桌面位图。
在第一种方法中发送桌面位图似乎是一种简单的任务，它需要大量的资源。 例如，以未压缩形式提供的1080p 桌面映像的大小约为 8 Mb。 在本地连接的监视器上显示此图像时，屏幕刷新率适中为 30 Hz，要求带宽约 237 MB/秒。

为了减少通过网络传输的数据量，RDP 结合使用多种方法，包括但不限于

* 帧速率优化
* 屏幕内容分类
* 内容特定的编解码器
* 渐进式图像编码
* 客户端缓存

为了更好地了解远程图形，请注意以下事项：

* 图形越丰富，其带宽就越大
  * 文本、窗口 UI 元素和纯色区域消耗的带宽低于其他任何内容。
  * 自然映像是带宽使用量最重要的参与者。 但是，客户端缓存有助于减少。
* 仅传输已更改的屏幕部分。 如果屏幕上没有可见的更新，则不会发送更新。
* 视频播放和其他高帧速率内容实质上是图像幻灯片。 RDP 会动态使用适当的视频编解码器，交付接近原始帧速率的视频编解码器。 不过，它仍是图形，但对于带宽利用率，这仍是最重要的因素。
* 远程桌面中的空闲时间表示无或最小屏幕更新;因此，在空闲时间内网络使用最小。
* 最小化远程桌面客户端窗口时，不会从会话主机发送图形更新。

请记住，施加于网络的压力取决于应用工作负荷的输出帧速率和显示分辨率。 如果帧速率或显示分辨率提高，带宽要求也会随之提高。 例如，具有高分辨率显示的一个轻型工作负荷比具有常规或低分辨率的轻型工作负荷需要更多的可用带宽。 不同的显示分辨率需要不同的可用带宽。

下表将对不同图形方案使用的数据进行估计。 这些数字适用于具有1920x1080 分辨率的单个监视器配置，并且具有默认图形模式和 444/AVC 图形模式。

| 方案 | 默认模式 | H.264/AVC 444 模式 | 缩略图 | 方案说明 |
|:---|---:|---:|---|---|
| 空闲 | 0.3 Kbps | 0.3 Kbps |:::image type="content" source="media/idle.png" alt-text="空闲连接屏幕截图":::| 用户已暂停工作，并且没有活动屏幕更新 |
| Microsoft Word | 100-150 Kbps | 200-300 Kbps |:::image type="content" source="media/word.gif" alt-text="Microsoft Word 动画":::| 用户正在积极地处理 Microsoft Word、键入、粘贴图形以及在文档之间切换 |
| Microsoft Excel | 150-200 Kbps | 400-500 Kbps |:::image type="content" source="media/excel.gif" alt-text="Microsoft Excel 动画":::| 用户正在使用 Microsoft Excel，同时更新多个包含公式和图表的单元格 |
| Microsoft PowerPoint | 4-4.5 Mbps | 1.6-1.8 Mbps |:::image type="content" source="media/powerpoint.gif" alt-text="Microsoft PowerPoint 动画":::| 用户正在积极地使用 Microsoft PowerPoint，打字，粘贴。 用户同时修改丰富的图形和使用幻灯片过渡效果 |
| Web 浏览 | 6-6.5 Mbps | 0.9-1 Mbps |:::image type="content" source="media/web.gif" alt-text="Web 浏览动画":::| 用户正在积极地处理包含多个静态和动画图像的图形丰富的网站。 用户水平和垂直滚动页面 |
| 映像库 | 3.3-3.6 Mbps | 0.7-0.8 Mbps |:::image type="content" source="media/image-gallery.gif" alt-text="图像库的动画":::| 用户正在使用映像库应用程序。 浏览、缩放、调整和旋转图像 |
| 视频播放 | 8.5-9.5 Mbps | 2.5-2.8 Mbps |:::image type="content" source="media/video.gif" alt-text="视频播放动画":::| 用户正在观看使用1/2 屏幕的 30 FPS 视频 |
| 全屏视频播放 | 7.5-8.5 Mbps | 2.5-3.1 Mbps |:::image type="content" source="media/fullscreen-video.gif" alt-text="全屏视频播放动画":::| 用户正在观看最大化为全屏的 30 FPS 视频 |

## <a name="dynamic-bandwidth-allocation"></a>动态带宽分配

远程桌面协议是一种新式协议，旨在动态地适应不断变化的网络条件。
RDP 使用持续的网络检测来主动监视可用网络带宽和数据包往返时间，而不是使用带宽利用率的硬限制。 根据结果，RDP 会动态选择图形编码选项，并为设备重定向和其他虚拟通道分配带宽。  
此技术允许 RDP 在可用时使用完整的网络管道，并且在需要网络时迅速返回。
RDP 检测并调整图像质量、帧速率或压缩算法（如果其他应用程序请求网络）。

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>使用限制速率限制网络带宽使用

在大多数情况下，无需限制带宽利用率，因为限制可能会影响用户体验。 然而，在受约束的网络中，你可能想要限制网络使用率。 另一个示例是租赁的网络，按使用的流量量收费。

在这种情况下，可以通过在 QoS 策略中指定限制速率来限制 RDP 出站网络流量。

  >[!NOTE]
  > [请确保已启用 RDP Shortpath](./shortpath.md) -对于反向连接传输，不支持中止速率限制。

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>使用组策略在会话主机上实现中止速率限制

你可以在组策略内使用基于策略的服务质量 (QoS) 来设置预定义的限制速率。

若要为已加入域的会话主机创建 QoS 策略，请首先登录到安装了组策略管理的计算机。 打开组策略管理 (选择 "开始"，指向 "管理工具"，然后选择 "组策略管理) "，然后完成以下步骤：

1. 在组策略管理 "中，找到要在其中创建新策略的容器。 例如，如果所有会话主机计算机都位于名为 " **会话主机**" 的 OU 中，则应在 "主持 Ou" 会话中创建新策略。

2. 右键单击相应的容器，然后选择 " **在这个域中创建 GPO 并在此处链接"**。

3. 在 " **新建 GPO** " 对话框中，在 " **名称** " 框中键入新组策略对象的名称，然后选择 **"确定"**。

4. 右键单击新创建的策略，然后选择 " **编辑**"。

5. 在组策略管理编辑器中，展开 " **计算机配置**"，展开 " **Windows 设置**"，右键单击 " **基于策略的 QoS**"，然后选择 " **创建新策略**"。

6. 在 " **基于策略的 QoS** " 对话框中，在 "开始" 页上的 " **名称** " 框中键入新策略的名称。 选择 " **指定出站中止速率** " 并设置所需的值，然后选择 " **下一步**"。

7. 在下一页上，选择 " **仅限具有此可执行文件名称的应用程序** " 并输入 **svchost.exe** 的名称，然后选择 " **下一步**"。 此设置指示策略仅确定来自远程桌面服务的匹配流量的优先级。

8. 在第三页上，确保选择了 " **所有源 ip 地址** 和 **目标 ip 地址** "。 选择“**下一页**”。 这两个设置可确保无论哪个计算机 (IP 地址) 发送数据包，以及哪台计算机 (IP 地址) 将接收数据包，都将对数据包进行管理。

9. 在第四页上，从 "**选择此 QoS 策略应用** 于的协议" 下拉列表中选择 " **UDP** "。

10. 在 " **指定源端口号**" 标题下，选择 " **从此源端口或范围**"。 在随附的文本框中，键入 **3390**。 选择“完成”。

在会话主机计算机上刷新组策略之前，你创建的新策略将不会生效。 尽管组策略会定期定期进行刷新，但你可以通过以下步骤强制立即刷新：

1. 在要刷新组策略的每个会话主机上，以管理员身份打开命令提示符 (" *以管理员身份运行*) "。

2. 在命令提示符下，输入

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>使用 PowerShell 在会话主机上实施中止速率限制

你可以使用以下 PowerShell cmdlet 为 RDP Shortpath 设置限制速率：

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>后续步骤

* 若要了解有关 Windows 虚拟桌面的带宽要求，请参阅 [了解远程桌面协议 (RDP) Windows 虚拟桌面的带宽要求](rdp-bandwidth.md)。
* 若要了解 Windows 虚拟桌面网络连接，请参阅 [了解 Windows 虚拟桌面网络连接](network-connectivity.md)。
* 若要开始了解 Windows 虚拟桌面的服务质量 (QoS) ，请参阅 [实现 Windows 虚拟桌面 (qos) 的服务质量](rdp-quality-of-service-qos.md)。
