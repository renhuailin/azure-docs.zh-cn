---
title: 安全代理身份验证（预览）
description: 执行微代理身份验证有两种可能的方法。
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: 8dd8abaedaaababf4d84330c5bf8cb030bac55bd
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014537"
---
# <a name="micro-agent-authentication-methods-preview"></a>微代理身份验证方法（预览版）

Defender for IoT 微代理有两种身份验证选项： 

- 连接字符串 

- 证书 

## <a name="authentication-using-a-connection-string"></a>使用连接字符串进行身份验证 

若要使用连接字符串，你需要添加一个文件，使用以 utf-8 格式编码的连接字符串（在名为 `connection_string.txt` 的文件中的 defender 代理目录中）。 例如，

```azurecli
echo “<connection string>” > connection_string.txt 
```

完成此操作后，你应该使用此命令重启服务。

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>使用证书进行身份验证 


要使用证书进行身份验证： 

1. 将证书的 PEM 编码的公共部分放入名为 `certificate_public.pem` 的文件中的 defender 代理目录。
1. 将 PEM 编码的私钥放入名为 `certificate_private.pem` 的文件中的 defender 代理目录。
1. 将相应的连接字符串放入名为 `connection_string.txt` 的文件中。 例如，

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    此操作表示将需要为 defender 代理提供证书才能进行身份验证。 

1. 使用以下代码重启服务： 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>确保微代理正常运行 

1. 运行以下命令： 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. 通过确认服务状态为“活动”来确保服务稳定，并确保进程运行时间很合适： 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="确保服务处于稳定状态，以确保服务处于活动状态。":::

## <a name="next-steps"></a>后续步骤

检查[安全状况 - CIS 基准](concept-security-posture.md)。
