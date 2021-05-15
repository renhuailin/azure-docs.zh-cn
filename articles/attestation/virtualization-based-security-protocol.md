---
title: 用于 Azure 证明的基于虚拟化的安全 (VBS) 协议
description: VBS 证明协议
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507873"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>受信任的平台模块 (TPM) 和基于虚拟化的安全 (VBS) enclave 证明协议 

Microsoft Azure 证明提供强大的安全保证有赖于验证是否从信任根 (TPM) 到启动虚拟机监控程序和安全内核都需要维护信任链。 要实现此目的，Azure 证明必须先证明计算机的启动状态，然后才能在安全 enclave 中建立信任。 操作系统、虚拟机监控程序和安全内核二进制文件必须由正确的官方 Microsoft 机构签署，并以安全的方式进行配置。 一旦在受信任的平台模块 (TPM) 和虚拟机监控程序的健康状况之间建立绑定信任，我们就可以信任已测量的启动日志中提供的基于虚拟化的安全 (VBS) enclave IDK，这样我们就可以验证 enclave 和 mint 生成了密钥对，证明报告在该密钥中绑定信任，并包含安全级别和启动证明属性等其他声明。 

VBS enclave 要求使用 TPM 来提供用于验证安全基础的度量。 除协议中的请求对象外，VBS enclave 也由 TPM 终结点证明。 

## <a name="protocol-messages"></a>协议消息

### <a name="init-message"></a>初始化消息

#### <a name="direction"></a>方向

客户端 -> Azure 证明

#### <a name="payload"></a>有效负载

```
{ 
  "type": "aikcert" 
} 
```

“type”（ASCII 字符串）：表示所请求证明的类型。 目前仅支持“aikcert”。

### <a name="challenge-message"></a>质询消息

#### <a name="direction"></a>方向

Azure 证明 -> 客户端

#### <a name="payload"></a>有效负载

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**challenge** (BASE64URL(OCTETS))：服务发出的随机值。

**service_context** (BASE64URL(OCTETS))：服务创建的非跳转上下文。


### <a name="request-message"></a>请求消息

#### <a name="direction"></a>方向

客户端 -> Azure 证明 

#### <a name="payload"></a>有效负载

```
{
  "request": "<JWS>"
}
```

**request** (JWS)：请求包含一个 JSON Web 签名 (JWS) 结构。 JWS 保护的标头和 JWS 有效负载如下所示。 与任何 JWS 结构一样，最终值由以下内容组成：

BASE64URL(UTF8(JWS保护的标头)) || '.' ||

BASE64URL(JWS有效负载) || '.' ||

BASE64URL(JWS签名)

##### <a name="jws-protected-header"></a>JWS 保护的标头

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS 有效负载

JWS 有效负载可以是 basic 或 VBS 类型。 当证明证据不包含 VBS 数据时，使用 Basic。 

仅 TPM 示例： 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

TPM + VBS enclave 示例： 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (StringOrURI)：信赖方标识符。 由服务在计算计算机 ID 声明时使用

**rp_data** (BASE64URL(OCTETS))：信赖方传递的非跳转数据。 这通常由信赖方用作 nonce 来保证报表的新鲜度

**challenge** (BASE64URL(OCTETS))：服务发出的随机值。

**tpm_att_data**：与 TPM 相关的证明数据

- **srtm_boot_log (BASE64URL(OCTETS))** ：由函数 Tbsi_Get_TCG_Log_Ex 检索的 SRTM 启动日志，日志类型 = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL(OCTETS))** ：由函数 Tbsi_Get_TCG_Log_Ex 检索的 SRTM 恢复日志，日志类型 = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL(OCTETS))** ：由函数 Tbsi_Get_TCG_Log_Ex 检索的 DRTM 启动日志，日志类型 = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL(OCTETS))** ：由函数 Tbsi_Get_TCG_Log_Ex 检索的 DRTM 恢复日志，日志类型 = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL(OCTETS))** ：由函数 NCryptGetProperty 返回的 AIK 的 X.509 证书，属性 = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**：AIK 的公共部件，表示为 JSON Web 密钥 (JWK) 对象 (RFC 7517)

- **current_claim (BASE64URL(OCTETS))** ：由函数 NCryptCreateClaim 返回的当前 PCR 状态的证明声明，dwClaimType = NCRYPT_CLAIM_PLATFORM，参数 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK 设置为包含所有 PCR。 服务发送的质询还应用于计算此声明

- **boot_claim (BASE64URL(OCTETS))** ：启动时由函数 NCryptCreateClaim 返回的 PCR 状态的证明声明，dwClaimType = NCRYPT_CLAIM_PLATFORM，参数 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK 设置为包含所有 PCR

**vsm_report** (BASE64URL(OCTETS))：函数 EnclaveGetAttestationReport 返回的 VBS enclave 证明报告。 EnclaveData 参数必须是 report_signed 的值的 SHA-512 哈希（包括左大括号和右大括号）。 哈希函数输入为 UTF8(report_signed)

**attest_key**：enclave 密钥的公共部件，表示为 JSON Web 密钥 (JWK) 对象 (RFC 7517)

**custom_claims**：发送到服务的自定义 enclave 声明数组，该服务可通过策略进行评估。 声明

- **name（字符串）** ：声明的名称。 此名称将追加到由证明服务确定的 url（以避免冲突），连接的字符串成为可在策略中使用的声明的类型

- **value（字符串）** ：声明的值

- **value_type（字符串）** ：声明的值的数据类型

**service_context** (BASE64URL(OCTETS))：服务创建的非跳转上下文。

### <a name="report-message"></a>报告消息

#### <a name="direction"></a>方向

Azure 证明 -> 客户端

#### <a name="payload"></a>有效负载

```
{
  "report": "<JWT>"
}
```

**report** (JWT)：JSON Web 令牌 (JWT) 格式 (RFC 7519) 的证明报告。

## <a name="next-steps"></a>后续步骤

- [Azure 证明工作流](workflow.md)
