---
title: Azure AD 设备注册的工作流程
description: 托管域和联合域的 Azure AD 设备注册流程
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc24ecd387c011b5a8a36f380916ab815c1f0ab
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254266"
---
# <a name="how-it-works-device-registration"></a>工作流程：设备注册

设备注册是基于云的身份验证的先决条件。 通常，设备需要建立 Azure AD 或混合 Azure AD 联接才能完成设备注册。 本文详细介绍了 Azure AD 联接和混合 Azure AD 联接如何在托管和联合环境中工作。有关如何在这些设备上进行 Azure AD 身份验证的详细信息，请参阅[主刷新令牌](concept-primary-refresh-token.md#detailed-flows)一文

## <a name="azure-ad-joined-in-managed-environments"></a>托管环境中的 Azure AD 联接

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-managed.png" alt-text="托管环境中的 Azure AD 联接设备流程" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-managed.png":::

| 阶段 | 说明 |
| :----: | :----------- |
| A | Azure AD 联接设备注册的最常见方式是在开箱即用体验 (OOBE) 期间，在云体验主机 (CXH) 应用程序中加载 Azure AD 联接 Web 应用。 应用程序将 GET 请求发送到 Azure AD OpenID 配置终结点，以发现授权终结点。 Azure AD 以 JSON 文档形式向应用程序返回 OpenID 配置（其中包含授权终结点）。 |
| B | 应用程序为授权终结点生成登录请求，并收集用户凭据。 |
| C | 用户提供其用户名（采用 UPN 格式）后，应用程序会将 GET 请求发送到 Azure AD 来发现用户的相应领域信息。 此信息用于确定环境是托管环境还是联合环境。 Azure AD 返回 JSON 对象格式的信息。 应用程序确定环境是托管环境（非联合环境）。<br><br>此阶段的最后一步是让应用程序创建一个身份验证缓冲区，如果是在 OOBE 中，则暂时进行缓存，以在 OOBE 结束时自动登录。 应用程序将凭据 POST 到 Azure AD，在那里进行验证。 Azure AD 返回包含声明的 ID 令牌。 |
| D | 应用程序查找 MDM 使用条款（mdm_tou_url 声明）。 如果存在，应用程序会从声明的值中检索使用条款，向用户显示内容，并等待用户接受使用条款。 此步骤是可选的，如果声明不存在或声明值为空，则跳过此步骤。 |
| E | 应用程序将设备注册发现请求发送到 Azure 设备注册服务 (ADRS)。 Azure DRS 返回发现数据文档，这会返回特定于租户的 URI 以完成设备注册。 |
| F | 应用程序创建 TPM 绑定的（首选）RSA 2048 位密钥对，称为设备密钥 (dkpub/dkpriv)。 应用程序使用 dkpub 和公钥创建证书请求，然后使用 dkpriv 对证书请求进行签名。 接下来，应用程序从 TPM 的存储根密钥派生第二个密钥对。 此密钥是传输密钥 (tkpub/tkpriv)。 |
| G | 应用程序将设备注册请求发送到 Azure DRS，其中包括 ID 令牌、证书请求、tkpub 和证明数据。 Azure DRS 验证 ID 令牌，创建设备 ID，并基于包含的证书请求创建证书。 然后，Azure DRS 在 Azure AD 中写入设备对象，并将设备 ID 和设备证书发送到客户端。 |
| H | 从 Azure DRS 接收到设备 ID 和设备证书后，设备注册完成。 设备 ID 将保存起来以供将来参考（可从 `dsregcmd.exe /status` 查看），设备证书安装在计算机的个人存储中。 完成设备注册后，此过程将继续进行 MDM 注册。 |

## <a name="azure-ad-joined-in-federated-environments"></a>联合环境中的 Azure AD 联接

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-federated.png" alt-text="联合环境中的 Azure AD 联接设备流程" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-federated.png":::

| 阶段 | 说明 |
| :----: | :----------- |
| A | Azure AD 联接设备注册的最常见方式是在开箱即用体验 (OOBE) 期间，在云体验主机 (CXH) 应用程序中加载 Azure AD 联接 Web 应用。 应用程序将 GET 请求发送到 Azure AD OpenID 配置终结点，以发现授权终结点。 Azure AD 以 JSON 文档形式向应用程序返回 OpenID 配置（其中包含授权终结点）。 |
| B | 应用程序为授权终结点生成登录请求，并收集用户凭据。 |
| C | 用户提供其用户名（采用 UPN 格式）后，应用程序会将 GET 请求发送到 Azure AD 来发现用户的相应领域信息。 此信息用于确定环境是托管环境还是联合环境。 Azure AD 返回 JSON 对象格式的信息。 应用程序确定环境是联合环境。<br><br>应用程序重定向到返回的 JSON 领域对象中的 AuthURL 值（本地 STS 登录页）。 应用程序通过 STS 网页收集凭据。 |
| D | 应用程序将凭据 POST 到本地 STS，这可能需要再额外进行几次身份验证。 本地 STS 对用户进行身份验证并返回令牌。 应用程序将令牌 POST 到 Azure AD 进行身份验证。 Azure AD 验证令牌，并返回包含声明的 ID 令牌。 |
| E | 应用程序查找 MDM 使用条款（mdm_tou_url 声明）。 如果存在，应用程序会从声明的值中检索使用条款，向用户显示内容，并等待用户接受使用条款。 此步骤是可选的，如果声明不存在或声明值为空，则跳过此步骤。 |
| F | 应用程序将设备注册发现请求发送到 Azure 设备注册服务 (ADRS)。 Azure DRS 返回发现数据文档，这会返回特定于租户的 URI 以完成设备注册。 |
| G | 应用程序创建 TPM 绑定的（首选）RSA 2048 位密钥对，称为设备密钥 (dkpub/dkpriv)。 应用程序使用 dkpub 和公钥创建证书请求，然后使用 dkpriv 对证书请求进行签名。 接下来，应用程序从 TPM 的存储根密钥派生第二个密钥对。 此密钥是传输密钥 (tkpub/tkpriv)。 |
| H | 应用程序将设备注册请求发送到 Azure DRS，其中包括 ID 令牌、证书请求、tkpub 和证明数据。 Azure DRS 验证 ID 令牌，创建设备 ID，并基于包含的证书请求创建证书。 然后，Azure DRS 在 Azure AD 中写入设备对象，并将设备 ID 和设备证书发送到客户端。 |
| I | 从 Azure DRS 接收到设备 ID 和设备证书后，设备注册完成。 设备 ID 将保存起来以供将来参考（可从 `dsregcmd.exe /status` 查看），设备证书安装在计算机的个人存储中。 完成设备注册后，此过程将继续进行 MDM 注册。 |

## <a name="hybrid-azure-ad-joined-in-managed-environments"></a>托管环境中的混合 Azure AD 联接

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png" alt-text="托管环境中的混合 Azure AD 联接设备流程" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png":::

| 阶段 | 说明 |
| :----: | ----------- |
| A | 用户使用域凭据登录到已加入域的 Windows 10 计算机。 此凭据可以是用户名和密码或智能卡身份验证。 用户登录触发“自动设备联接”任务。 “自动设备联接”任务在加入域时被触发，并且每小时重试一次。 它并不完全取决于用户登录。 |
| B | 任务使用 LDAP 协议查询 Active Directory 中是否有存储在 Active Directory 配置分区 (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`) 中的服务连接点上的关键字属性。 关键字属性中返回的值用于确定设备注册是定向到 Azure 设备注册服务 (ADRS) 还是定向到本地托管的企业设备注册服务。 |
| C | 对于托管环境，任务以自签名证书的形式创建一个初始身份验证凭据。 任务使用 LDAP 将证书写入 Active Directory 中计算机对象上的 userCertificate 属性。 |
| D | 在 Azure AD 中创建一个表示包含 userCertificate 属性上的证书的计算机的设备对象之前，计算机无法向 Azure DRS 进行身份验证。 Azure AD Connect 检测到属性更改。 在下一个同步周期，Azure AD Connect 将 userCertificate、对象 GUID 和计算机 SID 发送到 Azure DRS。 Azure DRS 使用属性信息在 Azure AD 中创建一个设备对象。 |
| E | “自动设备联接”任务在每次用户登录时或每小时触发一次，并尝试使用 userCertificate 属性中公钥的相应私钥向 Azure AD 对计算机进行身份验证。 Azure AD 对计算机进行身份验证，并向计算机颁发 ID 令牌。 |
| F | 任务创建 TPM 绑定的（首选）RSA 2048 位密钥对，称为设备密钥 (dkpub/dkpriv)。 应用程序使用 dkpub 和公钥创建证书请求，然后使用 dkpriv 对证书请求进行签名。 接下来，应用程序从 TPM 的存储根密钥派生第二个密钥对。 此密钥是传输密钥 (tkpub/tkpriv)。 |
| G | 任务将设备注册请求发送到 Azure DRS，其中包括 ID 令牌、证书请求、tkpub 和证明数据。 Azure DRS 验证 ID 令牌，创建设备 ID，并基于包含的证书请求创建证书。 然后，Azure DRS 在 Azure AD 中更新设备对象，并将设备 ID 和设备证书发送到客户端。 |
| H | 从 Azure DRS 接收到设备 ID 和设备证书后，设备注册完成。 设备 ID 将保存起来以供将来参考（可从 `dsregcmd.exe /status` 查看），设备证书安装在计算机的个人存储中。 完成设备注册后，任务将退出。 |

## <a name="hybrid-azure-ad-joined-in-federated-environments"></a>联合环境中的混合 Azure AD 联接

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png" alt-text="托管环境中的混合 Azure AD 联接设备流程" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png":::

| 阶段 | 说明 |
| :----: | :----------- |
| A | 用户使用域凭据登录到已加入域的 Windows 10 计算机。 此凭据可以是用户名和密码或智能卡身份验证。 用户登录触发“自动设备联接”任务。 “自动设备联接”任务在加入域时被触发，并且每小时重试一次。 它并不完全取决于用户登录。 |
| B | 任务使用 LDAP 协议查询 Active Directory 中是否有存储在 Active Directory 配置分区 (`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`) 中的服务连接点上的关键字属性。 关键字属性中返回的值用于确定设备注册是定向到 Azure 设备注册服务 (ADRS) 还是定向到本地托管的企业设备注册服务。 |
| C | 对于联合环境，计算机使用 Windows 集成身份验证对企业设备注册终结点进行身份验证。 企业设备注册服务创建并返回一个令牌，其中包括对象 GUID、计算机 SID 和已加入域的状态的声明。 任务将令牌和声明提交到 Azure AD，在那里进行验证。 Azure AD 向正在运行的任务返回一个 ID 令牌。 |
| D | 应用程序创建 TPM 绑定的（首选）RSA 2048 位密钥对，称为设备密钥 (dkpub/dkpriv)。 应用程序使用 dkpub 和公钥创建证书请求，然后使用 dkpriv 对证书请求进行签名。 接下来，应用程序从 TPM 的存储根密钥派生第二个密钥对。 此密钥是传输密钥 (tkpub/tkpriv)。 |
| E | 为了为本地联合应用程序提供 SSO，任务会从本地 STS 请求企业 PRT。 运行 Active Directory 联合身份验证服务角色的 Windows Server 2016 验证请求，并将其返回给正在运行的任务。 |
| F | 任务将设备注册请求发送到 Azure DRS，其中包括 ID 令牌、证书请求、tkpub 和证明数据。 Azure DRS 验证 ID 令牌，创建设备 ID，并基于包含的证书请求创建证书。 然后，Azure DRS 在 Azure AD 中写入设备对象，并将设备 ID 和设备证书发送到客户端。 从 Azure DRS 接收到设备 ID 和设备证书后，设备注册完成。 设备 ID 将保存起来以供将来参考（可从 `dsregcmd.exe /status` 查看），设备证书安装在计算机的个人存储中。 完成设备注册后，任务将退出。 |
| G | 如果已启用 Azure AD Connect 设备写回，Azure AD Connect 会在下一个同步周期从 Azure AD 请求更新（使用证书信任的混合部署需要设备写回）。 Azure AD 将设备对象与匹配的同步计算机对象相关联。 Azure AD Connect 接收包含对象 GUID 和计算机 SID 的设备对象，并将设备对象写入 Active Directory。 |

## <a name="next-steps"></a>后续步骤

- [已加入 Azure AD 的设备](concept-azure-ad-join.md)
- [Azure AD 注册设备](concept-azure-ad-register.md)
- [混合 Azure AD 加入设备](concept-azure-ad-join-hybrid.md)
- [什么是主刷新令牌？](concept-primary-refresh-token.md)
- [Azure AD Connect：设备选项](../hybrid/how-to-connect-device-options.md)
