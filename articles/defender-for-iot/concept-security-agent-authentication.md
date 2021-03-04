---
title: 安全代理身份验证
titleSuffix: Azure Defender for IoT
description: 通过两种可能的方法执行微代理身份验证。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: b0304bd191626adb71041fb0561862b988ee25cd
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124578"
---
# <a name="micro-agent-authentication-methods"></a>微代理身份验证方法 

用于 IoT 微代理的 Defender 的身份验证有两个选项： 

- 连接字符串 

- 证书 

## <a name="authentication-using-a-connection-string"></a>使用连接字符串进行身份验证 

若要使用连接字符串，你需要添加一个文件，该文件使用以 utf-8 格式编码的连接字符串（在名为的文件中的 defender 代理目录中） `connection_string.txt` 。 例如，

```azurecli
echo “<connection string>” > connection_string.txt 
```

完成此操作后，你应该使用此命令重启服务。

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>使用证书进行身份验证 


使用证书进行身份验证： 

1. 将证书的 PEM 编码的公共部分放入到名为的文件中的 defender 代理目录 `certificate_public.pem` 。
1. 在名为的文件中将 PEM 编码的私钥放入 defender 代理目录 `certificate_private.pem` 。
1. 将适当的连接字符串放在名为的文件中 `connection_string.txt` 。 例如，

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    此操作表示 defender 代理将需要提供证书才能进行身份验证。 

1. 使用以下代码重新启动服务： 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>确保微型代理正常运行 

1. 运行以下命令： 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. 确保该服务处于稳定状态，方法是确保该服务处于 **活动状态** ，并且该进程的运行时间合适： 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="确保服务处于稳定状态，以确保服务稳定。":::

## <a name="next-steps"></a>后续步骤

检查 [安全状况– ci 基准](concept-security-posture.md)。
