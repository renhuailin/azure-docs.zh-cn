---
title: Defender IoT 微代理故障排除（预览）
description: 了解如何处理意外或无法解释的错误。
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 23722195f4a7f7a19f10962ce648bc1a6e2a4012
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014667"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Defender IoT 微代理故障排除（预览）

如果发生意外错误，则可以使用这些故障排除方法来尝试解决问题。 还可以在需要时与 Azure Defender for IoT 产品团队联系以获取帮助。   

## <a name="service-status"></a>服务状态 

查看服务的状态： 

1. 运行以下命令

    ```bash
    systemctl status defender-iot-micro-agent.service 
    ```

1. 通过确认服务状态为 `active` 来检查服务是否稳定，以及进程运行时间是否正常。

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="通过检查服务是否处于活动状态以及运行时间是否正常来确保服务稳定。":::

如果服务列为 `inactive`，则请使用以下命令启动该服务：

```bash
systemctl start defender-iot-micro-agent.service 
```

如果进程运行时间不到 2 分钟，则可以知道该服务崩溃。 要解决此问题，必须[查看日志](#review-the-logs)。

## <a name="validate-micro-agent-root-privileges"></a>验证微代理根特权

使用以下命令验证 Defender IoT 微代理服务是否正在以 root 权限运行。

```bash
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="验证 Defender for IoT 微代理服务是否正在以 root 权限运行。":::
## <a name="review-the-logs"></a>查看日志 

若要查看日志，请使用以下命令：  

```bash
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>快速查看日志

如果在运行微代理时出现问题，可以在临时状态下运行微代理，这将允许使用以下命令查看日志：

```bash
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>重新启动服务。

若要重新启动服务，请使用以下命令： 

```bash
sudo systemctl restart defender-iot-micro-agent 
```

## <a name="next-steps"></a>后续步骤

查看[功能支持和停用](edge-security-module-deprecation.md)。
