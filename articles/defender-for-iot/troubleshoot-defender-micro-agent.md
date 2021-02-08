---
title: 'Defender IoT 微代理 (预览版的疑难解答) '
titleSuffix: Azure Defender for IoT
description: 了解如何处理意外或不解释的错误。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 07198a5d0ef5d0a6c9eed97523c61826e451b7f5
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809699"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Defender IoT 微代理 (预览版的疑难解答) 

如果出现意外错误或未解释的错误，请使用以下故障排除方法尝试解决问题。 你还可以在需要时与 Azure Defender for IoT 产品团队联系以获取帮助。   

## <a name="service-status"></a>服务状态 

查看服务的状态： 

1. 运行以下命令

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. 确保该服务是稳定的，因为确保该服务是 `active` ，并且进程中的运行时间是合适的。

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="检查服务是否处于活动状态，确保其正常运行。":::

如果服务列为 `inactive` ，则使用以下命令启动该服务：

```azurecli
systemctl start defender-iot-micro-agent.service 
```

如果进程运行时间太短，你会知道该服务崩溃。 若要解决此问题，必须查看日志。

## <a name="review-logs"></a>查看日志 

使用以下命令验证 Defender IoT 微代理服务是否正在使用 root 权限运行。

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="验证用于 IoT 微代理服务的 Defender 是否正在以 root 权限运行。":::

若要查看日志，请使用以下命令：  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

若要重新启动该服务，请使用以下命令： 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>后续步骤

查看 [功能支持和停](edge-security-module-deprecation.md)用。
