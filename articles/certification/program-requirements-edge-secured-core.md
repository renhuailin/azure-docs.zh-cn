---
title: Edge Secured-Core 认证要求
description: Edge Secured-Core 认证要求
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 81e9bc60a7749cdc155fc6997c198e834476dbf0
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031924"
---
# <a name="edge-secured-core-certification-requirements-preview"></a>Edge Secured-Core 认证要求（预览版） #

本文档概述了特定于设备的功能和要求，用户需要满足这些要求才能完成认证并在 Azure IoT 设备目录中列出带有 Edge Secured-core 标签的设备。

## <a name="program-purpose"></a>计划用途 ##
Edge Secured-core 是 Azure 认证设备计划中的增量认证，面向运行完整操作系统（如 Linux 或 Windows 10 IoT）的 IoT 设备。该计划使设备合作伙伴能够通过满足一组额外的安全标准来区分其设备。 满足此标准的设备可实现以下承诺：
1. 基于硬件的设备标识 
2. 可以强制实施系统完整性 
3. 保持最新状态且可远程管理
4. 提供静态数据保护
5. 提供传输中的数据保护
6. 内置安全代理和强化
## <a name="requirements"></a>要求 ##

---
|名称|SecuredCore.Built-in.Security|
|:---|:---|
|状态|必需|
|说明|测试的目的是确保设备可以通过向 Azure Defender for IoT 发送数据来报告安全信息和事件。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证 |设备必须生成安全日志和警报。 Azure 安全中心中的设备日志和警报消息。<ol><li>从 GitHub 下载并部署安全代理</li><li>验证来自 Azure Defender for IoT 的警报消息。</li></ol>|
|资源|[Azure Defender for IoT 文档](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|名称|SecuredCore.Encryption.Storage|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证敏感数据是否可以在非易失性存储中加密。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保启用存储加密，默认算法为 XTS-AES，密钥长度为 128 位或更长。 </br></br>注意：2021 年 6 月预览版仅验证设备是否已安装 DM-Crypt 且具有一个加密分区。|
|资源||

---
|名称|SecuredCore.Hardware.SecureEnclave|
|:---|:---|
|状态|可选|
|说明|测试的目的是验证安全 enclave 是否存在，并确保可以通过安全代理来访问 enclave。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保 Azure 安全代理可以与安全 enclave 通信|
|资源|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|名称|SecuredCore.Hardware.Identity|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证设备标识是否植根于硬件。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保设备具有 TPM，并且可以使用 TPM 认可密钥通过 IoT 中心进行预配。|
|资源|[通过 DPS 设置自动预配](../iot-dps/quick-setup-auto-provision.md)|

---
|名称|SecuredCore.Update|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证设备是否可以接收和更新其固件和软件。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|合作伙伴确认他们能够通过 Microsoft 更新、[Device Update for IoT Hub (ADU)](../iot-hub-device-update/understand-device-update.md) 向设备发送更新。 对于使用 Device Update for IoT Hub 的 Linux 设备，认证需要在 Secured Core 测试过程中提供 .swu 更新文件，并为 Certification Service 提供特定于设备的信息来生成[更新清单](../iot-hub-device-update/update-manifest.md)文件。|
|资源|[IoT 中心的设备更新文档](../iot-hub-device-update/index.yml)|

---
|名称|SecuredCore.Manageability.Configuration|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证设备是否支持远程安全管理。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保设备支持远程管理，特别是安全配置。 并将状态报告回 IoT 中心/Azure Defender for IoT。|
|资源||

---
|名称|SecuredCore.Manageability.Reset|
|:---|:---|
|状态|必需|
|说明|测试的目的是针对两个用例验证设备：a) 能够执行重置（删除用户数据、删除用户配置），b) 在更新导致问题的情况下，将设备还原到已知的上一个正常点。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集和提交的文档组合验证此设备是否支持此功能。 设备制造商可以确定是否实现这些功能来支持远程重置或仅本地重置。|
|资源||

