---
title: Azure IoT Edge 安全管理器 - Azure IoT Edge
description: 管理 IoT Edge 设备安全性情况和安全服务的完整性。
services: iot-edge
keywords: 安全性, 安全元素, enclave, TEE, IoT Edge
author: eustacea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: a04f99151a10a2bfea7341eda47566ab54eca537
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746410"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 安全管理器

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge 安全管理器是一种界限明确的安全内核，用于通过抽象化安全硅硬件来保护 IoT Edge 设备及其所有组件。 安全管理器是增强安全性的重点，为原始设备制造商 (OEM) 提供技术集成点。

![Azure IoT Edge 安全管理器](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge 安全管理器旨在保护 IoT Edge 设备和所有固有软件操作的完整性。 安全管理器会转换来自基础硬件信任根（如果可用）的信任，以便启动 IoT Edge 运行时并监视持续进行的操作。  IoT Edge 安全管理器是与安全硅硬件（如果可用）结合使用的软件，以帮助提供最高安全保证。  

IoT Edge 安全管理器的职责包括但不限于：

* 安全、慎重地启动 Azure IoT Edge 设备。
* 预配设备标识和转换信任（如果适用）。
* 托管和保护云服务（如设备预配服务）的设备组件。
* 使用唯一标识安全预配 IoT Edge 模块。
* 通过公证服务作为设备硬件信任根的网关守卫。
* 监视运行时 IoT Edge 操作的完整性。

IoT Edge 安全管理器包含三个组件：

* IoT Edge 安全守护程序。
* 硬件安全模块平台抽象层 (HSM PAL)。
* 可选但强烈推荐的硬件硅信任根或 HSM。

## <a name="the-iot-edge-security-daemon"></a>IoT Edge 安全守护程序

IoT Edge 安全守护程序负责 IoT Edge 安全管理器的逻辑操作。 它表示 IoT Edge 设备的受信任计算基础的重要部分。

### <a name="design-principles"></a>设计原理

IoT Edge 安全守护程序遵循两个核心原则：最大化操作完整性，并最大程度地减小膨胀和改动。

#### <a name="maximize-operational-integrity"></a>最大程度地提高操作完整性

IoT Edge 安全守护程序会在任何给定硬件信任根的防御功能内尽可能以最高完整性运行。 通过适当集成，硬件信任根以静态方式度量并监视安全守护程序，并在运行时抵御篡改。

物理访问对 IoT 设备来说始终是一种威胁。 硬件信任根对于保护 IoT Edge 安全守护程序的完整性起重要作用。  硬件信任根分为两类：

* 用于保护敏感信息（如机密和加密密钥）的安全元素。
* 用于保护机密（如密钥）和敏感工作负载（如计量和计费）的安全 enclave。

存在两种类型的可以使用硬件信任根的执行环境：

* 标准或丰富执行环境 (REE)，依赖于使用安全元素来保护敏感信息。
* 受信任的执行环境 (TEE)，依赖于使用安全 enclave 技术来保护敏感信息并对软件执行提供保护。

对于使用安全 enclave 作为硬件信任根的设备，IoT Edge 安全守护程序中的敏感逻辑应该位于 enclave 内。  安全守护程序的非敏感部分可以位于 TEE 外部。  在任何情况下，原始设计制造商 (ODM) 和原始设备制造商 (OEM) 都应从其 HSM 扩展信任，以在启动和运行时度量和维护 IoT Edge 安全守护程序的完整性。

#### <a name="minimize-bloat-and-churn"></a>最大程度地减小膨胀和改动

IoT Edge 安全守护程序的另一个核心原则是最大程度地减小改动。  对于最高的信任级别，IoT Edge 安全守护程序可与设备硬件信任根紧密结合，并作为本机代码运行。  这些类型的实现通常可通过硬件信任根的安全更新路径来更新守护程序软件（与操作系统提供的更新机制完全不同），在某些情况下这可能比较困难。  虽然建议为 IoT 设备进行安全续订，但更新需求过多或更新有效负载过大会以多种方式扩展威胁面。  示例会展示如何跳过更新以最大程度提高操作可用性，或阐述硬件信任根受到过多限制而无法处理大型更新有效负载。  因此，IoT Edge 安全守护程序的设计简洁明了，保持占用情况，受信任的计算基础较小，可最大程度降低更新需求。

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge 安全守护程序的体系结构

![Azure IoT Edge 安全守护程序](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge 安全守护程序可充分利用任何可用硬件信任根技术来增强安全性。  此外，在硬件技术提供受信任的执行环境的情况下，它还允许在标准/丰富执行环境 (REE) 和受信任的执行环境 (TEE) 之间进行分字运算。 特定于角色的接口可启用 IoT Edge 的主要组件，确保 IoT Edge 设备及其操作的完整性。

#### <a name="cloud-interface"></a>云接口

云接口允许 IoT Edge 安全守护程序访问云服务，例如提高设备安全性的云功能（如安全续订）。  例如，IoT Edge 安全守护程序当前使用此接口来访问 Azure IoT 中心[设备预配服务](../iot-dps/index.yml)，以便进行设备标识生命周期管理。  

#### <a name="management-api"></a>管理 API

IoT Edge 安全守护程序提供了管理 API，在创建/启动/停止/删除 IoT Edge 模块时，IoT Edge 代理会调用该 API。 安全守护程序存储所有活动模块的“注册”。 这些注册将模块的标识映射到该模块的某些属性。 例如，这些模块属性包括容器中运行的进程的进程标识符 (pid) 和 docker 容器内容的哈希值。

工作负荷 API 使用这些属性（如下所述）来验证调用方是否有权执行某一操作。

管理 API 是一个特权 API，仅可从 IoT Edge 代理进行调用。  IoT Edge 安全守护程序启动后，它会启动 IoT Edge 代理并验证 IoT Edge 代理是否未被篡改，然后就可以为 IoT Edge 代理创建隐式注册。 工作负载 API 使用的相同证明过程也会将管理 API 的访问权限限制为仅供 IoT Edge 代理使用。

#### <a name="container-api"></a>容器 API

容器 API 会与正在使用的容器系统（例如 Moby 或 Docker）交互，以便进行模块管理。

#### <a name="workload-api"></a>工作负载 API

工作负荷 API 可供所有模块访问。 它向模块提供标识证明（以 HSM 根签名令牌或 X509 证书的形式）和相应的信任捆绑包。 信任捆绑包包含模块应信任的所有其他服务器的 CA 证书。

IoT Edge 安全守护程序使用证明过程来保护此 API。 当模块调用此 API 时，安全守护程序尝试查找标识的注册。 如果成功，则其使用注册的属性来度量该模块。 如果度量过程的结果与注册匹配，则生成新的标识证明。 相应 CA 证书（信任捆绑包）也返回到模块。  该模块使用此证书来连接到 IoT 中心、其他模块或启动服务器。 当签名令牌或证书即将到期时，模块有责任请求新的证书。

### <a name="integration-and-maintenance"></a>集成和维护

Microsoft 维护 [GitHub 上的 IoT Edge 安全守护程序](https://github.com/Azure/iotedge/tree/master/edgelet)的主要代码库。

#### <a name="installation-and-updates"></a>安装和更新

IoT Edge 安全守护程序的安装和更新通过操作系统的程序包管理系统进行管理。 具有硬件信任根的 IoT Edge 设备应通过安全启动和更新管理系统来管理守护程序的生命周期，从而进一步增强其完整性。 设备制造商应该根据各自的设备功能来探索这些方法。

#### <a name="versioning"></a>版本控制

IoT Edge 运行时可跟踪和报告 IoT Edge 安全守护程序的版本。 该版本报告为 IoT Edge 代理模块报告的属性的 runtime.platform.version 特性。

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>硬件安全模块平台抽象层 (HSM PAL)

HSM PAL 将所有硬件信任根抽象化，从而使 IoT Edge 开发者或用户从其复杂性中解脱出来。  它包括应用程序编程接口 (API) 和跨域通信过程的结合，例如标准执行环境和安全 enclave 之间的通信。  HSM PAL 的实际实现取决于使用的特定安全硬件。 它的存在允许使用几乎任何安全硅硬件。

## <a name="secure-silicon-root-of-trust-hardware"></a>安全硅硬件信任根

安全硅必须在 IoT Edge 设备硬件内定位标记信任。  安全硅种类繁多，包括受信任的平台模块 (TPM)、嵌入式安全元素 (eSE)、ARM TrustZone、Intel SGX 和自定义安全硅技术。  考虑到与 IoT 设备物理访问相关联的威胁，建议在设备中使用安全硅信任根。

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge 安全管理器集成和维护

IoT Edge 安全管理器旨在标识并隔离保护 Azure IoT Edge 平台的安全性和完整性的组件，以增强自定义。 第三方（如设备制造商）应使用其设备硬件提供可用的自定义安全功能。  

了解如何使用软件或虚拟 TPM 通过受信任的平台模块 (TPM) 强化 Azure IoT 安全管理器：  

[使用 Linux 虚拟机上的虚拟 TPM](how-to-auto-provision-simulated-device-linux.md) 创建和预配 IoT Edge 设备。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
在 Windows 上使用模拟的 TPM 创建和预配 [IoT Edge 设备](how-to-auto-provision-simulated-device-windows.md)。
:::moniker-end

## <a name="next-steps"></a>后续步骤

阅读博客 [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)（保护智能边缘）。
