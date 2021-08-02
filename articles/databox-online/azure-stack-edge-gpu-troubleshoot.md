---
title: 运行诊断，收集日志以对 Azure Stack Edge 设备进行故障排除 | Microsoft Docs
description: 介绍如何运行诊断，使用日志排查 Azure Stack Edge Pro GPU 设备问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 82f8fe0574ec98c71ace2aaddda2d0bc2bc6e99f
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006340"
---
# <a name="run-diagnostics-collect-logs-to-troubleshoot-azure-stack-edge-device-issues"></a>运行诊断，收集日志以对 Azure Stack Edge 设备进行故障排除

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何运行诊断、收集日志包、高级安全日志以及查看日志，以排查 Azure Stack Edge 设备的上传和刷新问题。


## <a name="run-diagnostics"></a>运行诊断

若要诊断和排查任何设备错误，可以运行诊断测试。 在设备的本地 Web UI 中执行以下步骤，以运行诊断测试。

1. 在本地 Web UI 中，转到“故障排除”>“诊断测试”。 选择要运行的测试，然后选择“运行测试”。 此测试会诊断网络、设备、Web 代理、时间或云设置可能存在的任何问题。 你将收到设备正在运行测试的通知。

    ![选择测试 ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. 测试完成后，会显示结果。 

    ![查看测试结果](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    如果未通过某项测试，会显示建议的措施的 URL。 选择 URL 查看建议的操作。
 
    ![查看未通过测试的警告](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>收集支持包

日志包由所有可帮助 Microsoft 支持部门排查任何设备问题的相关日志组成。 可以通过本地 Web UI 生成日志包。

执行以下步骤收集支持包。 

1. 在本地 Web UI 中，转到“故障排除”>“支持”。 选择“创建支持包”。 系统随即开始收集支持包。 收集包可能需要几分钟时间。

    ![选择“添加用户”](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. 创建支持包后，选择“下载支持包”。 压缩包将下载到所选的路径。 可以解压缩该包并查看系统日志文件。

    ![选择“添加用户 2”](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>收集高级安全日志

高级安全日志可以是 Azure Stack Edge Pro 设备的软件或硬件入侵日志。

### <a name="software-intrusion-logs"></a>软件入侵日志

会针对入站和出站流量收集软件入侵或默认防火墙日志。 

- 当设备在工厂进行映像时，会启用默认防火墙日志记录。 当你通过本地 UI 或通过设备的 Windows PowerShell 界面创建支持包时，这些日志默认会捆绑在支持包中。

- 如果支持包中仅需要防火墙日志来查看设备中的任何软件 (NW) 入侵，请在创建支持包时使用 `-Include FirewallLog` 选项。 

- 如果未提供任何特定的包含选项，防火墙日志将默认会包含在支持包中。

- 在支持包中，防火墙日志是 `pfirewall.log`，位于根文件夹中。 下面是 Azure Stack Edge Pro 设备的软件入侵日志的一个示例。 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>硬件入侵日志

为了检测设备的任何硬件入侵，目前所有的机箱事件（如打开或关闭机箱）都会被记录下来。 

- 设备的系统事件日志是使用 `racadm` cmdlet 读取的。 然后，这些事件会经过筛选，将与机箱相关的事件筛选到 `HWIntrusion.txt` 文件中。

- 若要仅获取支持包中的硬件入侵日志，请在创建支持包时使用 `-Include HWSelLog` 选项。 

- 如果未提供任何特定的包含选项，硬件入侵日志将默认会包含在支持包中。

- 在支持包中，硬件入侵日志为 `HWIntrusion.txt`，位于根文件夹中。 下面是 Azure Stack Edge Pro 设备的硬件入侵日志的一个示例。 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="troubleshoot-device-upload-and-refresh-errors"></a>排查设备上传和刷新错误

相关错误文件中包含上传和刷新过程中遇到的任何错误。

1. 若要查看错误文件，请转到共享，然后选择该共享以查看内容。 


2. 选择“Microsoft Data Box Edge 文件夹”。 此文件夹包含两个子文件夹：

    - Upload 文件夹：包含上传错误的日志文件。
    - Refresh 文件夹：包含刷新期间发生的错误。

    下面是 refresh 文件夹中的日志文件示例。

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. 如果此文件中包含错误（在示例中已突出显示），请记下错误代码（在本例中为 16001）。 根据以下错误参考查找此错误代码的说明。

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>后续步骤

- [排查设备激活问题](azure-stack-edge-gpu-troubleshoot-activation.md)。
- [排查 Azure 资源管理器问题](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)。
- [排查 Blob 存储问题](azure-stack-edge-gpu-troubleshoot-blob-storage.md)。
- [排查 IoT Edge 中的计算问题](azure-stack-edge-gpu-troubleshoot-iot-edge.md)。