---
|名称|SecuredCore.Updates.Duration|
|:---|:---|
|状态|必需|
|说明|此策略的目的是确保设备的安全性。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动|
|验证|提交时承诺，经过认证的设备必须在提交之日起 60 个月内保持最新状态。 以某种方式提供给买方和设备本身的规范应指明其软件更新的持续时间。|
|资源||

---
|名称|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|状态|必需|
|说明|此策略的目的是确保有一种机制来收集和分发有关产品中漏洞的报告。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动|
|验证|将审查关于提交和接收认证设备的漏洞报告过程的文件。|
|资源||

---
|名称|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|状态|必需|
|说明|此策略的目的是确保在修补程序可用的 180 天内解决高/关键漏洞（使用 CVSS 3.0）。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动|
|验证|将审查关于提交和接收认证设备的漏洞报告过程的文件。|
|资源||


---
|名称|SecuredCore.Encryption.TLS|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证对所需的 TLS 版本和密码套件的支持。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
验证|通过工具集验证设备，以确保设备支持最低为 1.2 的 TLS 版本，并支持以下所需的 TLS 密码套件。<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|资源| [IoT 中心的 TLS 支持](../iot-hub/iot-hub-tls-support.md) <br /> [Windows 10 中的 TLS 密码套件](/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|名称|SecuredCore.Protection.SignedUpdates|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证必须对更新进行签名。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保除非正确签名并验证，否则不会应用对操作系统、驱动程序、应用程序软件、库、包和固件的更新。
|资源||

---
|名称|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证设备的启动完整性。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保每次设备启动时均验证固件和内核签名。 <ul><li>UEFI：已启用安全启动</li><li>Uboot：已启用验证启动</li></ul> </br> </br>注意：2021 年 6 月预览版仅验证 UEFI 是否存在。|
|资源||

---
|名称|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证此设备上的代码完整性是否可用。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保启用代码完整性。 </br> Windows：HVCI </br> Linux：dm-verity 和 IMA|
|资源||

---
|名称|SecuredCore.Protection.NetworkServices|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证接受网络输入的应用程序没有以提升的特权运行。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保接受网络连接的服务没有以系统或根权限运行。|
|资源||

---
|名称|SecuredCore.Protection.Baselines|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证系统是否符合基线安全配置。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保 Defender IOT 系统配置基准已运行。|
|资源| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|名称|SecuredCore.Firmware.Protection|
|:---|:---|
|状态|必需|
|说明|测试的目的是确保设备有足够的固件安全威胁缓解措施。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确认通过以下方法之一保护其免受固件安全威胁： <ul><li>DRTM + UEFI 管理模式缓解</li><li>DRTM + UEFI 管理模式强化</li><li>进行 SRTM + 运行时固件强化的已批准 FW</li></ul> |
|资源| https://trustedcomputinggroup.org/ |

---
|名称|SecuredCore.Firmware.Attestation|
|:---|:---|
|状态|必需|
|说明|测试的目的是确保设备能够远程向 Microsoft Azure 证明服务进行证明。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保可以收集平台启动日志和启动活动的度量数据，并远程向 Microsoft Azure 证明服务进行证明。|
|资源| [Microsoft Azure 证明](../attestation/index.yml) |

---
|名称|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证未在外部可访问端口上启用 DMA。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|如果设备上具有支持 DMA 的外部端口，则使用这些工具集验证是否已为这些端口启用并配置了 IOMMU 或 SMMU。|
|资源||

---
|名称|SecuredCore.Protection.Debug|
|:---|:---|
|状态|必需|
|说明|测试的目的是验证是否已禁用设备上的调试功能。|
|目标可用性|2021|
|适用于|任何设备|
|(OS)|不可知|
|验证类型|手动/工具|
|验证|通过工具集验证设备，以确保调试功能需要授权才能启用。|
|资源||