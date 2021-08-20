---
title: NIST SP 800-53 R4 法规符合性详细信息
description: NIST SP 800-53 R4 法规符合性内置计划的详细信息。 每个控制措施都映射到一个或多个协助评估的 Azure Policy 定义。
ms.date: 07/16/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 7f334639a5cd1f4d6124729915cbb248e6a40e50
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114402733"
---
# <a name="details-of-the-nist-sp-800-53-r4-regulatory-compliance-built-in-initiative"></a>NIST SP 800-53 R4 法规符合性内置计划的详细信息

下文详细说明了 Azure Policy 法规符合性内置计划定义如何映射到 NIST SP 800-53 R4 的符合性域和控制措施 。
有关此符合性标准的详细信息，请参阅 [NIST SP 800-53 R4](https://nvd.nist.gov/800-53)。 如需了解所有权，请参阅 [Azure Policy 策略定义](../concepts/definition-structure.md#type)和[云中责任分担](../../../security/fundamentals/shared-responsibility.md)。

以下映射适用于 NIST SP 800-53 R4 控制措施。 使用右侧的导航栏可直接跳转到特定的符合性域。 许多控制措施都是使用 [Azure Policy](../overview.md) 计划定义实现的。 若要查看完整计划定义，请在 Azure 门户中打开“策略”，并选择“定义”页 。
然后，找到并选择 NIST SP 800-53 Rev. 4 法规符合性内置计划定义。

此内置计划部署为 [NIST SP 800-53 R4 蓝图示例](../../blueprints/samples/nist-sp-800-53-r4.md)的一部分。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../overview.md) 定义关联。
> 这些策略有助于[评估控制的合规性](../how-to/get-compliance-data.md)；但是，控制与一个或多个策略之间通常不是一对一或完全匹配。 因此，Azure Policy 中的符合性仅引用策略定义本身；这并不能确保你完全符合某个控制措施的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性标准的符合性域、控制措施和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/NIST80053_audit.json)。

## <a name="access-control"></a>访问控制

### <a name="access-control-policy-and-procedures"></a>访问控制策略和过程

**ID**：NIST SP 800-53 Rev. 4 AC-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1000 - 访问控制政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ef3cc79-733e-48ed-ab6f-7bf439e9b406) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1000.json) |
|[Microsoft 托管的控制 1001 - 访问控制政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e26f8c3-4bf3-4191-b8fc-d888805101b7) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1001.json) |

### <a name="account-management"></a>帐户管理

**ID**：NIST SP 800-53 Rev. 4 AC-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只多只为订阅指定 3 个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建议最多指定 3 个订阅所有者，以减少可能出现的已遭入侵的所有者做出的违规行为。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[应该为 SQL 服务器预配 Azure Active Directory 管理员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |审核确认已为 SQL Server 预配了 Azure Active Directory 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[审核自定义 RBAC 规则的使用情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |审核“所有者、参与者、读者”等内置角色而不是容易出错的自定义 RBAC 角色。 使用自定义角色被视为例外，需要进行严格的审查和威胁建模 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[认知服务帐户应禁用本地身份验证方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |禁用本地身份验证方法可确保认知服务帐户需要专用于身份验证的 Azure Active Directory 标识，从而提高安全性。 有关详细信息，请访问：[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[应从订阅中删除弃用的帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |应从订阅中删除弃用的帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[应从订阅中删除拥有所有者权限的已弃用帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |应从订阅中删除拥有所有者权限的已弃用帐户。  已弃用帐户是已阻止登录的帐户。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[应从订阅中删除拥有所有者权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |为了防止发生未受监视的访问，应从订阅中删除拥有所有者权限的外部帐户。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[应从订阅中删除拥有读取权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |应从订阅中删除拥有读取特权的外部帐户，以防发生未受监视的访问。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[应从订阅中删除具有写入权限的外部帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |应从订阅中删除拥有写入特权的外部帐户，以防发生未受监视的访问。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[应在 API 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[应在函数应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[应在 Web 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[Microsoft 托管的控制 1002 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F632024c2-8079-439d-a7f6-90af1d78cc65) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1002.json) |
|[Microsoft 托管的控制 1003 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b68b179-3704-4ff7-b51d-7d65374d165d) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1003.json) |
|[Microsoft 托管的控制 1004 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc17822dc-736f-4eb4-a97d-e6be662ff835) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1004.json) |
|[Microsoft 托管的控制 1005 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b626abc-26d4-4e22-9de8-3831818526b1) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1005.json) |
|[Microsoft 托管的控制 1006 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faae8d54c-4bce-4c04-b3aa-5b65b67caac8) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1006.json) |
|[Microsoft 托管的控制 1007 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17200329-bf6c-46d8-ac6d-abf4641c2add) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1007.json) |
|[Microsoft 托管的控制 1008 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8356cfc6-507a-4d20-b818-08038011cd07) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1008.json) |
|[Microsoft 托管的控制 1009 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb26f8610-e615-47c2-abd6-c00b2b0b503a) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1009.json) |
|[Microsoft 托管的控制 1010 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F784663a8-1eb0-418a-a98c-24d19bc1bb62) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1010.json) |
|[Microsoft 托管的控制 1011 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7e6a54f3-883f-43d5-87c4-172dfd64a1f5) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1011.json) |
|[Microsoft 托管的控制 1012 - 帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefd7b9ae-1db6-4eb6-b0fe-87e6565f9738) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1012.json) |
|[Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |审核 Service Fabric 中仅通过 Azure Active Directory 进行客户端身份验证 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="automated-system-account-management"></a>自动系统帐户管理

**ID**：NIST SP 800-53 Rev. 4 AC-2 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应该为 SQL 服务器预配 Azure Active Directory 管理员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |审核确认已为 SQL Server 预配了 Azure Active Directory 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[认知服务帐户应禁用本地身份验证方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |禁用本地身份验证方法可确保认知服务帐户需要专用于身份验证的 Azure Active Directory 标识，从而提高安全性。 有关详细信息，请访问：[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[Microsoft 托管的控制 1013 - 帐户管理 \| 自动系统帐户管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fd7b917-d83b-4379-af60-51e14e316c61) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1013.json) |
|[Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |审核 Service Fabric 中仅通过 Azure Active Directory 进行客户端身份验证 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="removal-of-temporary--emergency-accounts"></a>删除临时帐户/紧急帐户

**ID**：NIST SP 800-53 Rev. 4 AC-2 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1014 - 帐户管理 \| 删除临时帐户/紧急帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5dee936c-8037-4df1-ab35-6635733da48c) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1014.json) |

### <a name="disable-inactive-accounts"></a>禁用非活动帐户

**ID**：NIST SP 800-53 Rev. 4 AC-2 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1015 - 帐户管理 \| 禁用非活动帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F544a208a-9c3f-40bc-b1d1-d7e144495c14) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1015.json) |

### <a name="automated-audit-actions"></a>自动审核操作

**ID**：NIST SP 800-53 Rev. 4 AC-2 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1016 - 帐户管理 \| 自动审核操作](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8b43277-512e-40c3-ab00-14b3b6e72238) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1016.json) |

### <a name="inactivity-logout"></a>非活动状态注销

**ID**：NIST SP 800-53 Rev. 4 AC-2 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1017 - 帐户管理 \| 非活动状态注销](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fc3db37-e59a-48c1-84e9-1780cedb409e) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1017.json) |

### <a name="role-based-schemes"></a>基于角色的方案

**ID**：NIST SP 800-53 Rev. 4 AC-2 (7)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应该为 SQL 服务器预配 Azure Active Directory 管理员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |审核确认已为 SQL Server 预配了 Azure Active Directory 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[审核自定义 RBAC 规则的使用情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |审核“所有者、参与者、读者”等内置角色而不是容易出错的自定义 RBAC 角色。 使用自定义角色被视为例外，需要进行严格的审查和威胁建模 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[认知服务帐户应禁用本地身份验证方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |禁用本地身份验证方法可确保认知服务帐户需要专用于身份验证的 Azure Active Directory 标识，从而提高安全性。 有关详细信息，请访问：[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[Microsoft 托管的控制 1018 - 帐户管理 \| 基于角色的方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9121abf-e698-4ee9-b1cf-71ee528ff07f) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1018.json) |
|[Microsoft 托管的控制 1019 - 帐户管理 \| 基于角色的方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a3ee9b2-3977-459c-b8ce-2db583abd9f7) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1019.json) |
|[Microsoft 托管的控制 1020 - 帐户管理 \| 基于角色的方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b291ee8-3140-4cad-beb7-568c077c78ce) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1020.json) |
|[Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |审核 Service Fabric 中仅通过 Azure Active Directory 进行客户端身份验证 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[应使用服务主体（而不是管理证书）来保护你的订阅](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6646a0bd-e110-40ca-bb97-84fcee63c414) |通过管理证书，任何使用它们进行身份验证的人员都可管理与它们关联的订阅。 为了更安全地管理订阅，建议将服务主体和资源管理器结合使用来限制证书泄露所造成的影响。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UseServicePrincipalToProtectSubscriptions.json) |

### <a name="restrictions-on-use-of-shared--group-accounts"></a>使用共享/组帐户的限制

**ID**：NIST SP 800-53 Rev. 4 AC-2 (9)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1021 - 帐户管理 \| 使用共享/组帐户的限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a3eb0a3-428d-4669-baff-20a14eb4b551) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1021.json) |

### <a name="shared--group-account-credential-termination"></a>共享/组帐户凭据终止

**ID**：NIST SP 800-53 Rev. 4 AC-2 (10)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1022 - 帐户管理 \| 共享/组帐户凭据终止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F411f7e2d-9a0b-4627-a0b9-1700432db47d) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1022.json) |

### <a name="usage-conditions"></a>使用条件

**ID**：NIST SP 800-53 Rev. 4 AC-2 (11)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1023 - 帐户管理 \| 使用条件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe55698b6-3dea-4aa9-99b9-d8218c6ab6e5) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1023.json) |

### <a name="account-monitoring--atypical-usage"></a>帐户监视/非典型使用

**ID**：NIST SP 800-53 Rev. 4 AC-2 (12)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc 的 Azure Defender 扩展为你启用了 Arc 的 Kubernetes 群集提供威胁防护。 该扩展从群集中的节点收集数据，并将数据发送到云中的 Azure Defender for Kubernetes 后端做进一步分析。 若要了解详细信息，请访问 [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)。 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[应通过即时网络访问控制来保护虚拟机的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |建议通过 Azure 安全中心监视可能的网络适时 (JIT) 访问 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft 托管的控制 1024 - 帐户管理 \| 帐户监视/异常使用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84914fb4-12da-4c53-a341-a9fd463bed10) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1024.json) |
|[Microsoft 托管的控制 1025 - 帐户管理 \| 帐户监视/异常使用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fadfe020d-0a97-45f4-a39c-696ef99f3a95) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1025.json) |

### <a name="disable-accounts-for-high-risk-individuals"></a>禁用高风险个人的帐户

**ID**：NIST SP 800-53 Rev. 4 AC-2 (13)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1026 - 帐户管理 \| 禁用高风险个人的帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55419419-c597-4cd4-b51e-009fd2266783) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1026.json) |

### <a name="access-enforcement"></a>执法机构

**ID**：NIST SP 800-53 Rev. 4 AC-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[添加系统分配的托管标识，在没有标识的虚拟机上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持，但没有任何托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[添加系统分配的托管标识，以在具有用户分配的标识的 VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持、至少有一个用户分配的标识，但没有系统分配的托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[应该为 SQL 服务器预配 Azure Active Directory 管理员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |审核确认已为 SQL Server 预配了 Azure Active Directory 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[审核具有不使用密码的帐户的 Linux 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Linux 计算机具有不使用密码的帐户，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[对 Linux 虚拟机进行身份验证需要 SSH 密钥](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F630c64f9-8b6b-4c64-b511-6544ceff6fd6) |虽然 SSH 本身提供加密连接，但是将密码用于 SSH 仍会使 VM 易受到暴力攻击。 通过 SSH 对 Azure Linux 虚拟机进行身份验证时，最安全的方法是使用公钥-私钥对，也称为 SSH 密钥。 了解详细信息：[https://docs.microsoft.com/azure/virtual-machines/linux/create-ssh-keys-detailed](../../../virtual-machines/linux/create-ssh-keys-detailed.md)。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxNoPasswordForSSH_AINE.json) |
|[认知服务帐户应禁用本地身份验证方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |禁用本地身份验证方法可确保认知服务帐户需要专用于身份验证的 Azure Active Directory 标识，从而提高安全性。 有关详细信息，请访问：[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[部署 Linux 来宾配置扩展以在 Linux VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |此策略将 Linux 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Linux 虚拟机。 Linux 来宾配置扩展是所有 Linux 来宾配置分配的先决条件，在使用任何 Linux 来宾配置策略定义之前必须将其部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[应在 API 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[应在函数应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[应在 Web 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[应对订阅中拥有写入权限的帐户启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |为了防止帐户或资源出现违规问题，应为所有拥有写入特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[应在对订阅拥有所有者权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |为了防止帐户或资源出现违规问题，应为所有拥有所有者权限的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[应在对订阅拥有读取权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |为了防止帐户或资源出现违规问题，应为所有拥有读取特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft 托管的控制 1027 - 执法机构](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa76ca9b0-3f4a-4192-9a38-b25e4f8ae48c) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1027.json) |
|[Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |审核 Service Fabric 中仅通过 Azure Active Directory 进行客户端身份验证 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[存储帐户应迁移到新的 Azure 资源管理器资源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37e0d2fe-28a5-43d6-a273-67d37d1f5606) |使用新的 Azure 资源管理器为存储帐户提供安全增强功能，例如：更强大的访问控制 (RBAC)、更好的审核、基于 Azure 资源管理器的部署和监管、对托管标识的访问权限、访问密钥保管库以获取机密、基于 Azure AD 的身份验证以及对标记和资源组的支持，以简化安全管理 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |
|[应将虚拟机迁移到新的 Azure 资源管理器资源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |对虚拟机使用新的 Azure 资源管理器以提供安全增强功能，例如：更强的访问控制 (RBAC)、更佳审核功能、基于 Azure 资源管理器的部署和治理、对托管标识的访问、访问密钥保管库以获取机密、基于 Azure AD 的身份验证以及支持使用标记和资源组简化安全管理 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="role-based-access-control"></a>基于角色的访问控制

**ID**：NIST SP 800-53 Rev. 4 AC-3 (7)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |若要对用户可以执行的操作提供粒度筛选，请使用基于角色的访问控制 (RBAC) 来管理 Kubernetes 服务群集中的权限并配置相关授权策略。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="information-flow-enforcement"></a>信息流强制

**ID**：NIST SP 800-53 Rev. 4 AC-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在面向 Internet 的虚拟机上应用自适应网络强化建议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 安全中心会分析面向虚拟机的 Internet 的流量模式，并提供可减小潜在攻击面的网络安全组规则建议 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[所有 Internet 流量都应通过所部署的 Azure 防火墙进行路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure 安全中心已确认，你的某些子网未使用下一代防火墙进行保护。 通过使用 Azure 防火墙或受支持的下一代防火墙限制对子网的访问，保护子网免受潜在威胁的危害 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[应限制在与虚拟机关联的网络安全组上使用所有网络端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure 安全中心已识别到网络安全组的某些入站规则过于宽松。 入站规则不应允许从“任何”或“Internet”范围进行访问。 这有可能使得攻击者能够将你的资源定为攻击目标。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[API 管理服务应使用虚拟网络](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure 虚拟网络部署提供了增强的安全性和隔离，并允许你将 API 管理服务放置在不可经 Internet 路由的网络（你控制对其的访问权限）中。 然后，可以使用各种 VPN 技术将这些网络连接到本地网络，这样就能够访问网络中的和/或本地的后端服务。 可以将开发人员门户和 API 网关配置为可以从 Internet 访问或只能在虚拟网络内访问。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[应用程序配置应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到应用配置实例（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[应在 Kubernetes 服务上定义经授权的 IP 范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |通过仅向特定范围内的 IP 地址授予 API 访问权限，来限制对 Kubernetes 服务管理 API 的访问。 建议将访问权限限制给已获授权的 IP 范围，以确保只有受允许网络中的应用程序可以访问群集。 |Audit、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Azure API for FHIR 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR 应至少具有一个获得批准的专用终结点连接。 虚拟网络中的客户端可以安全地访问通过专用链接获得专用终结点连接的资源。 有关详细信息，请访问：[https://aka.ms/fhir-privatelink](../../../healthcare-apis/fhir/configure-private-link.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |通过专用终结点，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 通过将专用终结点映射到 Azure Cache for Redis 实例，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure 认知搜索服务应使用支持专用链接的 SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |使用 Azure 认知搜索的受支持的 SKU，可以通过 Azure 专用链接在源位置或目标位置没有公共 IP 地址的情况下将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到搜索服务，可以降低数据泄露风险。 有关详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure 认知搜索服务应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |禁用公用网络访问可确保 Azure 认知搜索服务不会在公共 Internet 上公开，从而提高安全性。 创建专用终结点可以限制搜索服务的公开。 有关详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure 认知搜索服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure 认知搜索可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Cosmos DB 帐户应有防火墙规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |应在 Azure Cosmos DB 帐户上定义防火墙规则，以防止来自未经授权的源的流量。 至少定义了一个 IP 规则且启用了虚拟网络筛选器的帐户才会被视为合规。 禁用公共访问的帐户也被视为合规。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure 数据工厂应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 数据工厂，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure 事件网格域应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格域（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure 事件网格主题应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格主题（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 文件同步应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |为指定的存储同步服务资源创建专用终结点，可以从组织网络的专用 IP 地址空间中寻址存储同步服务资源，而不是通过可通过 Internet 访问的公共终结点。 单独创建专用终结点并不会禁用公共终结点。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure 密钥保管库应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |禁用对密钥保管库的公用网络访问，确保无法通过公共 Internet 对其进行访问。 这样可以减少数据泄露风险。 有关详细信息，请访问：[https://aka.ms/akvprivatelink](../../../key-vault/general/private-link-service.md)。 |Audit、Deny、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|[ Azure 机器学习工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 机器学习工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到服务总线命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure SignalR 服务资源而不是整个服务，可降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Synapse 工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure Synapse 工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure Web PubSub 服务可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[认知服务帐户应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |禁用公用网络访问可确保认知服务帐户不会在公共 Internet 上公开，从而提高安全性。 创建专用终结点可以限制认知服务帐户的公开。 有关详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)。  |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[认知服务帐户应限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |应限制对认知服务帐户的网络访问。 配置网络规则，使只有来自允许的网络的应用程序才能访问认知服务帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络或到公共 Internet IP 地址范围的流量授予访问权限。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[认知服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到认知服务将减少数据泄露的可能性。 有关专用链接的详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[容器注册表不得允许无限制的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |默认情况下，Azure 容器注册表接受来自任何网络上的主机的 Internet 连接。 为了防止注册表受到潜在的威胁，只允许来自特定的公共 IP 地址或地址范围的访问。 如果注册表没有 IP/防火墙规则或配置的虚拟网络，它将出现在不正常资源中。 有关容器注册表网络规则的详细信息，请访问 [https://aka.ms/acr/portal/public-network](../../../container-registry/container-registry-access-selected-networks.md) 和 [https://aka.ms/acr/vnet](../../../container-registry/container-registry-vnet.md)。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_AuditDeny.json) |
|[容器注册表应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。通过将专用终结点映射到容器注册表，而不是整个服务，还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CORS 不应允许所有资源都能访问你的 Web 应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |跨源资源共享 (CORS) 不应允许所有域都能访问你的 Web 应用程序。 仅允许所需的域与 Web 应用交互。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[CosmosDB 帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 CosmosDB 帐户，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[磁盘访问资源应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 diskAccesses，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[事件中心命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件中心命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[面向 Internet 的虚拟机应使用网络安全组进行保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范 VM 遭受潜在威胁。 如需详细了解如何使用 NSG 控制流量，请访问 [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IoT 中心设备预配服务实例应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 IoT 中心设备预配服务可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[应禁用虚拟机上的 IP 转发](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |在虚拟机的 NIC 上启用 IP 转发可让该计算机接收发往其他目标的流量。 极少需要启用 IP 转发（例如，将 VM 用作网络虚拟设备时），因此，此策略应由网络安全团队评审。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[应通过即时网络访问控制来保护虚拟机的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |建议通过 Azure 安全中心监视可能的网络适时 (JIT) 访问 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[应关闭虚拟机上的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |打开远程管理端口会使 VM 暴露在较高级别的 Internet 攻击风险之下。 此类攻击试图暴力破解凭据，来获取对计算机的管理员访问权限。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[Microsoft 托管的控制 1028 - 信息流强制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff171df5c-921b-41e9-b12b-50801c315475) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1028.json) |
|[应使用网络安全组来保护非面向 Internet 的虚拟机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb91dfba-c30d-4263-9add-9c2384e659a6) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范非面向 Internet 的 VM 遭受潜在威胁。 如需详细了解如何使用 NSG 控制流量，请访问 [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternalVirtualMachines_Audit.json) |
|[应启用 Azure SQL 数据库上的专用终结点连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |专用终结点连接通过启用到 Azure SQL 数据库的专用连接来加强安全通信。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[应为 Key Vault 配置专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |专用链接提供了一种将 Key Vault 连接到 Azure 资源，而无需通过公共 Internet 发送流量的方法。 专用链接提供深度防御，可防范数据外泄。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[应为 MariaDB 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |专用终结点连接通过启用到 Azure Database for MariaDB 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[应为 MySQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |专用终结点连接通过启用到 Azure Database for MySQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[应为 PostgreSQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |专用终结点连接通过启用到 Azure Database for PostgreSQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[应禁用 Azure SQL 数据库上的公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b8ca024-1d5c-4dec-8995-b1a932b41780) |禁用公用网络访问属性可确保只能从专用终结点访问 Azure SQL 数据库，从而提高安全性。 此配置拒绝所有符合基于 IP 或虚拟网络的防火墙规则的登录。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PublicNetworkAccess_Audit.json) |
|[应为 MariaDB 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |禁用公用网络访问属性以提高安全性，并确保只能从专用终结点访问 Azure Database for MariaDB。 此配置严格禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
|[应为 MySQL 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |禁用公用网络访问属性以提高安全性，并确保只能从专用终结点访问 Azure Database for MySQL。 此配置严格禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
|[应为 PostgreSQL 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb52376f7-9612-48a1-81cd-1ffe4b61032c) |禁用公用网络访问属性以提高安全性，并确保只能从专用终结点访问 Azure Database for PostgreSQL。 此配置禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_DisablePublicNetworkAccess_Audit.json) |
|[应禁止存储帐户公共访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |对 Azure 存储中的容器和 blob 进行匿名公共读取访问虽然是共享数据的一种简便方法，但可能会带来安全风险。 为了防止不希望的匿名访问导致数据泄露，Microsoft 建议禁止对存储帐户的公共访问，除非你的方案需要。 |审核、拒绝、已禁用 |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[应限制对存储帐户的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |应限制对存储帐户的网络访问。 配置网络规则，以便只允许来自允许的网络的应用程序访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络的流量或公共 Internet IP 地址范围授予访问权限 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[存储帐户应使用虚拟网络规则来限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |使用虚拟网络规则作为首选方法（而不使用基于 IP 的筛选），保护存储帐户免受潜在威胁危害。 禁用基于 IP 的筛选可以阻止公共 IP 访问你的存储帐户。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |
|[存储帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到存储帐户可以降低数据泄露风险。 有关专用链接的详细信息，请访问 [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[子网应与网络安全组关联](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范子网遭受潜在威胁。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝流向子网的网络流量。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[VM 映像生成器模板应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 VM 映像生成器生成资源可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)。 |审核、已禁用、拒绝 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |

### <a name="dynamic-information-flow-control"></a>动态信息流控制

**ID**：NIST SP 800-53 Rev. 4 AC-4 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在面向 Internet 的虚拟机上应用自适应网络强化建议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 安全中心会分析面向虚拟机的 Internet 的流量模式，并提供可减小潜在攻击面的网络安全组规则建议 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[应通过即时网络访问控制来保护虚拟机的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |建议通过 Azure 安全中心监视可能的网络适时 (JIT) 访问 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="security-policy-filters"></a>安全策略筛选器

**ID**：NIST SP 800-53 Rev. 4 AC-4 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1029 - 信息流强制实施 \| 安全策略筛选器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53ac8f8e-c2b5-4d44-8a2d-058e9ced9b69) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1029.json) |

### <a name="physical--logical-separation-of-information-flows"></a>信息流的物理/逻辑分离

**ID**：NIST SP 800-53 Rev. 4 AC-4 (21)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1030 - 信息流实施 \| 信息流的物理/逻辑分离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3531453-b869-4606-9122-29c1cd6e7ed1) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1030.json) |

### <a name="separation-of-duties"></a>职责分离

**ID**：NIST SP 800-53 Rev. 4 AC-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1031 - 职责分离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b93a801-fe25-4574-a60d-cb22acffae00) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1031.json) |
|[Microsoft 托管的控制 1032 - 职责分离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5aa85661-d618-46b8-a20f-ca40a86f0751) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1032.json) |
|[Microsoft 托管的控制 1033 - 职责分离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48540f01-fc11-411a-b160-42807c68896e) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1033.json) |
|[应为订阅分配了多个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |建议指定多个订阅所有者，这样才会有管理员访问冗余。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege"></a>最小特权

**ID**：NIST SP 800-53 Rev. 4 AC-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只多只为订阅指定 3 个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建议最多指定 3 个订阅所有者，以减少可能出现的已遭入侵的所有者做出的违规行为。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[审核自定义 RBAC 规则的使用情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |审核“所有者、参与者、读者”等内置角色而不是容易出错的自定义 RBAC 角色。 使用自定义角色被视为例外，需要进行严格的审查和威胁建模 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft 托管的控制 1034 - 最小特权](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02a5ed00-6d2e-4e97-9a98-46c32c057329) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1034.json) |

### <a name="authorize-access-to-security-functions"></a>授权对安全功能的访问

**ID**：NIST SP 800-53 Rev. 4 AC-6 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1035 - 最小特权 \| 授权对安全功能的访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca94b046-45e2-444f-a862-dc8ce262a516) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1035.json) |

### <a name="non-privileged-access-for-nonsecurity-functions"></a>非安全功能的非特权访问

**ID**：NIST SP 800-53 Rev. 4 AC-6 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1036 - 最小特权 \| 非安全功能的非特权访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a16d673-8cf0-4dcf-b1d5-9b3e114fef71) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1036.json) |

### <a name="network-access-to-privileged-commands"></a>特权命令的网络访问

**ID**：NIST SP 800-53 Rev. 4 AC-6 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1037 - 最小特权 \| 特权命令的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa4c2a3d-1294-41a3-9ada-0e540471e9fb) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1037.json) |

### <a name="privileged-accounts"></a>特权帐户

**ID**：NIST SP 800-53 Rev. 4 AC-6 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1038 - 最小权限 \| 特权帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26692e88-71b7-4a5f-a8ac-9f31dd05bd8e) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1038.json) |

### <a name="review-of-user-privileges"></a>用户特权评审

**ID**：NIST SP 800-53 Rev. 4 AC-6 (7)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只多只为订阅指定 3 个所有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建议最多指定 3 个订阅所有者，以减少可能出现的已遭入侵的所有者做出的违规行为。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[审核自定义 RBAC 规则的使用情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |审核“所有者、参与者、读者”等内置角色而不是容易出错的自定义 RBAC 角色。 使用自定义角色被视为例外，需要进行严格的审查和威胁建模 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft 托管的控制 1039 - 最小特权 \| 用户特权评审](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a7b9de4-a8a2-4672-914d-c5f6752aa7f9) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1039.json) |
|[Microsoft 托管的控制 1040 - 最小权限 \| 用户特权评审](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F54205576-cec9-463f-ba44-b4b3f5d0a84c) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1040.json) |

### <a name="privilege-levels-for-code-execution"></a>代码执行的特权等级

**ID**：NIST SP 800-53 Rev. 4 AC-6 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1041 - 最小特权 \| 代码执行的特权等级](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb3d8d15b-627a-4219-8c96-4d16f788888b) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1041.json) |

### <a name="auditing-use-of-privileged-functions"></a>审核使用特权功能

**ID**：NIST SP 800-53 Rev. 4 AC-6 (9)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1042 - 最小特权 \| 审核使用特权功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F319dc4f0-0fed-4ac9-8fc3-7aeddee82c07) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1042.json) |

### <a name="prohibit-non-privileged-users-from-executing-privileged-functions"></a>禁止非特权用户执行特权功能

**ID**：NIST SP 800-53 Rev. 4 AC-6 (10)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1043 - 最小特权 \| 禁止非特权用户执行特权功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361a77f6-0f9c-4748-8eec-bc13aaaa2455) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1043.json) |

### <a name="unsuccessful-logon-attempts"></a>失败的登录尝试次数

**ID**：NIST SP 800-53 Rev. 4 AC-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1044 - 失败的登录尝试次数](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0abbac52-57cf-450d-8408-1208d0dd9e90) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1044.json) |
|[Microsoft 托管的控制 1045 - 失败的登录尝试次数](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F554d2dd6-f3a8-4ad5-b66f-5ce23bd18892) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1045.json) |

### <a name="purge--wipe-mobile-device"></a>清除/擦除移动设备

**ID**：NIST SP 800-53 Rev. 4 AC-7 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1046 - 自动帐户锁定 \| 清除/擦除移动设备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b1aa965-7502-41f9-92be-3e2fe7cc392a) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1046.json) |

### <a name="system-use-notification"></a>系统使用通知

**ID**：NIST SP 800-53 Rev. 4 AC-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1047 - 系统使用通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1ff6d62-a55c-41ab-90ba-90bb5b7b6f62) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1047.json) |
|[Microsoft 托管的控制 1048 - 系统使用通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F483e7ca9-82b3-45a2-be97-b93163a0deb7) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1048.json) |
|[Microsoft 托管的控制 1049 - 系统使用通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9adf7ba7-900a-4f35-8d57-9f34aafc405c) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1049.json) |

### <a name="concurrent-session-control"></a>并发会话控制

**ID**：NIST SP 800-53 Rev. 4 AC-10

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1050 - 并发会话控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd20184c-b4ec-4ce5-8db6-6e86352d183f) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1050.json) |

### <a name="session-lock"></a>会话锁定

**ID**：NIST SP 800-53 Rev. 4 AC-11

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1051 - 会话锁定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7cac6ee9-b58b-40c8-a5ce-f0efc3d9b339) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1051.json) |
|[Microsoft 托管的控制 1052 - 会话锁定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F027cae1c-ec3e-4492-9036-4168d540c42a) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1052.json) |

### <a name="pattern-hiding-displays"></a>模式隐藏显示

**ID**：NIST SP 800-53 Rev. 4 AC-11 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1053 - 会话锁定 \| 模式隐藏显示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7582b19c-9dba-438e-aed8-ede59ac35ba3) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1053.json) |

### <a name="session-termination"></a>会话终止

**ID**：NIST SP 800-53 Rev. 4 AC-12

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1054 - 会话终止](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5807e1b4-ba5e-4718-8689-a0ca05a191b2) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1054.json) |

### <a name="user-initiated-logouts--message-displays"></a>用户启动的注销/消息显示

**ID**：NIST SP 800-53 Rev. 4 AC-12 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1055 - 会话终止\| 用户启动的注销/消息显示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F769efd9b-3587-4e22-90ce-65ddcd5bd969) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1055.json) |
|[Microsoft 托管的控制 1056 - 会话终止 \| 用户启动的注销/消息显示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac43352f-df83-4694-8738-cfce549fd08d) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1056.json) |

### <a name="permitted-actions-without-identification-or-authentication"></a>允许的操作，无需标识或身份验证

**ID**：NIST SP 800-53 Rev. 4 AC-14

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1057 - 允许的操作，无需标识或身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78255758-6d45-4bf0-a005-7016bc03b13c) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1057.json) |
|[Microsoft 托管的控制 1058 - 允许的操作，无需标识或身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76e85d08-8fbb-4112-a1c1-93521e6a9254) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1058.json) |

### <a name="security-attributes"></a>安全属性

**ID**：NIST SP 800-53 Rev. 4 AC-16

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |

### <a name="remote-access"></a>远程访问

**ID**：NIST SP 800-53 Rev. 4 AC-17

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[添加系统分配的托管标识，在没有标识的虚拟机上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持，但没有任何托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[添加系统分配的托管标识，以在具有用户分配的标识的 VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持、至少有一个用户分配的标识，但没有系统分配的托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[应用程序配置应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到应用配置实例（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[审核允许通过没有密码的帐户进行远程连接的 Linux 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Linux 计算机允许通过没有密码的帐户进行远程连接，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Azure API for FHIR 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR 应至少具有一个获得批准的专用终结点连接。 虚拟网络中的客户端可以安全地访问通过专用链接获得专用终结点连接的资源。 有关详细信息，请访问：[https://aka.ms/fhir-privatelink](../../../healthcare-apis/fhir/configure-private-link.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis 应驻留在虚拟网络中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Azure 虚拟网络部署为 Azure Cache for Redis 提供了增强的安全性和隔离，以及子网、访问控制策略和其他功能，以进一步限制访问。配置了虚拟网络的 Azure Cache for Redis 实例是不可公开寻址的，只能从虚拟网络中的虚拟机和应用程序访问。 |Audit、Deny、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Azure Cache for Redis 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |通过专用终结点，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 通过将专用终结点映射到 Azure Cache for Redis 实例，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure 认知搜索服务应使用支持专用链接的 SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |使用 Azure 认知搜索的受支持的 SKU，可以通过 Azure 专用链接在源位置或目标位置没有公共 IP 地址的情况下将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到搜索服务，可以降低数据泄露风险。 有关详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure 认知搜索服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure 认知搜索可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure 数据工厂应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 数据工厂，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure 事件网格域应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格域（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure 事件网格主题应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格主题（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 文件同步应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |为指定的存储同步服务资源创建专用终结点，可以从组织网络的专用 IP 地址空间中寻址存储同步服务资源，而不是通过可通过 Internet 访问的公共终结点。 单独创建专用终结点并不会禁用公共终结点。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[ Azure 机器学习工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 机器学习工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到服务总线命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure SignalR 服务资源而不是整个服务，可降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Spring Cloud 应使用网络注入](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud 实例应使用虚拟网络注入实现以下目的：1. 将 Azure Spring Cloud 与 Internet 隔离。 2. 使 Azure Spring Cloud 能够与本地数据中心内的系统和/或其他虚拟网络中的 Azure 服务内的系统进行交互。 3. 授权客户控制 Azure Spring Cloud 的入站和出站网络通信。 |审核、已禁用、拒绝 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
|[Azure Synapse 工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure Synapse 工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure Web PubSub 服务可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[认知服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到认知服务将减少数据泄露的可能性。 有关专用链接的详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[容器注册表应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。通过将专用终结点映射到容器注册表，而不是整个服务，还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB 帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 CosmosDB 帐户，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[部署 Linux 来宾配置扩展以在 Linux VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |此策略将 Linux 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Linux 虚拟机。 Linux 来宾配置扩展是所有 Linux 来宾配置分配的先决条件，在使用任何 Linux 来宾配置策略定义之前必须将其部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[部署 Windows 来宾配置扩展以在 Windows VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |此策略将 Windows 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Windows 虚拟机。 Windows 来宾配置扩展是所有 Windows 来宾配置分配的先决条件，在使用任何 Windows 来宾配置策略定义之前必须将其部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[磁盘访问资源应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 diskAccesses，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[事件中心命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件中心命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[IoT 中心设备预配服务实例应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 IoT 中心设备预配服务可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[Microsoft 托管的控制 1059 - 远程访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa29b5d9f-4953-4afe-b560-203a6410b6b4) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1059.json) |
|[Microsoft 托管的控制 1060 - 远程访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a987fd-2003-45de-a120-014956581f2b) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1060.json) |
|[应启用 Azure SQL 数据库上的专用终结点连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |专用终结点连接通过启用到 Azure SQL 数据库的专用连接来加强安全通信。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[应为 Key Vault 配置专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |专用链接提供了一种将 Key Vault 连接到 Azure 资源，而无需通过公共 Internet 发送流量的方法。 专用链接提供深度防御，可防范数据外泄。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[应为 MariaDB 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |专用终结点连接通过启用到 Azure Database for MariaDB 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[应为 MySQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |专用终结点连接通过启用到 Azure Database for MySQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[应为 PostgreSQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |专用终结点连接通过启用到 Azure Database for PostgreSQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[应为 API 应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |远程调试需要在 API 应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[应对函数应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |远程调试需要在函数应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[应禁用 Web 应用程序的远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |远程调试需要在 Web 应用程序上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[应限制对存储帐户的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |应限制对存储帐户的网络访问。 配置网络规则，以便只允许来自允许的网络的应用程序访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络的流量或公共 Internet IP 地址范围授予访问权限 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[存储帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到存储帐户可以降低数据泄露风险。 有关专用链接的详细信息，请访问 [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[VM 映像生成器模板应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 VM 映像生成器生成资源可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)。 |审核、已禁用、拒绝 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |

### <a name="automated-monitoring--control"></a>自动监视/控制

**ID**：NIST SP 800-53 Rev. 4 AC-17 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[添加系统分配的托管标识，在没有标识的虚拟机上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持，但没有任何托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[添加系统分配的托管标识，以在具有用户分配的标识的 VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持、至少有一个用户分配的标识，但没有系统分配的托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[应用程序配置应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到应用配置实例（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[审核允许通过没有密码的帐户进行远程连接的 Linux 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Linux 计算机允许通过没有密码的帐户进行远程连接，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Azure API for FHIR 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR 应至少具有一个获得批准的专用终结点连接。 虚拟网络中的客户端可以安全地访问通过专用链接获得专用终结点连接的资源。 有关详细信息，请访问：[https://aka.ms/fhir-privatelink](../../../healthcare-apis/fhir/configure-private-link.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis 应驻留在虚拟网络中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Azure 虚拟网络部署为 Azure Cache for Redis 提供了增强的安全性和隔离，以及子网、访问控制策略和其他功能，以进一步限制访问。配置了虚拟网络的 Azure Cache for Redis 实例是不可公开寻址的，只能从虚拟网络中的虚拟机和应用程序访问。 |Audit、Deny、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Azure Cache for Redis 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |通过专用终结点，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 通过将专用终结点映射到 Azure Cache for Redis 实例，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure 认知搜索服务应使用支持专用链接的 SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |使用 Azure 认知搜索的受支持的 SKU，可以通过 Azure 专用链接在源位置或目标位置没有公共 IP 地址的情况下将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到搜索服务，可以降低数据泄露风险。 有关详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure 认知搜索服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure 认知搜索可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure 数据工厂应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 数据工厂，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure 事件网格域应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格域（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure 事件网格主题应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格主题（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 文件同步应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |为指定的存储同步服务资源创建专用终结点，可以从组织网络的专用 IP 地址空间中寻址存储同步服务资源，而不是通过可通过 Internet 访问的公共终结点。 单独创建专用终结点并不会禁用公共终结点。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[ Azure 机器学习工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 机器学习工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到服务总线命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure SignalR 服务资源而不是整个服务，可降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Spring Cloud 应使用网络注入](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud 实例应使用虚拟网络注入实现以下目的：1. 将 Azure Spring Cloud 与 Internet 隔离。 2. 使 Azure Spring Cloud 能够与本地数据中心内的系统和/或其他虚拟网络中的 Azure 服务内的系统进行交互。 3. 授权客户控制 Azure Spring Cloud 的入站和出站网络通信。 |审核、已禁用、拒绝 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
|[Azure Synapse 工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure Synapse 工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure Web PubSub 服务可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[认知服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到认知服务将减少数据泄露的可能性。 有关专用链接的详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[容器注册表应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。通过将专用终结点映射到容器注册表，而不是整个服务，还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB 帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 CosmosDB 帐户，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[部署 Linux 来宾配置扩展以在 Linux VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |此策略将 Linux 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Linux 虚拟机。 Linux 来宾配置扩展是所有 Linux 来宾配置分配的先决条件，在使用任何 Linux 来宾配置策略定义之前必须将其部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[部署 Windows 来宾配置扩展以在 Windows VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |此策略将 Windows 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Windows 虚拟机。 Windows 来宾配置扩展是所有 Windows 来宾配置分配的先决条件，在使用任何 Windows 来宾配置策略定义之前必须将其部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[磁盘访问资源应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 diskAccesses，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[事件中心命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件中心命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[IoT 中心设备预配服务实例应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 IoT 中心设备预配服务可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[Microsoft 托管的控制 1061 - 远程访问 \| 自动监视/控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ac22808-a2e8-41c4-9d46-429b50738914) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1061.json) |
|[应启用 Azure SQL 数据库上的专用终结点连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |专用终结点连接通过启用到 Azure SQL 数据库的专用连接来加强安全通信。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[应为 Key Vault 配置专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |专用链接提供了一种将 Key Vault 连接到 Azure 资源，而无需通过公共 Internet 发送流量的方法。 专用链接提供深度防御，可防范数据外泄。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[应为 MariaDB 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |专用终结点连接通过启用到 Azure Database for MariaDB 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[应为 MySQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |专用终结点连接通过启用到 Azure Database for MySQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[应为 PostgreSQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |专用终结点连接通过启用到 Azure Database for PostgreSQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[应为 API 应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |远程调试需要在 API 应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[应对函数应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |远程调试需要在函数应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[应禁用 Web 应用程序的远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |远程调试需要在 Web 应用程序上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[应限制对存储帐户的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |应限制对存储帐户的网络访问。 配置网络规则，以便只允许来自允许的网络的应用程序访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络的流量或公共 Internet IP 地址范围授予访问权限 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[存储帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到存储帐户可以降低数据泄露风险。 有关专用链接的详细信息，请访问 [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[VM 映像生成器模板应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 VM 映像生成器生成资源可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)。 |审核、已禁用、拒绝 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |

### <a name="protection-of-confidentiality--integrity-using-encryption"></a>使用加密保护保密性/完整性

**ID**：NIST SP 800-53 Rev. 4 AC-17 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1062 - 远程访问 \| 使用加密保护保密性/完整性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4708723f-e099-4af1-bbf9-b6df7642e444) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1062.json) |

### <a name="managed-access-control-points"></a>托管的访问控制点

**ID**：NIST SP 800-53 Rev. 4 AC-17 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1063 - 远程访问 \| 托管的访问控制点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F593ce201-54b2-4dd0-b34f-c308005d7780) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1063.json) |

### <a name="privileged-commands--access"></a>特权命令/访问

**ID**：NIST SP 800-53 Rev. 4 AC-17 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1064 - 远程访问 \| 特权命令/访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb4d9508-cbf0-4a3c-bb5c-6c95b159f3fb) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1064.json) |
|[Microsoft 托管的控制 1065 - 远程访问 \| 特权命令/访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff87b8085-dca9-4cf1-8f7b-9822b997797c) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1065.json) |

### <a name="disconnect--disable-access"></a>断开连接/禁用访问

**ID**：NIST SP 800-53 Rev. 4 AC-17 (9)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1066 - 远程访问 \| 断开连接/禁用访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4455c2e8-c65d-4acf-895e-304916f90b36) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1066.json) |

### <a name="wireless-access"></a>无线访问

**ID**：NIST SP 800-53 Rev. 4 AC-18

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1067 - 无线访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5e54f6-0127-44d0-8b61-f31dc8dd6190) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1067.json) |
|[Microsoft 托管的控制 1068 - 无线访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d045bca-a0fd-452e-9f41-4ec33769717c) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1068.json) |

### <a name="authentication-and-encryption"></a>身份验证和加密

**ID**：NIST SP 800-53 Rev. 4 AC-18 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1069 - 无线访问 \| 身份验证和加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F91c97b44-791e-46e9-bad7-ab7c4949edbb) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1069.json) |

### <a name="disable-wireless-networking"></a>禁用无线网络

**ID**：NIST SP 800-53 Rev. 4 AC-18 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1070 - 无线访问 \| 禁用无线网络](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68f837d0-8942-4b1e-9b31-be78b247bda8) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1070.json) |

### <a name="restrict-configurations-by-users"></a>限制用户配置

**ID**：NIST SP 800-53 Rev. 4 AC-18 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1071 - 无线访问 \| 限制用户配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a437f5b-9ad6-4f28-8861-de404d511ae4) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1071.json) |

### <a name="antennas--transmission-power-levels"></a>天线/传输功率电平

**ID**：NIST SP 800-53 Rev. 4 AC-18 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1072 - 无线访问 \| 天线/传输功率电平](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ca29e41-34ec-4e70-aba9-6248aca18c31) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1072.json) |

### <a name="access-control-for-mobile-devices"></a>移动设备的访问控制

**ID**：NIST SP 800-53 Rev. 4 AC-19

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1073 - 移动设备的访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab55cdb0-c7dd-4bd8-ae22-a7cea7594e9c) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1073.json) |
|[Microsoft 托管的控制 1074 - 移动设备的访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F27a69937-af92-4198-9b86-08d355c7e59a) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1074.json) |

### <a name="full-device--container-based-encryption"></a>全设备/基于容器的加密

**ID**：NIST SP 800-53 Rev. 4 AC-19 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1075 - 移动设备的访问控制 \| 全设备/基于容器的加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc933d22-04df-48ed-8f87-22a3773d4309) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1075.json) |

### <a name="use-of-external-information-systems"></a>使用外部信息系统

**ID**：NIST SP 800-53 Rev. 4 AC-20

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1076 - 使用外部信息系统](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98a4bd5f-6436-46d4-ad00-930b5b1dfed4) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1076.json) |
|[Microsoft 托管的控制 1077 - 使用外部信息系统](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2dad3668-797a-412e-a798-07d3849a7a79) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1077.json) |

### <a name="limits-on-authorized-use"></a>授权使用的限制

**ID**：NIST SP 800-53 Rev. 4 AC-20 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1078 - 使用外部信息系统 \| 授权使用的限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb25faf85-8a16-4f28-8e15-d05c0072d64d) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1078.json) |
|[Microsoft 托管的控制 1079 - 使用外部信息系统 \| 授权使用的限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85c32733-7d23-4948-88da-058e2c56b60f) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1079.json) |

### <a name="portable-storage-devices"></a>便携式存储设备

**ID**：NIST SP 800-53 Rev. 4 AC-20 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1080 - 使用外部信息系统 \| 便携式存储设备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F852981b4-a380-4704-aa1e-2e52d63445e5) |由 Microsoft 实现此访问控制控制措施 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1080.json) |

### <a name="information-sharing"></a>信息共享

**ID**：NIST SP 800-53 Rev. 4 AC-21

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1081 - 信息共享](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3867f2a9-23bb-4729-851f-c3ad98580caf) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1081.json) |
|[Microsoft 托管的控制 1082 - 信息共享](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24d480ef-11a0-4b1b-8e70-4e023bf2be23) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1082.json) |

### <a name="publicly-accessible-content"></a>可公开访问的内容

**ID**：NIST SP 800-53 Rev. 4 AC-22

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1083 - 可公开访问的内容](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e319cb6-2ca3-4a58-ad75-e67f484e50ec) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1083.json) |
|[Microsoft 托管的控制 1084 - 可公开访问的内容](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0eb15db-dd1c-4d1d-b200-b12dd6cd060c) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1084.json) |
|[Microsoft 托管的控制 1085 - 可公开访问的内容](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d117e0-38b0-4bbb-aaab-563be5dd10ba) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1085.json) |
|[Microsoft 托管的控制 1086 - 可公开访问的内容](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb321e6f-16a0-4be3-878f-500956e309c5) |由 Microsoft 实现此访问控制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1086.json) |

## <a name="awareness-and-training"></a>意识和培训

### <a name="security-awareness-and-training-policy-and-procedures"></a>安全意识与培训政策和程序

**ID**：NIST SP 800-53 Rev. 4 AT-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1087 - 安全意识与培训政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F100c82ba-42e9-4d44-a2ba-94b209248583) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1087.json) |
|[Microsoft 托管的控制 1088 - 安全意识与培训政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d50f99d-1356-49c0-934a-45f742ba7783) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1088.json) |

### <a name="security-awareness-training"></a>安全意识培训

**ID**：NIST SP 800-53 Rev. 4 AT-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1089 - 安全意识培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef080e67-0d1a-4f76-a0c5-fb9b0358485e) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1089.json) |
|[Microsoft 托管的控制 1090 - 安全意识培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fb740e5-cbc7-4d10-8686-d1bf826652b1) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1090.json) |
|[Microsoft 托管的控制 1091 - 安全意识培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb23bd715-5d1c-4e5c-9759-9cbdf79ded9d) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1091.json) |

### <a name="insider-threat"></a>内部威胁

**ID**：NIST SP 800-53 Rev. 4 AT-2 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1092 - 安全意识培训 \| 内部威胁](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8a29d47b-8604-4667-84ef-90d203fcb305) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1092.json) |

### <a name="role-based-security-training"></a>基于角色的安全培训

**ID**：NIST SP 800-53 Rev. 4 AT-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1093 - 基于角色的安全培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a0bdeeb-15f4-47e8-a1da-9f769f845fdf) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1093.json) |
|[Microsoft 托管的控制 1094 - 基于角色的安全培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b1853e0-8973-446b-b567-09d901d31a09) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1094.json) |
|[Microsoft 托管的控制 1095 - 基于角色的安全培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc3f6f7a-057b-433e-9834-e8c97b0194f6) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1095.json) |

### <a name="practical-exercises"></a>实践练习

**ID**：NIST SP 800-53 Rev. 4 AT-3 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1096 - 基于角色的安全培训 \| 实践练习](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F420c1477-aa43-49d0-bd7e-c4abdd9addff) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1096.json) |

### <a name="suspicious-communications-and-anomalous-system-behavior"></a>可疑通信和异常系统行为

**ID**：NIST SP 800-53 Rev. 4 AT-3 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1097 - 基于角色的安全培训 \| 可疑通信和异常系统行为](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3e4836-f19e-47eb-a8cd-c3ca150452c0) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1097.json) |

### <a name="security-training-records"></a>安全培训记录

**ID**：NIST SP 800-53 Rev. 4 AT-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1098 - 安全培训记录](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84363adb-dde3-411a-9fc1-36b56737f822) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1098.json) |
|[Microsoft 托管的控制 1099 - 安全培训记录](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01910bab-8639-4bd0-84ef-cc53b24d79ba) |由 Microsoft 实现此意识和培训控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1099.json) |

## <a name="audit-and-accountability"></a>审核和责任

### <a name="audit-and-accountability-policy-and-procedures"></a>审核和责任策略和过程

**ID**：NIST SP 800-53 Rev. 4 AU-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1100 - 审核和责任政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4057863c-ca7d-47eb-b1e0-503580cba8a4) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1100.json) |
|[Microsoft 托管的控制 1101 - 审核和责任政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7327b708-f0e0-457d-9d2a-527fcc9c9a65) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1101.json) |

### <a name="audit-events"></a>审核事件

**ID**：NIST SP 800-53 Rev. 4 AU-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1102 - 审核事件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9943c16a-c54c-4b4a-ad28-bfd938cdbf57) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1102.json) |
|[Microsoft 托管的控制 1103 - 审核事件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16feeb31-6377-437e-bbab-d7f73911896d) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1103.json) |
|[Microsoft 托管的控制 1104 - 审核事件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcdd8d244-18b2-4306-a1d1-df175ae0935f) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1104.json) |
|[Microsoft 托管的控制 1105 - 审核事件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b73f57b-587d-4470-a344-0b0ae805f459) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1105.json) |

### <a name="reviews-and-updates"></a>评审和更新

**ID**：NIST SP 800-53 Rev. 4 AU-2 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1106 - 审核事件 \| 评审和更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd2b4feae-61ab-423f-a4c5-0e38ac4464d8) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1106.json) |

### <a name="content-of-audit-records"></a>审核记录的内容

**ID**：NIST SP 800-53 Rev. 4 AU-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1107 - 审核记录的内容](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb29ed931-8e21-4779-8458-27916122a904) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1107.json) |

### <a name="additional-audit-information"></a>其他审核信息

**ID**：NIST SP 800-53 Rev. 4 AU-3 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1108 - 审核记录的内容 \| 其他审核信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9ad559e-c12d-415e-9a78-e50fdd7da7ba) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1108.json) |

### <a name="centralized-management-of-planned-audit-record-content"></a>集中管理计划的审核记录内容

**ID**：NIST SP 800-53 Rev. 4 AU-3 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1109 - 审核记录的内容 \| 集中管理计划的审核记录内容](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d9ffa23-ad92-4d0d-b1f4-7db274cc2aec) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1109.json) |

### <a name="audit-storage-capacity"></a>审核存储容量

**ID**：NIST SP 800-53 Rev. 4 AU-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1110 - 审核存储容量](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6182bfa7-0f2a-43f5-834a-a2ddf31c13c7) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1110.json) |

### <a name="response-to-audit-processing-failures"></a>对审核处理失败的响应

**ID**：NIST SP 800-53 Rev. 4 AU-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1111 - 对审核处理失败的响应](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21de687c-f15e-4e51-bf8d-f35c8619965b) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1111.json) |
|[Microsoft 托管的控制 1112 - 对审核处理失败的响应](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd530aad8-4ee2-45f4-b234-c061dae683c0) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1112.json) |

### <a name="audit-storage-capacity"></a>审核存储容量

**ID**：NIST SP 800-53 Rev. 4 AU-5 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1113 - 对审核处理失败的响应 \| 审核存储容量](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F562afd61-56be-4313-8fe4-b9564aa4ba7d) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1113.json) |

### <a name="real-time-alerts"></a>实时警报

**ID**：NIST SP 800-53 Rev. 4 AU-5 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1114 - 对审核处理失败的响应 \| 实时警报](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c090801-59bc-4454-bb33-e0455133486a) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1114.json) |

### <a name="audit-review-analysis-and-reporting"></a>审核评审、分析和报告

**ID**：NIST SP 800-53 Rev. 4 AU-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc 的 Azure Defender 扩展为你启用了 Arc 的 Kubernetes 群集提供威胁防护。 该扩展从群集中的节点收集数据，并将数据发送到云中的 Azure Defender for Kubernetes 后端做进一步分析。 若要了解详细信息，请访问 [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)。 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[Microsoft 托管的控制 1115 - 审核评审、分析和报告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b653845-2ad9-4e09-a4f3-5a7c1d78353d) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1115.json) |
|[Microsoft 托管的控制 1116 - 审核评审、分析和报告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e47bc51-35d1-44b8-92af-e2f2d8b67635) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1116.json) |
|[应在 Linux 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[应在 Windows 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[应启用网络观察程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 需要在存在虚拟网络的每个区域中创建一个网络观察程序资源组。 如果网络观察程序资源组在特定区域中不可用，则会启用警报。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="process-integration"></a>过程集成

**ID**：NIST SP 800-53 Rev. 4 AU-6 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1117 - 审核评审、分析和报告 \| 过程集成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fbfe680-6dbb-4037-963c-a621c5635902) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1117.json) |

### <a name="correlate-audit-repositories"></a>关联审核存储库

**ID**：NIST SP 800-53 Rev. 4 AU-6 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1118 - 审核评审、分析和报告 \| 关联审核存储库](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96f743d-a195-420d-983a-08aa06bc441e) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1118.json) |

### <a name="central-review-and-analysis"></a>集中评审和分析

**ID**：NIST SP 800-53 Rev. 4 AU-6 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用 SQL 服务器上的审核](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |应在 SQL 服务器上启用审核以跟踪服务器上所有数据库的数据库活动，并将其保存在审核日志中。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[你的订阅应启用 Log Analytics 代理自动预配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |为了监视安全漏洞和威胁，Azure 安全中心会从 Azure 虚拟机收集数据。 数据是使用 Log Analytics 代理收集的，该代理以前称为 Microsoft Monitoring Agent (MMA)，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到 Log Analytics 工作区以用于分析。 建议启用自动预配，将代理自动部署到所有受支持的 Azure VM 和任何新创建的 VM。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc 的 Azure Defender 扩展为你启用了 Arc 的 Kubernetes 群集提供威胁防护。 该扩展从群集中的节点收集数据，并将数据发送到云中的 Azure Defender for Kubernetes 后端做进一步分析。 若要了解详细信息，请访问 [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)。 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[应启用应用程序服务中的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |审核确认已在应用上启用诊断日志。 如果发生安全事件或网络遭泄露，这样便可以重新创建活动线索用于调查目的。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[应在计算机上安装来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |若要确保安全配置计算机的来宾内设置，请安装来宾配置扩展。 该扩展监视的来宾内设置包括操作系统的配置、应用程序配置或状态以及环境设置。 安装后，来宾内策略将可用，如“应启用 Windows 攻击防护”。 更多信息请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[应在计算机上解决 Log Analytics 代理运行状况问题](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |安全中心使用 Log Analytics 代理，它之前被称为 Microsoft Monitoring Agent (MMA)。 为了确保成功监视虚拟机，需要确保此代理安装在虚拟机上，并能正确地将安全事件收集到配置的工作区中。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics 代理应安装在 Linux Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |如果 Log Analytics 代理未安装，此策略审核 Linux Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Log Analytics 代理应安装在虚拟机上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |此策略审核是否有任何 Windows/Linux 虚拟机 (VM) 没有安装安全中心用于监视安全漏洞和威胁的 Log Analytics 代理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Log Analytics 代理应安装在虚拟机规模集上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |安全中心从 Azure 虚拟机 (VM) 收集数据，以监视安全漏洞和威胁。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics 代理应安装在 Windows Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |如果 Log Analytics 代理未安装，此策略审核 Windows Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 托管的控制 1119 - 审核评审、分析和报告 \| 中心评审和分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F845f6359-b764-4b40-b579-657aefe23c44) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1119.json) |
|[应在 Linux 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[应在 Windows 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[应启用网络观察程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 需要在存在虚拟网络的每个区域中创建一个网络观察程序资源组。 如果网络观察程序资源组在特定区域中不可用，则会启用警报。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[应启用 Azure Data Lake Store 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[应启用 Azure 流分析中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用 Batch 帐户中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[应启用 Data Lake Analytics 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用事件中心内的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[应启用 IoT 中心内的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[应启用 Key Vault 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |对启用资源日志进行审核。 使用此策略可在发生安全事件或网络受到安全威胁时重新创建用于调查的活动线索 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[应启用逻辑应用中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[应启用搜索服务中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[应启用虚拟机规模集中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |建议启用日志，以便在出现某个事件或遭到入侵后需要进行调查时可以重新创建活动线索。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[应使用系统分配的托管标识来部署虚拟机的来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |来宾配置扩展需要系统分配的托管标识。 如果安装了来宾配置扩展，但没有系统分配的托管标识，则此策略作用域内的 Azure 虚拟机是不合规的。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md) |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="integration--scanning-and-monitoring-capabilities"></a>集成/扫描和监视功能

**ID**：NIST SP 800-53 Rev. 4 AU-6 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用 SQL 服务器上的审核](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |应在 SQL 服务器上启用审核以跟踪服务器上所有数据库的数据库活动，并将其保存在审核日志中。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[你的订阅应启用 Log Analytics 代理自动预配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |为了监视安全漏洞和威胁，Azure 安全中心会从 Azure 虚拟机收集数据。 数据是使用 Log Analytics 代理收集的，该代理以前称为 Microsoft Monitoring Agent (MMA)，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到 Log Analytics 工作区以用于分析。 建议启用自动预配，将代理自动部署到所有受支持的 Azure VM 和任何新创建的 VM。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc 的 Azure Defender 扩展为你启用了 Arc 的 Kubernetes 群集提供威胁防护。 该扩展从群集中的节点收集数据，并将数据发送到云中的 Azure Defender for Kubernetes 后端做进一步分析。 若要了解详细信息，请访问 [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)。 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[应启用应用程序服务中的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |审核确认已在应用上启用诊断日志。 如果发生安全事件或网络遭泄露，这样便可以重新创建活动线索用于调查目的。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[应在计算机上安装来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |若要确保安全配置计算机的来宾内设置，请安装来宾配置扩展。 该扩展监视的来宾内设置包括操作系统的配置、应用程序配置或状态以及环境设置。 安装后，来宾内策略将可用，如“应启用 Windows 攻击防护”。 更多信息请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[应在计算机上解决 Log Analytics 代理运行状况问题](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |安全中心使用 Log Analytics 代理，它之前被称为 Microsoft Monitoring Agent (MMA)。 为了确保成功监视虚拟机，需要确保此代理安装在虚拟机上，并能正确地将安全事件收集到配置的工作区中。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics 代理应安装在 Linux Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |如果 Log Analytics 代理未安装，此策略审核 Linux Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Log Analytics 代理应安装在虚拟机上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |此策略审核是否有任何 Windows/Linux 虚拟机 (VM) 没有安装安全中心用于监视安全漏洞和威胁的 Log Analytics 代理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Log Analytics 代理应安装在虚拟机规模集上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |安全中心从 Azure 虚拟机 (VM) 收集数据，以监视安全漏洞和威胁。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics 代理应安装在 Windows Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |如果 Log Analytics 代理未安装，此策略审核 Windows Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 托管的控制 1120 - 审核评审、分析和报告 \| 集成/扫描和监视功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc69b870e-857b-458b-af02-bb234f7a00d3) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1120.json) |
|[应在 Linux 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[应在 Windows 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[应启用网络观察程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 需要在存在虚拟网络的每个区域中创建一个网络观察程序资源组。 如果网络观察程序资源组在特定区域中不可用，则会启用警报。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[应启用 Azure Data Lake Store 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[应启用 Azure 流分析中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用 Batch 帐户中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[应启用 Data Lake Analytics 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用事件中心内的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[应启用 IoT 中心内的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[应启用 Key Vault 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |对启用资源日志进行审核。 使用此策略可在发生安全事件或网络受到安全威胁时重新创建用于调查的活动线索 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[应启用逻辑应用中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[应启用搜索服务中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[应启用虚拟机规模集中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |建议启用日志，以便在出现某个事件或遭到入侵后需要进行调查时可以重新创建活动线索。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[应使用系统分配的托管标识来部署虚拟机的来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |来宾配置扩展需要系统分配的托管标识。 如果安装了来宾配置扩展，但没有系统分配的托管标识，则此策略作用域内的 Azure 虚拟机是不合规的。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md) |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="correlation-with-physical-monitoring"></a>与物理监视关联

**ID**：NIST SP 800-53 Rev. 4 AU-6 (6)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1121 - 审核评审、分析和报告 \| 与物理监视关联](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc72b0eb9-1fc2-44e5-a866-e7cb0532f7c1) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1121.json) |

### <a name="permitted-actions"></a>允许的操作

**ID**：NIST SP 800-53 Rev. 4 AU-6 (7)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1122 - 审核评审、分析和报告 \| 允许的操作](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F243ec95e-800c-49d4-ba52-1fdd9f6b8b57) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1122.json) |

### <a name="audit-level-adjustment"></a>审核等级调整

**ID**：NIST SP 800-53 Rev. 4 AU-6 (10)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1123 - 审核评审、分析和报告 \| 审核等级调整](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03996055-37a4-45a5-8b70-3f1caa45f87d) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1123.json) |

### <a name="audit-reduction-and-report-generation"></a>审核缩减和报表生成

**ID**：NIST SP 800-53 Rev. 4 AU-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1124 - 审核缩减和报表生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10152dd-78f8-4335-ae2d-ad92cc028da4) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1124.json) |
|[Microsoft 托管的控制 1125 - 审核缩减和报表生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6ce745a-670e-47d3-a6c4-3cfe5ef00c10) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1125.json) |

### <a name="automatic-processing"></a>自动处理

**ID**：NIST SP 800-53 Rev. 4 AU-7 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1126 - 审核缩减和报表生成 \| 自动处理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f37f71b-420f-49bf-9477-9c0196974ecf) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1126.json) |

### <a name="time-stamps"></a>时间戳

**ID**：NIST SP 800-53 Rev. 4 AU-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1127 - 时间戳](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ce328db-aef3-48ed-9f81-2ab7cf839c66) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1127.json) |
|[Microsoft 托管的控制 1128 - 时间戳](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef212163-3bc4-4e86-bcf8-705127086393) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1128.json) |

### <a name="synchronization-with-authoritative-time-source"></a>与权威时间源同步

**ID**：NIST SP 800-53 Rev. 4 AU-8 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1129 - 时间戳 \| 与权威时间源同步](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71bb965d-4047-4623-afd4-b8189a58df5d) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1129.json) |
|[Microsoft 托管的控制 1130 - 时间戳 \| 与权威时间源同步](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd7c4c1d-51ee-4349-9dab-89a7f8c8d102) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1130.json) |

### <a name="protection-of-audit-information"></a>审核信息保护

**ID**：NIST SP 800-53 Rev. 4 AU-9

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1131 - 审核信息保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb472a17e-c2bc-493f-b50b-42d55a346962) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1131.json) |

### <a name="audit-backup-on-separate-physical-systems--components"></a>对单独物理系统/组件的审核备份

**ID**：NIST SP 800-53 Rev. 4 AU-9 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1132 - 审核信息保护 \| 对独立物理系统/组件的审核备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05938e10-cdbd-4a54-9b2b-1cbcfc141ad0) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1132.json) |

### <a name="cryptographic-protection"></a>加密保护

**ID**：NIST SP 800-53 Rev. 4 AU-9 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1133 - 审核信息保护 \| 加密保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90b60a09-133d-45bc-86ef-b206a6134bbe) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1133.json) |

### <a name="access-by-subset-of-privileged-users"></a>特权用户子集的访问权限

**ID**：NIST SP 800-53 Rev. 4 AU-9 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1134 - 审核信息保护 \| 特权用户子集的访问权限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e95f70e-181c-4422-9da2-43079710c789) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1134.json) |

### <a name="non-repudiation"></a>不可否认性

**ID**：NIST SP 800-53 Rev. 4 AU-10

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1135 - 不可否认性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c308b6b-2429-4b97-86cf-081b8e737b04) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1135.json) |

### <a name="audit-record-retention"></a>审核记录保留期

**ID**：NIST SP 800-53 Rev. 4 AU-11

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1136 - 审核记录保留期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97ed5bac-a92f-4f6d-a8ed-dc094723597c) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1136.json) |
|[对存储帐户目标进行审核的 SQL Server 应配置至少 90 天的保留期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |为便于调查事件，建议将 SQL Server 审核数据在存储帐户目标中的数据保留期设置为至少 90 天。 确认你遵守所运营区域的必要保留规则。 为了符合监管标准，有时需要这样做。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="audit-generation"></a>审核生成

**ID**：NIST SP 800-53 Rev. 4 AU-12

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用 SQL 服务器上的审核](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |应在 SQL 服务器上启用审核以跟踪服务器上所有数据库的数据库活动，并将其保存在审核日志中。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[你的订阅应启用 Log Analytics 代理自动预配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |为了监视安全漏洞和威胁，Azure 安全中心会从 Azure 虚拟机收集数据。 数据是使用 Log Analytics 代理收集的，该代理以前称为 Microsoft Monitoring Agent (MMA)，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到 Log Analytics 工作区以用于分析。 建议启用自动预配，将代理自动部署到所有受支持的 Azure VM 和任何新创建的 VM。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc 的 Azure Defender 扩展为你启用了 Arc 的 Kubernetes 群集提供威胁防护。 该扩展从群集中的节点收集数据，并将数据发送到云中的 Azure Defender for Kubernetes 后端做进一步分析。 若要了解详细信息，请访问 [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)。 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[应启用应用程序服务中的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |审核确认已在应用上启用诊断日志。 如果发生安全事件或网络遭泄露，这样便可以重新创建活动线索用于调查目的。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[应在计算机上安装来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |若要确保安全配置计算机的来宾内设置，请安装来宾配置扩展。 该扩展监视的来宾内设置包括操作系统的配置、应用程序配置或状态以及环境设置。 安装后，来宾内策略将可用，如“应启用 Windows 攻击防护”。 更多信息请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[应在计算机上解决 Log Analytics 代理运行状况问题](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |安全中心使用 Log Analytics 代理，它之前被称为 Microsoft Monitoring Agent (MMA)。 为了确保成功监视虚拟机，需要确保此代理安装在虚拟机上，并能正确地将安全事件收集到配置的工作区中。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics 代理应安装在 Linux Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |如果 Log Analytics 代理未安装，此策略审核 Linux Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Log Analytics 代理应安装在虚拟机上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |此策略审核是否有任何 Windows/Linux 虚拟机 (VM) 没有安装安全中心用于监视安全漏洞和威胁的 Log Analytics 代理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Log Analytics 代理应安装在虚拟机规模集上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |安全中心从 Azure 虚拟机 (VM) 收集数据，以监视安全漏洞和威胁。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics 代理应安装在 Windows Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |如果 Log Analytics 代理未安装，此策略审核 Windows Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 托管的控制 1137 - 审核生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4344df62-88ab-4637-b97b-bcaf2ec97e7c) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1137.json) |
|[Microsoft 托管的控制 1138 - 审核生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c284fc0-268a-4f29-af44-3c126674edb4) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1138.json) |
|[Microsoft 托管的控制 1139 - 审核生成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ed62522-de00-4dda-9810-5205733d2f34) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1139.json) |
|[应在 Linux 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[应在 Windows 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[应启用网络观察程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 需要在存在虚拟网络的每个区域中创建一个网络观察程序资源组。 如果网络观察程序资源组在特定区域中不可用，则会启用警报。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[应启用 Azure Data Lake Store 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[应启用 Azure 流分析中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用 Batch 帐户中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[应启用 Data Lake Analytics 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用事件中心内的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[应启用 IoT 中心内的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[应启用 Key Vault 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |对启用资源日志进行审核。 使用此策略可在发生安全事件或网络受到安全威胁时重新创建用于调查的活动线索 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[应启用逻辑应用中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[应启用搜索服务中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[应启用虚拟机规模集中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |建议启用日志，以便在出现某个事件或遭到入侵后需要进行调查时可以重新创建活动线索。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[应使用系统分配的托管标识来部署虚拟机的来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |来宾配置扩展需要系统分配的托管标识。 如果安装了来宾配置扩展，但没有系统分配的托管标识，则此策略作用域内的 Azure 虚拟机是不合规的。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md) |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="system-wide--time-correlated-audit-trail"></a>系统范围/时间相关的审核线索

**ID**：NIST SP 800-53 Rev. 4 AU-12 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用 SQL 服务器上的审核](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |应在 SQL 服务器上启用审核以跟踪服务器上所有数据库的数据库活动，并将其保存在审核日志中。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[你的订阅应启用 Log Analytics 代理自动预配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |为了监视安全漏洞和威胁，Azure 安全中心会从 Azure 虚拟机收集数据。 数据是使用 Log Analytics 代理收集的，该代理以前称为 Microsoft Monitoring Agent (MMA)，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到 Log Analytics 工作区以用于分析。 建议启用自动预配，将代理自动部署到所有受支持的 Azure VM 和任何新创建的 VM。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc 的 Azure Defender 扩展为你启用了 Arc 的 Kubernetes 群集提供威胁防护。 该扩展从群集中的节点收集数据，并将数据发送到云中的 Azure Defender for Kubernetes 后端做进一步分析。 若要了解详细信息，请访问 [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)。 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[应启用应用程序服务中的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |审核确认已在应用上启用诊断日志。 如果发生安全事件或网络遭泄露，这样便可以重新创建活动线索用于调查目的。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[应在计算机上安装来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |若要确保安全配置计算机的来宾内设置，请安装来宾配置扩展。 该扩展监视的来宾内设置包括操作系统的配置、应用程序配置或状态以及环境设置。 安装后，来宾内策略将可用，如“应启用 Windows 攻击防护”。 更多信息请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[应在计算机上解决 Log Analytics 代理运行状况问题](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |安全中心使用 Log Analytics 代理，它之前被称为 Microsoft Monitoring Agent (MMA)。 为了确保成功监视虚拟机，需要确保此代理安装在虚拟机上，并能正确地将安全事件收集到配置的工作区中。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics 代理应安装在 Linux Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |如果 Log Analytics 代理未安装，此策略审核 Linux Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Log Analytics 代理应安装在虚拟机上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |此策略审核是否有任何 Windows/Linux 虚拟机 (VM) 没有安装安全中心用于监视安全漏洞和威胁的 Log Analytics 代理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Log Analytics 代理应安装在虚拟机规模集上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |安全中心从 Azure 虚拟机 (VM) 收集数据，以监视安全漏洞和威胁。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics 代理应安装在 Windows Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |如果 Log Analytics 代理未安装，此策略审核 Windows Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 托管的控制 1140 - 审核生成 \| 系统范围/时间相关的审核跟踪](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90d8b8ad-8ee3-4db7-913f-2a53fcff5316) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1140.json) |
|[应在 Linux 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[应在 Windows 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[应启用网络观察程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 需要在存在虚拟网络的每个区域中创建一个网络观察程序资源组。 如果网络观察程序资源组在特定区域中不可用，则会启用警报。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[应启用 Azure Data Lake Store 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[应启用 Azure 流分析中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用 Batch 帐户中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[应启用 Data Lake Analytics 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[应启用事件中心内的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[应启用 IoT 中心内的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[应启用 Key Vault 中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |对启用资源日志进行审核。 使用此策略可在发生安全事件或网络受到安全威胁时重新创建用于调查的活动线索 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[应启用逻辑应用中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[应启用搜索服务中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[应启用服务总线中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |对启用资源日志进行审核。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists、Disabled |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[应启用虚拟机规模集中的资源日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |建议启用日志，以便在出现某个事件或遭到入侵后需要进行调查时可以重新创建活动线索。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[应使用系统分配的托管标识来部署虚拟机的来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |来宾配置扩展需要系统分配的托管标识。 如果安装了来宾配置扩展，但没有系统分配的托管标识，则此策略作用域内的 Azure 虚拟机是不合规的。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md) |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="changes-by-authorized-individuals"></a>由已获授权的个人进行的更改

**ID**：NIST SP 800-53 Rev. 4 AU-12 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1141 - 审核生成 \| 已获授权的个人的更改](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fdefbf4-93e7-4513-bc95-c1858b7093e0) |由 Microsoft 实现此审核和问责制控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1141.json) |

## <a name="security-assessment-and-authorization"></a>安全评估和授权

### <a name="security-assessment-and-authorization-policy-and-procedures"></a>安全评估和授权策略和过程

**ID**：NIST SP 800-53 Rev. 4 CA-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1142 - 安全评估和授权政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01524fa8-4555-48ce-ba5f-c3b8dcef5147) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1142.json) |
|[Microsoft 托管的控制 1143 - 安全评估和授权政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c6de11b-5f51-4f7c-8d83-d2467c8a816e) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1143.json) |

### <a name="security-assessments"></a>安全评估

**ID**：NIST SP 800-53 Rev. 4 CA-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1144 - 安全评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fa15ff1-a693-4ee4-b094-324818dc9a51) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1144.json) |
|[Microsoft 托管的控制 1145 - 安全评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0724970-9c75-4a64-a225-a28002953f28) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1145.json) |
|[Microsoft 托管的控制 1146 - 安全评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd83410c-ecb6-4547-8f14-748c3cbdc7ac) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1146.json) |
|[Microsoft 托管的控制 1147 - 安全评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fef824a-29a8-4a4c-88fc-420a39c0d541) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1147.json) |

### <a name="independent-assessors"></a>独立评估者

**ID**：NIST SP 800-53 Rev. 4 CA-2 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1148 - 安全评估 \| 独立评估师](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e62650-c7c2-4786-bdfa-17edc1673902) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1148.json) |

### <a name="specialized-assessments"></a>专业评估

**ID**：NIST SP 800-53 Rev. 4 CA-2 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1149 - 安全评估 \| 专业评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e1b855b-a013-481a-aeeb-2bcb129fd35d) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1149.json) |

### <a name="external-organizations"></a>外部组织

**ID**：NIST SP 800-53 Rev. 4 CA-2 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1150 - 安全评估 \| 外部组织](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd630429d-e763-40b1-8fba-d20ba7314afb) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1150.json) |

### <a name="system-interconnections"></a>系统互连

**ID**：NIST SP 800-53 Rev. 4 CA-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1151 - 系统互连](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F347e3b69-7fb7-47df-a8ef-71a1a7b44bca) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1151.json) |
|[Microsoft 托管的控制 1152 - 系统互连](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbeff0acf-7e67-40b2-b1ca-1a0e8205cf1b) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1152.json) |
|[Microsoft 托管的控制 1153 - 系统互连](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61cf3125-142c-4754-8a16-41ab4d529635) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1153.json) |

### <a name="unclassified-non-national-security-system-connections"></a>未分类的非国家安全系统连接

**ID**：NIST SP 800-53 Rev. 4 CA-3 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1154 - 系统互连 \| 未分类非国家安全系统连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe757ceb9-93b3-45fe-a4f4-f43f64f1ac5a) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1154.json) |

### <a name="restrictions-on-external-system-connections"></a>外部系统连接的限制

**ID**：NIST SP 800-53 Rev. 4 CA-3 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1155 - 系统互连 \| 外部系统连接限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d33f9f1-12d0-46ad-9fbd-8f8046694977) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1155.json) |

### <a name="plan-of-action-and-milestones"></a>操作和里程碑计划

**ID**：NIST SP 800-53 Rev. 4 CA-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1156 - 操作和里程碑计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d52e864-9a3b-41ee-8f03-520815fe5378) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1156.json) |
|[Microsoft 托管的控制 1157 - 操作和里程碑计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F15495367-cf68-464c-bbc3-f53ca5227b7a) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1157.json) |

### <a name="security-authorization"></a>安全授权

**ID**：NIST SP 800-53 Rev. 4 CA-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1158 - 安全授权](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffff50cf2-28eb-45b4-b378-c99412688907) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1158.json) |
|[Microsoft 托管的控制 1159 - 安全授权](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0925f098-7877-450b-8ba4-d1e55f2d8795) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1159.json) |
|[Microsoft 托管的控制 1160 - 安全授权](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e797ca6-2aa8-4333-b335-7036f1110c05) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1160.json) |

### <a name="continuous-monitoring"></a>持续监视

**ID**：NIST SP 800-53 Rev. 4 CA-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1161 - 持续监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2f8f6c6-dde4-436b-a79d-bc50e129eb3a) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1161.json) |
|[Microsoft 托管的控制 1162 - 持续监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5770f3d6-8c2b-4f6f-bf0e-c8c8fc36d592) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1162.json) |
|[Microsoft 托管的控制 1163 - 持续监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F961663a1-8a91-4e59-b6f5-1eee57c0f49c) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1163.json) |
|[Microsoft 托管的控制 1164 - 持续监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fb8d3ce-9e96-481c-9c68-88d4e3019310) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1164.json) |
|[Microsoft 托管的控制 1165 - 持续监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47e10916-6c9e-446b-b0bd-ff5fd439d79d) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1165.json) |
|[Microsoft 托管的控制 1166 - 持续监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb02733d-3cc5-4bb0-a6cd-695ba2c2272e) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1166.json) |
|[Microsoft 托管的控制 1167 - 持续监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcbb2be76-4891-430b-95a7-ca0b0a3d1300) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1167.json) |

### <a name="independent-assessment"></a>独立评估

**ID**：NIST SP 800-53 Rev. 4 CA-7 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1168 - 持续监视 \| 独立评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82409f9e-1f32-4775-bf07-b99d53a91b06) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1168.json) |

### <a name="trend-analyses"></a>趋势分析

**ID**：NIST SP 800-53 Rev. 4 CA-7 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1169 - 持续监视 \| 趋势分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7ba2cb3-5675-4468-8b50-8486bdd998a5) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1169.json) |

### <a name="penetration-testing"></a>渗透测试

**ID**：NIST SP 800-53 Rev. 4 CA-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1170 - 渗透测试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b78b9b3-ee3c-48e0-a243-ed6dba5b7a12) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1170.json) |

### <a name="independent-penetration-agent-or-team"></a>独立渗透代理或团队

**ID**：NIST SP 800-53 Rev. 4 CA-8 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1171 - 渗透测试 \| 独立渗透机构或团队](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d4820bc-8b61-4982-9501-2123cb776c00) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1171.json) |

### <a name="internal-system-connections"></a>内部系统连接

**ID**：NIST SP 800-53 Rev. 4 CA-9

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1172 - 内部系统连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb43e946e-a4c8-4b92-8201-4a39331db43c) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1172.json) |
|[Microsoft 托管的控制 1173 - 内部系统连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4aff9e7-2e60-46fa-86be-506b79033fc5) |由 Microsoft 实现此安全评估和授权控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1173.json) |

## <a name="configuration-management"></a>配置管理

### <a name="configuration-management-policy-and-procedures"></a>配置管理策略和过程

**ID**：NIST SP 800-53 Rev. 4 CM-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1174 - 配置管理政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42a9a714-8fbb-43ac-b115-ea12d2bd652f) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1174.json) |
|[Microsoft 托管的控制 1175 - 配置管理政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6dab4254-c30d-4bb7-ae99-1d21586c063c) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1175.json) |

### <a name="baseline-configuration"></a>基线配置

**ID**：NIST SP 800-53 Rev. 4 CM-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1176 - 基线配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc30690a5-7bf3-467f-b0cd-ef5c7c7449cd) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1176.json) |

### <a name="reviews-and-updates"></a>评审和更新

**ID**：NIST SP 800-53 Rev. 4 CM-2 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1177 - 基线配置 \| 评审和更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63dbc7a8-e20b-4d38-b857-a7f6c0cd94bc) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1177.json) |
|[Microsoft 托管的控制 1178 - 基线配置 \| 评审和更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7818b8f4-47c6-441a-90ae-12ce04e99893) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1178.json) |
|[Microsoft 托管的控制 1179 - 基线配置 \| 评审和更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f9ce557-c8ab-4e6c-bb2c-9b8ed002c46c) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1179.json) |

### <a name="automation-support-for-accuracy--currency"></a>准确度/货币的自动化支持

**ID**：NIST SP 800-53 Rev. 4 CM-2 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1180 - 基线配置 \| 准确度/货币的自动化支持](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F874e7880-a067-42a7-bcbe-1a340f54c8cc) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1180.json) |

### <a name="retention-of-previous-configurations"></a>保留以前的配置

**ID**：NIST SP 800-53 Rev. 4 CM-2 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1181 - 基线配置 \| 保留以前的配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21839937-d241-4fa5-95c6-b669253d9ab9) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1181.json) |

### <a name="configure-systems-components-or-devices-for-high-risk-areas"></a>为高风险区域配置系统、组件或设备

**ID**：NIST SP 800-53 Rev. 4 CM-2 (7)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1182 - 基线配置 \| 为高风险区域配置系统、组件或设备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f34f554-da4b-4786-8d66-7915c90893da) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1182.json) |
|[Microsoft 托管的控制 1183 - 基线配置 \| 为高风险区域配置系统、组件或设备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5352e3e0-e63a-452e-9e5f-9c1d181cff9c) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1183.json) |

### <a name="configuration-change-control"></a>配置变更控制措施

**ID**：NIST SP 800-53 Rev. 4 CM-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1184 - 配置变更控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13579d0e-0ab0-4b26-b0fb-d586f6d7ed20) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1184.json) |
|[Microsoft 托管的控制 1185 - 配置变更控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6420cd73-b939-43b7-9d99-e8688fea053c) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1185.json) |
|[Microsoft 托管的控制 1186 - 配置变更控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb95ba3bd-4ded-49ea-9d10-c6f4b680813d) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1186.json) |
|[Microsoft 托管的控制 1187 - 配置变更控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9f2b2f9e-4ba6-46c3-907f-66db138b6f85) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1187.json) |
|[Microsoft 托管的控制 1188 - 配置变更控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb20548a-c926-4e4d-855c-bcddc6faf95e) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1188.json) |
|[Microsoft 托管的控制 1189 - 配置变更控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee45e02a-4140-416c-82c4-fecfea660b9d) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1189.json) |
|[Microsoft 托管的控制 1190 - 配置变更控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc66a3d1e-465b-4f28-9da5-aef701b59892) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1190.json) |

### <a name="automated-document--notification--prohibition-of-changes"></a>自动记录/通知/禁止变更

**ID**：NIST SP 800-53 Rev. 4 CM-3 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1191 - 配置变更控制 \| 自动记录/通知/禁止变更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f26a61b-a74d-467c-99cf-63644db144f7) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1191.json) |
|[Microsoft 托管的控制 1192 - 配置变更控制 \| 自动记录/通知/禁止变更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ebd97f7-b105-4f50-8daf-c51465991240) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1192.json) |
|[Microsoft 托管的控制 1193 - 配置变更控制 \| 自动记录/通知/禁止变更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5fd629f-3075-4cae-ab53-bad65495a4ac) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1193.json) |
|[Microsoft 托管的控制 1194 - 配置变更控制 \| 自动记录/通知/禁止变更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc34667f-397e-4a65-9b72-d0358f0b6b09) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1194.json) |
|[Microsoft 托管的控制 1195 - 配置变更控制 \| 自动记录/通知/禁止变更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1e1d65c-1013-4484-bd54-991332e6a0d2) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1195.json) |
|[Microsoft 托管的控制 1196 - 配置变更控制 \| 自动记录/通知/禁止变更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e7f4ea4-dd62-44f6-8886-ac6137cf52b0) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1196.json) |

### <a name="test--validate--document-changes"></a>测试/验证/记录变更

**ID**：NIST SP 800-53 Rev. 4 CM-3 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1197 - 配置变更控制 \| 测试/验证/记录变更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa20d2eaa-88e2-4907-96a2-8f3a05797e5c) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1197.json) |

### <a name="security-representative"></a>安全代表

**ID**：NIST SP 800-53 Rev. 4 CM-3 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1198 - 配置变更控制 \| 安全代表](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56be5c3-660b-4c61-9078-f67cf072c356) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1198.json) |

### <a name="cryptography-management"></a>加密管理

**ID**：NIST SP 800-53 Rev. 4 CM-3 (6)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1199 - 配置变更控制 \| 加密管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9a08d1c-09b1-48f1-90ea-029bbdf7111e) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1199.json) |

### <a name="security-impact-analysis"></a>安全影响分析

**ID**：NIST SP 800-53 Rev. 4 CM-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1200 - 安全影响分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe98fe9d7-2ed3-44f8-93b7-24dca69783ff) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1200.json) |

### <a name="separate-test-environments"></a>单独的测试环境

**ID**：NIST SP 800-53 Rev. 4 CM-4 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1201 - 安全影响分析 \| 单独的测试环境](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7daef997-fdd3-461b-8807-a608a6dd70f1) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1201.json) |

### <a name="access-restrictions-for-change"></a>变更的访问限制

**ID**：NIST SP 800-53 Rev. 4 CM-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1202 - 变更的访问限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40a2a83b-74f2-4c02-ae65-f460a5d2792a) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1202.json) |

### <a name="automated-access-enforcement--auditing"></a>自动化访问强制执行/审核

**ID**：NIST SP 800-53 Rev. 4 CM-5 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1203 - 变更的访问限制 \| 强制执行/审核自动化访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9012d14-e3e6-4d7b-b926-9f37b5537066) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1203.json) |

### <a name="review-system-changes"></a>查看系统变更

**ID**：NIST SP 800-53 Rev. 4 CM-5 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1204 - 变更的访问限制 \| 查看系统变更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f4f6750-d1ab-4a4c-8dfd-af3237682665) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1204.json) |

### <a name="signed-components"></a>已签名的组件

**ID**：NIST SP 800-53 Rev. 4 CM-5 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1205 - 变更的访问限制 \| 已签名的组件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b070cab-0fb8-4e48-ad29-fc90b4c2797c) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1205.json) |

### <a name="limit-production--operational-privileges"></a>限制生产/操作权限

**ID**：NIST SP 800-53 Rev. 4 CM-5 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1206 - 变更的访问限制 \| 限制生产/操作权限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0de232d-02a0-4652-872d-88afb4ae5e91) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1206.json) |
|[Microsoft 托管的控制 1207 - 变更的访问限制 \| 限制生产/操作权限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8713a0ed-0d1e-4d10-be82-83dffb39830e) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1207.json) |

### <a name="configuration-settings"></a>配置设置

**ID**：NIST SP 800-53 Rev. 4 CM-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在群集上安装并启用用于 Kubernetes 服务 (AKS) 的 Azure Policy 加载项](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a15ec92-a229-4763-bb14-0ea34a568f8d) |用于 Kubernetes 服务 (AKS) 的 Azure Policy 加载项扩展了 Gatekeeper v3（用于开放策略代理 (OPA) 的许可控制器 Webhook），以集中、一致的方式在群集上应用大规模强制措施和安全措施。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_AzurePolicyAddOn_Audit.json) |
|[CORS 不应允许所有资源都能访问 API 应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |跨源资源共享 (CORS) 不应允许所有域都能访问你的 API 应用。 仅允许所需的域与 API 应用交互。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |
|[CORS 不应允许所有资源都能访问函数应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |跨源资源共享 (CORS) 不应允许所有域都能访问你的函数应用。 仅允许所需的域与函数应用交互。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |
|[CORS 不应允许所有资源都能访问你的 Web 应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |跨源资源共享 (CORS) 不应允许所有域都能访问你的 Web 应用程序。 仅允许所需的域与 Web 应用交互。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[确保 API 应用的“客户端证书(传入客户端证书)”设置为“打开”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |客户端证书允许应用请求传入请求的证书。 只有具有有效证书的客户端才能访问该应用。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[确保 WEB 应用的“客户端证书(传入客户端证书)”设置为“打开”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |客户端证书允许应用请求传入请求的证书。 只有具有有效证书的客户端才能访问该应用。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[确保函数应用已启用“客户端证书（传入客户端证书）”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |客户端证书允许应用请求传入请求的证书。 只有具有有效证书的客户端才能访问该应用。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |
|[Kubernetes 群集容器 CPU 和内存资源限制不得超过指定的限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe345eecc-fa47-480f-9e88-67dcc122b164) |强制实施容器 CPU 和内存资源限制，以防止 Kubernetes 群集中发生资源耗尽攻击。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[6.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits.json) |
|[Kubernetes 群集容器不得共享主机进程 ID 命名空间或主机 IPC 命名空间](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8) |阻止 Pod 容器在 Kubernetes 群集中共享主机进程 ID 命名空间和主机 IPC 命名空间。 此建议是旨在提高 Kubernetes 环境安全性的 CIS 5.2.2 和 CIS 5.2.3 的一部分。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/BlockHostNamespace.json) |
|[Kubernetes 群集容器应只侦听允许的端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F440b515e-a580-421e-abeb-b159a61ddcbc) |将容器限制为只侦听允许的端口，以确保对 Kubernetes 群集进行的访问安全。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[6.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts.json) |
|[Kubernetes 群集容器只应使用允许的 AppArmor 配置文件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) |容器只应使用 Kubernetes 群集中允许的 AppArmor 配置文件。 此建议是旨在提高 Kubernetes 环境安全性的 Pod 安全策略的一部分。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/EnforceAppArmorProfile.json) |
|[Kubernetes 群集容器只应使用允许的功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) |限制功能以减小 Kubernetes 群集中容器的受攻击面。 此建议是旨在提高 Kubernetes 环境安全性的 CIS 5.2.8 和 CIS 5.2.9 的一部分。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedCapabilities.json) |
|[Kubernetes 群集容器应只使用允许的映像](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffebd0533-8e55-448f-b837-bd0e06f16469) |使用受信任注册表中的映像，以降低 Kubernetes 群集暴露于未知漏洞、安全问题和恶意映像的风险。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[6.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages.json) |
|[Kubernetes 群集容器应使用只读根文件系统运行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) |运行使用只读根文件系统的容器，以防止在运行时发生更改而导致恶意二进制文件添加到 Kubernetes 群集中的 PATH。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ReadOnlyRootFileSystem.json) |
|[Kubernetes 群集 Pod hostPath 卷只应使用允许的主机路径](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75) |仅限将 Pod HostPath 卷装载到 Kubernetes 群集中允许的主机路径。 此建议是旨在提高 Kubernetes 环境安全性的 Pod 安全策略的一部分。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedHostPaths.json) |
|[Kubernetes 群集 Pod 和容器只应使用批准的用户 ID 和组 ID 运行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) |控制 Pod 和容器可以使用哪些用户、主要组、补充组和文件系统组 ID 在 Kubernetes 群集中运行。 此建议是旨在提高 Kubernetes 环境安全性的 Pod 安全策略的一部分。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedUsersGroups.json) |
|[Kubernetes 群集 Pod 只应使用批准的主机网络和端口范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |限制 Pod 在 Kubernetes 群集中对主机网络和允许的主机端口范围的访问。 此建议是旨在提高 Kubernetes 环境安全性的 CIS 5.2.4 的一部分。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|[Kubernetes 群集服务应只侦听允许的端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F233a2a17-77ca-4fb1-9b6b-69223d272a44) |将服务限制为只侦听允许的端口，以确保对 Kubernetes 群集进行的访问安全。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[6.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts.json) |
|[Kubernetes 群集不应允许特权容器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4) |不允许在 Kubernetes 群集中创建特权容器。 此建议是旨在提高 Kubernetes 环境安全性的 CIS 5.2.1 的一部分。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[7.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege.json) |
|[Kubernetes 群集不得允许容器特权提升](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) |不允许容器使用特权提升运行，从而进入 Kubernetes 群集的根。 此建议是旨在提高 Kubernetes 环境安全性的 CIS 5.2.5 的一部分。 此策略通常适用于 Kubernetes 服务 (AKS) 以及 AKS 引擎和已启用 Azure Arc 的 Kubernetes 的预览版。 有关详细信息，请参阅 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)。 |审核、拒绝、已禁用 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilegeEscalation.json) |
|[Linux 计算机应符合 Azure 计算安全基线的要求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果计算机未根据 Azure 计算安全基线中的某条建议进行正确配置，则计算机不合规。 |AuditIfNotExists、Disabled |[1.1.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AzureLinuxBaseline_AINE.json) |
|[Microsoft 托管的控制 1208 - 配置设置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5ea87673-d06b-456f-a324-8abcee5c159f) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1208.json) |
|[Microsoft 托管的控制 1209 - 配置设置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fce669c31-9103-4552-ae9c-cdef4e03580d) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1209.json) |
|[Microsoft 托管的控制 1210 - 配置设置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3502c968-c490-4570-8167-1476f955e9b8) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1210.json) |
|[Microsoft 托管的控制 1211 - 配置设置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a8b9dc8-6b00-4701-aa96-bba3277ebf50) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1211.json) |
|[应为 API 应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |远程调试需要在 API 应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[应对函数应用禁用远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |远程调试需要在函数应用上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[应禁用 Web 应用程序的远程调试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |远程调试需要在 Web 应用程序上打开入站端口。 应禁用远程调试。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[Windows 计算机应符合 Azure 计算安全基线的要求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果计算机未根据 Azure 计算安全基线中的某条建议进行正确配置，则计算机不合规。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AzureWindowsBaseline_AINE.json) |

### <a name="automated-central-management--application--verification"></a>自动化的集中管理/应用/验证

**ID**：NIST SP 800-53 Rev. 4 CM-6 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1212 - 配置设置 \| 自动化的中央管理/应用/验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d970ee-4efc-49c8-8a4e-5916940d784c) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1212.json) |

### <a name="respond-to-unauthorized-changes"></a>响应未经授权的变更

**ID**：NIST SP 800-53 Rev. 4 CM-6 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1213 - 配置设置 \| 响应未经授权的变更](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81f11e32-a293-4a58-82cd-134af52e2318) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1213.json) |

### <a name="least-functionality"></a>最少的功能

**ID**：NIST SP 800-53 Rev. 4 CM-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在计算机中启用自适应应用程序控制以定义安全应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |启用应用程序控制，以定义计算机中正在运行的已知安全应用程序列表，并在其他应用程序运行时向你发出警报。 这有助于强化计算机免受恶意软件的侵害。 为了简化配置和维护规则的过程，安全中心使用机器学习来分析在每台计算机上运行的应用程序，并建议已知安全应用程序的列表。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[应更新自适应应用程序控制策略中的允许列表规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |监视配置为供 Azure 安全中心的自适应应用程序控制进行审核的计算机组是否有行为变化。 安全中心使用机器学习来分析计算机上的运行过程，并建议已知安全应用程序的列表。 这些应用程序作为推荐的应用显示，在自适应应用程序控制策略中允许使用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Microsoft 托管的控制 1214 - 最少的功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff714a4e2-b580-47b6-ae8c-f2812d3750f3) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1214.json) |
|[Microsoft 托管的控制 1215 - 最少的功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88fc93e8-4745-4785-b5a5-b44bb92c44ff) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1215.json) |

### <a name="periodic-review"></a>定期评审

**ID**：NIST SP 800-53 Rev. 4 CM-7 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1216 - 最少的功能 \| 定期评审](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7894fe6a-f5cb-44c8-ba90-c3f254ff9484) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1216.json) |
|[Microsoft 托管的控制 1217 - 最少的功能 \| 定期评审](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedea4f20-b02c-4115-be75-86c080e5c0ed) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1217.json) |

### <a name="prevent-program-execution"></a>防止程序执行

**ID**：NIST SP 800-53 Rev. 4 CM-7 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在计算机中启用自适应应用程序控制以定义安全应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |启用应用程序控制，以定义计算机中正在运行的已知安全应用程序列表，并在其他应用程序运行时向你发出警报。 这有助于强化计算机免受恶意软件的侵害。 为了简化配置和维护规则的过程，安全中心使用机器学习来分析在每台计算机上运行的应用程序，并建议已知安全应用程序的列表。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[应更新自适应应用程序控制策略中的允许列表规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |监视配置为供 Azure 安全中心的自适应应用程序控制进行审核的计算机组是否有行为变化。 安全中心使用机器学习来分析计算机上的运行过程，并建议已知安全应用程序的列表。 这些应用程序作为推荐的应用显示，在自适应应用程序控制策略中允许使用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft 托管的控制 1218 - 最少的功能 \| 防止程序执行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a1d0394-b9f5-493e-9e83-563fd0ac4df8) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1218.json) |

### <a name="authorized-software--whitelisting"></a>授权软件/允许列表

**ID**：NIST SP 800-53 Rev. 4 CM-7 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在计算机中启用自适应应用程序控制以定义安全应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |启用应用程序控制，以定义计算机中正在运行的已知安全应用程序列表，并在其他应用程序运行时向你发出警报。 这有助于强化计算机免受恶意软件的侵害。 为了简化配置和维护规则的过程，安全中心使用机器学习来分析在每台计算机上运行的应用程序，并建议已知安全应用程序的列表。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[应更新自适应应用程序控制策略中的允许列表规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |监视配置为供 Azure 安全中心的自适应应用程序控制进行审核的计算机组是否有行为变化。 安全中心使用机器学习来分析计算机上的运行过程，并建议已知安全应用程序的列表。 这些应用程序作为推荐的应用显示，在自适应应用程序控制策略中允许使用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft 托管的控制 1219 - 最少的功能 \| 授权软件/允许列表](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a39ac75-622b-4c88-9a3f-45b7373f7ef7) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1219.json) |
|[Microsoft 托管的控制 1220 - 最少的功能 \| 授权软件/允许列表](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc40f31a7-81e1-4130-99e5-a02ceea2a1d6) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1220.json) |
|[Microsoft 托管的控制 1221 - 最少的功能 \| 授权软件/允许列表](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22589a07-0007-486a-86ca-95355081ae2a) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1221.json) |

### <a name="information-system-component-inventory"></a>信息系统组件清单

**ID**：NIST SP 800-53 Rev. 4 CM-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1222 - 信息系统组件清单](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb39e62f-6bda-4558-8088-ec03d5670914) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1222.json) |
|[Microsoft 托管的控制 1223 - 信息系统组件清单](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a1bb01-ad5a-49c1-aad3-b0c893b2ec3a) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1223.json) |

### <a name="updates-during-installations--removals"></a>在安装/删除过程中更新

**ID**：NIST SP 800-53 Rev. 4 CM-8 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1224 - 信息系统组件清单 \| 在安装/删除过程中更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28cfa30b-7f72-47ce-ba3b-eed26c8d2c82) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1224.json) |

### <a name="automated-maintenance"></a>自动化维护

**ID**：NIST SP 800-53 Rev. 4 CM-8 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1225 - 信息系统组件清单 \| 自动维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d096fe0-f510-4486-8b4d-d17dc230980b) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1225.json) |

### <a name="automated-unauthorized-component-detection"></a>自动检测未经授权的组件

**ID**：NIST SP 800-53 Rev. 4 CM-8 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1226 - 信息系统组件清单 \| 自动检测未经授权的组件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc158eb1c-ae7e-4081-8057-d527140c4e0c) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1226.json) |
|[Microsoft 托管的控制 1227 - 信息系统组件清单 \| 自动检测未经授权的组件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03b78f5e-4877-4303-b0f4-eb6583f25768) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1227.json) |

### <a name="accountability-information"></a>责任制信息

**ID**：NIST SP 800-53 Rev. 4 CM-8 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1228 - 信息系统组件清单 \| 问责制信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F39c54140-5902-4079-8bb5-ad31936fe764) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1228.json) |

### <a name="no-duplicate-accounting-of-components"></a>没有重复的会计组件

**ID**：NIST SP 800-53 Rev. 4 CM-8 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1229 - 信息系统组件清单 \| 没有重复的组件核算](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03752212-103c-4ab8-a306-7e813022ca9d) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1229.json) |

### <a name="configuration-management-plan"></a>配置管理计划

**ID**：NIST SP 800-53 Rev. 4 CM-9

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1230 - 配置管理计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11158848-f679-4e9b-aa7b-9fb07d945071) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1230.json) |
|[Microsoft 托管的控制 1231 - 配置管理计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F244e0c05-cc45-4fe7-bf36-42dcf01f457d) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1231.json) |
|[Microsoft 托管的控制 1232 - 配置管理计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F396ba986-eac1-4d6d-85c4-d3fda6b78272) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1232.json) |
|[Microsoft 托管的控制 1233 - 配置管理计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d79001f-95fe-45d0-8736-f217e78c1f57) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1233.json) |

### <a name="software-usage-restrictions"></a>软件使用限制

**ID**：NIST SP 800-53 Rev. 4 CM-10

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在计算机中启用自适应应用程序控制以定义安全应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |启用应用程序控制，以定义计算机中正在运行的已知安全应用程序列表，并在其他应用程序运行时向你发出警报。 这有助于强化计算机免受恶意软件的侵害。 为了简化配置和维护规则的过程，安全中心使用机器学习来分析在每台计算机上运行的应用程序，并建议已知安全应用程序的列表。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[应更新自适应应用程序控制策略中的允许列表规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |监视配置为供 Azure 安全中心的自适应应用程序控制进行审核的计算机组是否有行为变化。 安全中心使用机器学习来分析计算机上的运行过程，并建议已知安全应用程序的列表。 这些应用程序作为推荐的应用显示，在自适应应用程序控制策略中允许使用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft 托管的控制 1234 - 软件使用限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb293f881-361c-47ed-b997-bc4e2296bc0b) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1234.json) |
|[Microsoft 托管的控制 1235 - 软件使用限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc49c610b-ece4-44b3-988c-2172b70d6e46) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1235.json) |
|[Microsoft 托管的控制 1236 - 软件使用限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ba3ed84-c768-4e18-b87c-34ef1aff1b57) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1236.json) |

### <a name="open-source-software"></a>开放源代码软件

**ID**：NIST SP 800-53 Rev. 4 CM-10 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1237 - 软件使用限制 \| 开放源代码软件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe80b6812-0bfa-4383-8223-cdd86a46a890) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1237.json) |

### <a name="user-installed-software"></a>用户安装的软件

**ID**：NIST SP 800-53 Rev. 4 CM-11

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在计算机中启用自适应应用程序控制以定义安全应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |启用应用程序控制，以定义计算机中正在运行的已知安全应用程序列表，并在其他应用程序运行时向你发出警报。 这有助于强化计算机免受恶意软件的侵害。 为了简化配置和维护规则的过程，安全中心使用机器学习来分析在每台计算机上运行的应用程序，并建议已知安全应用程序的列表。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[应更新自适应应用程序控制策略中的允许列表规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |监视配置为供 Azure 安全中心的自适应应用程序控制进行审核的计算机组是否有行为变化。 安全中心使用机器学习来分析计算机上的运行过程，并建议已知安全应用程序的列表。 这些应用程序作为推荐的应用显示，在自适应应用程序控制策略中允许使用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft 托管的控制 1238 - 用户安装的软件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa36cedd4-3ffd-4b1f-8b18-aa71d8d87ce1) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1238.json) |
|[Microsoft 托管的控制 1239 - 用户安装的软件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0be51298-f643-4556-88af-d7db90794879) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1239.json) |
|[Microsoft 托管的控制 1240 - 用户安装的软件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F129eb39f-d79a-4503-84cd-92f036b5e429) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1240.json) |

### <a name="alerts-for-unauthorized-installations"></a>未经授权的安装的警报

**ID**：NIST SP 800-53 Rev. 4 CM-11 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1241 - 用户安装的软件 \| 未经授权的安装的警报](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feca4d7b2-65e2-4e04-95d4-c68606b063c3) |由 Microsoft 实现此配置管理控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1241.json) |

## <a name="contingency-planning"></a>应变规划

### <a name="contingency-planning-policy-and-procedures"></a>应变计划策略和流程

**ID**：NIST SP 800-53 Rev. 4 CP-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1242 - 应变计划政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3b3293-667a-445e-a722-fa0b0afc0958) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1242.json) |
|[Microsoft 托管的控制 1243 - 应变计划政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca9a4469-d6df-4ab2-a42f-1213c396f0ec) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1243.json) |

### <a name="contingency-plan"></a>应变计划

**ID**：NIST SP 800-53 Rev. 4 CP-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1244 - 应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a13a8f8-c163-4b1b-8554-d63569dab937) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1244.json) |
|[Microsoft 托管的控制 1245 - 应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0e45314-57b8-4623-80cd-bbb561f59516) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1245.json) |
|[Microsoft 托管的控制 1246 - 应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F398eb61e-8111-40d5-a0c9-003df28f1753) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1246.json) |
|[Microsoft 托管的控制 1247 - 应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e666db5-b2ef-4b06-aac6-09bfce49151b) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1247.json) |
|[Microsoft 托管的控制 1248 - 应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50fc602d-d8e0-444b-a039-ad138ee5deb0) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1248.json) |
|[Microsoft 托管的控制 1249 - 应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3bf4251-0818-42db-950b-afd5b25a51c2) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1249.json) |
|[Microsoft 托管的控制 1250 - 应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8de614d8-a8b7-4f70-a62a-6d37089a002c) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1250.json) |

### <a name="coordinate-with-related-plans"></a>与相关计划协调

**ID**：NIST SP 800-53 Rev. 4 CP-2 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1251 - 应变计划 \| 与相关计划协调](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e2b3730-8c14-4081-8893-19dbb5de7348) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1251.json) |

### <a name="capacity-planning"></a>容量规划

**ID**：NIST SP 800-53 Rev. 4 CP-2 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1252 - 应变计划 \| 容量规划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa328fd72-8ff5-4f96-8c9c-b30ed95db4ab) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1252.json) |

### <a name="resume-essential-missions--business-functions"></a>恢复关键任务/业务功能

**ID**：NIST SP 800-53 Rev. 4 CP-2 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1253 - 应变计划 \| 恢复关键任务/业务功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0afce0b3-dd9f-42bb-af28-1e4284ba8311) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1253.json) |

### <a name="resume-all-missions--business-functions"></a>恢复所有任务/业务功能

**ID**：NIST SP 800-53 Rev. 4 CP-2 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1254 - 应变计划 \| 恢复所有任务/业务功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F704e136a-4fe0-427c-b829-cd69957f5d2b) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1254.json) |

### <a name="continue-essential-missions--business-functions"></a>继续执行基本任务/业务功能

**ID**：NIST SP 800-53 Rev. 4 CP-2 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1255 - 应变计划 \| 继续关键任务/业务功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3793f5e-937f-44f7-bfba-40647ef3efa0) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1255.json) |

### <a name="identify-critical-assets"></a>识别关键资产

**ID**：NIST SP 800-53 Rev. 4 CP-2 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1256 - 应变计划 \| 识别关键资产](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F232ab24b-810b-4640-9019-74a7d0d6a980) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1256.json) |

### <a name="contingency-training"></a>应变培训

**ID**：NIST SP 800-53 Rev. 4 CP-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1257 - 应变培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb958b241-4245-4bd6-bd2d-b8f0779fb543) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1257.json) |
|[Microsoft 托管的控制 1258 - 应变培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7814506c-382c-4d33-a142-249dd4a0dbff) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1258.json) |
|[Microsoft 托管的控制 1259 - 应变培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9e18f7-bad9-4d30-8806-a0c9d5e26208) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1259.json) |

### <a name="simulated-events"></a>模拟事件

**ID**：NIST SP 800-53 Rev. 4 CP-3 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1260 - 应变培训 \| 模拟事件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42254fc4-2738-4128-9613-72aaa4f0d9c3) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1260.json) |

### <a name="contingency-plan-testing"></a>应变计划测试

**ID**：NIST SP 800-53 Rev. 4 CP-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1261 - 应变计划测试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65aeceb5-a59c-4cb1-8d82-9c474be5d431) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1261.json) |
|[Microsoft 托管的控制 1262 - 应变计划测试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F831e510e-db41-4c72-888e-a0621ab62265) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1262.json) |
|[Microsoft 托管的控制 1263 - 应变计划测试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41472613-3b05-49f6-8fe8-525af113ce17) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1263.json) |

### <a name="coordinate-with-related-plans"></a>与相关计划协调

**ID**：NIST SP 800-53 Rev. 4 CP-4 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1264 - 应变计划测试 \| 与相关计划协调](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd280d4b-50a1-42fb-a479-ece5878acf19) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1264.json) |

### <a name="alternate-processing-site"></a>备用处理站点

**ID**：NIST SP 800-53 Rev. 4 CP-4 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1265 - 应变计划测试 \| 备用处理站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa18adb5b-1db6-4a5b-901a-7d3797d12972) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1265.json) |
|[Microsoft 托管的控制 1266 - 应变计划测试 \| 备用处理站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b4a3eb2-c25d-40bf-ad41-5094b6f59cee) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1266.json) |

### <a name="alternate-storage-site"></a>备用存储站点

**ID**：NIST SP 800-53 Rev. 4 CP-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为 Azure Database for MariaDB 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |通过 Azure Database for MariaDB，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以便在区域发生故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[应为 Azure Database for MySQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |通过 Azure Database for MySQL，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以便在区域发生故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[应为 Azure Database for PostgreSQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |通过 Azure Database for PostgreSQL，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以便在区域发生故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[应为存储帐户启用异地冗余存储](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf045164-79ba-4215-8f95-f8048dc1780b) |使用异地冗余创建高可用性应用程序 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |
|[应为 Azure SQL 数据库启用长期异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |此策略将审核未启用长期异地冗余备份的任何 Azure SQL 数据库。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |
|[Microsoft 托管的控制 1267 - 备用存储站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e97ba1d-be5d-4953-8da4-0cccf28f4805) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1267.json) |
|[Microsoft 托管的控制 1268 - 备用存储站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23f6e984-3053-4dfc-ab48-543b764781f5) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1268.json) |

### <a name="separation-from-primary-site"></a>从主站点分离

**ID**：NIST SP 800-53 Rev. 4 CP-6 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为 Azure Database for MariaDB 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |通过 Azure Database for MariaDB，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以便在区域发生故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[应为 Azure Database for MySQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |通过 Azure Database for MySQL，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以便在区域发生故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[应为 Azure Database for PostgreSQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |通过 Azure Database for PostgreSQL，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以便在区域发生故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[应为存储帐户启用异地冗余存储](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf045164-79ba-4215-8f95-f8048dc1780b) |使用异地冗余创建高可用性应用程序 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |
|[应为 Azure SQL 数据库启用长期异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |此策略将审核未启用长期异地冗余备份的任何 Azure SQL 数据库。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |
|[Microsoft 托管的控制 1269 - 备用存储站点 \| 从主站点分离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19b9439d-865d-4474-b17d-97d2702fdb66) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1269.json) |

### <a name="recovery-time--point-objectives"></a>恢复时间/点目标

**ID**：NIST SP 800-53 Rev. 4 CP-6 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1270 - 备用存储站点 \| 恢复时间/点目标](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c76a39-2097-408a-b237-b279f7b4614d) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1270.json) |

### <a name="accessibility"></a>可访问性

**ID**：NIST SP 800-53 Rev. 4 CP-6 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1271 - 备用存储站点 \| 辅助功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3bfb53-9c46-4010-b3db-a7ba1296dada) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1271.json) |

### <a name="alternate-processing-site"></a>备用处理站点

**ID**：NIST SP 800-53 Rev. 4 CP-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核未配置灾难恢复的虚拟机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |审核未配置灾难恢复的虚拟机。 若要详细了解灾难恢复，请访问 [https://aka.ms/asr-doc](../../../site-recovery/index.yml)。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Microsoft 托管的控制 1272 - 备用处理站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae46cf7a-e3fd-427b-9b91-44bc78e2d9d8) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1272.json) |
|[Microsoft 托管的控制 1273 - 备用处理站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe77fcbf2-a1e8-44f1-860e-ed6583761e65) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1273.json) |
|[Microsoft 托管的控制 1274 - 备用处理站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2aee175f-cd16-4825-939a-a85349d96210) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1274.json) |

### <a name="separation-from-primary-site"></a>从主站点分离

**ID**：NIST SP 800-53 Rev. 4 CP-7 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1275 - 备用处理站点 \| 从主站点分离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa23d9d53-ad2e-45ef-afd5-e6d10900a737) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1275.json) |

### <a name="accessibility"></a>可访问性

**ID**：NIST SP 800-53 Rev. 4 CP-7 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1276 - 备用处理站点 \| 辅助功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe214e563-1206-4a43-a56b-ac5880c9c571) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1276.json) |

### <a name="priority-of-service"></a>服务优先级

**ID**：NIST SP 800-53 Rev. 4 CP-7 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1277 - 备用处理网站 \| 服务优先级](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc43e829-3d50-4a0a-aa0f-428d551862aa) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1277.json) |

### <a name="preparation-for-use"></a>使用准备

**ID**：NIST SP 800-53 Rev. 4 CP-7 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1278 - 备用处理网站 \| 使用准备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e5ef485-9e16-4c53-a475-fbb8107eac59) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1278.json) |

### <a name="telecommunications-services"></a>电信服务

**ID**：NIST SP 800-53 Rev. 4 CP-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1279 - 电信服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d00bcd6-963d-4c02-ad8e-b45fa50bf3b0) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1279.json) |

### <a name="priority-of-service-provisions"></a>服务优先级预配

**ID**：NIST SP 800-53 Rev. 4 CP-8 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1280 - 电信服务 \| 服务优先级规定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa108498-b3a8-4ffb-9e79-1107e76afad3) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1280.json) |
|[Microsoft 托管的控制 1281 - 电信服务 \| 服务优先级规定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dc459b3-0e77-45af-8d71-cfd8c9654fe2) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1281.json) |

### <a name="single-points-of-failure"></a>单一故障点

**ID**：NIST SP 800-53 Rev. 4 CP-8 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1282 - 电信服务 \| 单一故障点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34042a97-ec6d-4263-93d2-8c1c46823b2a) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1282.json) |

### <a name="separation-of-primary--alternate-providers"></a>主要/备用提供商的分离

**ID**：NIST SP 800-53 Rev. 4 CP-8 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1283 - 电信服务 \| 主要/备用提供商的分离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9172e76-7f56-46e9-93bf-75d69bdb5491) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1283.json) |

### <a name="provider-contingency-plan"></a>提供商应变计划

**ID**：NIST SP 800-53 Rev. 4 CP-8 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1284 - 电信服务 \| 提供商应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942b3e97-6ae3-410e-a794-c9c999b97c0b) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1284.json) |
|[Microsoft 托管的控制 1285 - 电信服务 \| 提供商应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01f7726b-db54-45c2-bcb5-9bd7a43796ee) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1285.json) |
|[Microsoft 托管的控制 1286 - 电信服务 \| 提供商应变计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4f9b47a-2116-4e6f-88db-4edbf22753f1) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1286.json) |

### <a name="information-system-backup"></a>信息系统备份

**ID**：NIST SP 800-53 Rev. 4 CP-9

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为虚拟机启用 Azure 备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |启用 Azure 备份，确保对 Azure 虚拟机提供保护。 Azure 备份是一种安全且经济高效的 Azure 数据保护解决方案。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[应为 Azure Database for MariaDB 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |通过 Azure Database for MariaDB，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以便在区域发生故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[应为 Azure Database for MySQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |通过 Azure Database for MySQL，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以便在区域发生故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[应为 Azure Database for PostgreSQL 启用异地冗余备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |通过 Azure Database for PostgreSQL，你可以为数据库服务器选择冗余选项。 它可以设置为异地冗余备份存储，其中数据不仅存储在托管服务器的区域内，还可以复制到配对区域，以便在区域发生故障时提供恢复选项。 只能在服务器创建期间为备份配置异地冗余存储。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[密钥保管库应启用清除保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |恶意删除密钥保管库可能会导致永久丢失数据。 你组织中的恶意内部人员可能会删除和清除密钥保管库。 清除保护通过强制实施软删除密钥保管库的强制保留期来保护你免受内部攻击。 你的组织内的任何人都无法在软删除保留期内清除你的密钥保管库。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|[密钥保管库应启用软删除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e66c121-a66a-4b1f-9b83-0fd99bf0fc2d) |在未启用软删除的情况下删除密钥保管库，将永久删除密钥保管库中存储的所有机密、密钥和证书。 意外删除密钥保管库可能会导致永久丢失数据。 软删除允许在可配置的保持期内恢复意外删除的密钥保管库。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_SoftDeleteMustBeEnabled_Audit.json) |
|[Microsoft 托管的控制 1287 - 信息系统备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F819dc6da-289d-476e-8500-7e341ef8677d) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1287.json) |
|[Microsoft 托管的控制 1288 - 信息系统备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d854c3b-a3e6-4ec9-9f0c-c7274dbaeb2f) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1288.json) |
|[Microsoft 托管的控制 1289 - 信息系统备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a724864-956a-496c-b778-637cb1d762cf) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1289.json) |
|[Microsoft 托管的控制 1290 - 信息系统备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F92f85ce9-17b7-49ea-85ee-ea7271ea6b82) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1290.json) |

### <a name="testing-for-reliability--integrity"></a>测试可靠性/完整性

**ID**：NIST SP 800-53 Rev. 4 CP-9 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1291 - 信息系统备份 \| 测试可靠性/完整性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8fd073-9c85-4ee2-a9d0-2e4ec9eb8912) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1291.json) |

### <a name="test-restoration-using-sampling"></a>使用采样测试恢复

**ID**：NIST SP 800-53 Rev. 4 CP-9 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1292 - 信息系统备份 \| 使用采样法测试还原](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd03516cf-0293-489f-9b32-a18f2a79f836) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1292.json) |

### <a name="separate-storage-for-critical-information"></a>单独存储关键信息

**ID**：NIST SP 800-53 Rev. 4 CP-9 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1293 - 信息系统备份 \| 单独存储关键信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87f7cd82-2e45-4d0f-9e2f-586b0962d142) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1293.json) |

### <a name="transfer-to-alternate-storage-site"></a>转移到备用存储站点

**ID**：NIST SP 800-53 Rev. 4 CP-9 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1294 - 信息系统备份 \| 转移到备用存储站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49dbe627-2c1e-438c-979e-dd7a39bbf81d) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1294.json) |

### <a name="information-system-recovery-and-reconstitution"></a>信息系统恢复与重建

**ID**：NIST SP 800-53 Rev. 4 CP-10

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1295 - 信息系统恢复与重建](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa895fbdb-204d-4302-9689-0a59dc42b3d9) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1295.json) |

### <a name="transaction-recovery"></a>事务恢复

**ID**：NIST SP 800-53 Rev. 4 CP-10 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1296 - 信息系统恢复与重建 \| 事务恢复](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe57b98a0-a011-4956-a79d-5d17ed8b8e48) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1296.json) |

### <a name="restore-within-time-period"></a>时间段内还原

**ID**：NIST SP 800-53 Rev. 4 CP-10 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1297 - 信息系统恢复与重建 \| 时间段内还原](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93fd8af1-c161-4bae-9ba9-f62731f76439) |由 Microsoft 实现此应变计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1297.json) |

## <a name="identification-and-authentication"></a>识别和身份验证

### <a name="identification-and-authentication-policy-and-procedures"></a>标识和身份验证政策和过程

**ID**：NIST SP 800-53 Rev. 4 IA-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1298 - 标识和身份验证政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1dc784b5-4895-4d27-9d40-a06b032bd1ee) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1298.json) |
|[Microsoft 托管的控制 1299 - 标识和身份验证政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4e54f7-9ab0-4bae-b6cc-457809948a89) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1299.json) |

### <a name="identification-and-authentication-organizational-users"></a>标识和身份验证（组织用户）

**ID**：NIST SP 800-53 Rev. 4 IA-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应该为 SQL 服务器预配 Azure Active Directory 管理员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |审核确认已为 SQL Server 预配了 Azure Active Directory 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[认知服务帐户应禁用本地身份验证方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |禁用本地身份验证方法可确保认知服务帐户需要专用于身份验证的 Azure Active Directory 标识，从而提高安全性。 有关详细信息，请访问：[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[应在 API 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[应在函数应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[应在 Web 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[应对订阅中拥有写入权限的帐户启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |为了防止帐户或资源出现违规问题，应为所有拥有写入特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[应在对订阅拥有所有者权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |为了防止帐户或资源出现违规问题，应为所有拥有所有者权限的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[应在对订阅拥有读取权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |为了防止帐户或资源出现违规问题，应为所有拥有读取特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft 托管的控制 1300 - 标识和身份验证（组织用户）](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99deec7d-5526-472e-b07c-3645a792026a) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1300.json) |
|[Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |审核 Service Fabric 中仅通过 Azure Active Directory 进行客户端身份验证 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[应使用服务主体（而不是管理证书）来保护你的订阅](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6646a0bd-e110-40ca-bb97-84fcee63c414) |通过管理证书，任何使用它们进行身份验证的人员都可管理与它们关联的订阅。 为了更安全地管理订阅，建议将服务主体和资源管理器结合使用来限制证书泄露所造成的影响。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UseServicePrincipalToProtectSubscriptions.json) |

### <a name="network-access-to-privileged-accounts"></a>特权帐户的网络访问

**ID**：NIST SP 800-53 Rev. 4 IA-2 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应对订阅中拥有写入权限的帐户启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |为了防止帐户或资源出现违规问题，应为所有拥有写入特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[应在对订阅拥有所有者权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |为了防止帐户或资源出现违规问题，应为所有拥有所有者权限的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[Microsoft 托管的控制 1301 - 标识和身份验证（组织用户）\| 网络访问特权帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8e0cc-ac23-468b-abe4-a8a1cc6d7a08) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1301.json) |

### <a name="network-access-to-non-privileged-accounts"></a>非特权帐户的网络访问

**ID**：NIST SP 800-53 Rev. 4 IA-2 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在对订阅拥有读取权限的帐户上启用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |为了防止帐户或资源出现违规问题，应为所有拥有读取特权的订阅帐户启用多重身份验证 (MFA)。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft 托管的控制 1302 - 标识和身份验证（组织用户）\| 网络访问非特权帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09828c65-e323-422b-9774-9d5c646124da) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1302.json) |

### <a name="local-access-to-privileged-accounts"></a>特权帐户的本地访问

**ID**：NIST SP 800-53 Rev. 4 IA-2 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1303 - 标识和身份验证（组织用户）\| 本地访问特权帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F80ca0a27-918a-4604-af9e-723a27ee51e8) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1303.json) |

### <a name="local-access-to-non-privileged-accounts"></a>非特权帐户的本地访问

**ID**：NIST SP 800-53 Rev. 4 IA-2 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1304 - 标识和身份验证（组织用户）\| 本地访问非特权帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ca71be3-16cb-4d39-8b50-7f8fd5e2f11b) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1304.json) |

### <a name="group-authentication"></a>组身份验证

**ID**：NIST SP 800-53 Rev. 4 IA-2 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1305 - 标识和身份验证（组织用户）\| 组身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9166a8-1722-4b8f-847c-2cf3f2618b3d) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1305.json) |

### <a name="network-access-to-privileged-accounts---replay-resistant"></a>特权帐户的网络访问 - 抵抗重放攻击

**ID**：NIST SP 800-53 Rev. 4 IA-2 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1306 - 标识和身份验证（组织用户）\| 网络访问特权帐户 - 重播](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcafc6c3c-5fc5-4c5e-a99b-a0ccb1d34eff) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1306.json) |

### <a name="network-access-to-non-privileged-accounts---replay-resistant"></a>非特权帐户的网络访问 - 抵抗重放攻击

**ID**：NIST SP 800-53 Rev. 4 IA-2 (9)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1307 - 标识和身份验证（组织用户）\| 网络访问非特权帐户 - 重播](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84e622c8-4bed-417c-84c6-b2fb0dd73682) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1307.json) |

### <a name="remote-access---separate-device"></a>远程访问 - 单独的设备

**ID**：NIST SP 800-53 Rev. 4 IA-2 (11)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1308 - 标识和身份验证（组织用户）\| 远程访问 - 单独的设备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81817e1c-5347-48dd-965a-40159d008229) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1308.json) |

### <a name="acceptance-of-piv-credentials"></a>接受 PIV 凭据

**ID**：NIST SP 800-53 Rev. 4 IA-2 (12)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1309 - 标识和身份验证（组织用户）\| 接受 PIV 凭据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff355d62b-39a8-4ba3-abf7-90f71cb3b000) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1309.json) |

### <a name="device-identification-and-authentication"></a>设备标识和身份验证

**ID**：NIST SP 800-53 Rev. 4 IA-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1310 - 设备标识和身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F450d7ede-823d-4931-a99d-57f6a38807dc) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1310.json) |

### <a name="identifier-management"></a>标识符管理

**ID**：NIST SP 800-53 Rev. 4 IA-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应该为 SQL 服务器预配 Azure Active Directory 管理员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |审核确认已为 SQL Server 预配了 Azure Active Directory 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[认知服务帐户应禁用本地身份验证方法](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |禁用本地身份验证方法可确保认知服务帐户需要专用于身份验证的 Azure Active Directory 标识，从而提高安全性。 有关详细信息，请访问：[https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[应在 API 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[应在函数应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[应在 Web 应用中使用的托管标识](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |使用托管标识以实现增强的身份验证安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[Microsoft 托管的控制 1311 - 标识符管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7568697-0c9e-4ea3-9cec-9e567d14f3c6) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1311.json) |
|[Microsoft 托管的控制 1312 - 标识符管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d6a5968-9eef-4c18-8534-376790ab7274) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1312.json) |
|[Microsoft 托管的控制 1313 - 标识符管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36220f5b-79a1-4cdb-8c74-2d2449f9a510) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1313.json) |
|[Microsoft 托管的控制 1314 - 标识符管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef0c8530-efd9-45b8-b753-f03083d06295) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1314.json) |
|[Microsoft 托管的控制 1315 - 标识符管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87116-f1a1-4edb-bfbf-14e036f8d454) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1315.json) |
|[Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |审核 Service Fabric 中仅通过 Azure Active Directory 进行客户端身份验证 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[应使用服务主体（而不是管理证书）来保护你的订阅](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6646a0bd-e110-40ca-bb97-84fcee63c414) |通过管理证书，任何使用它们进行身份验证的人员都可管理与它们关联的订阅。 为了更安全地管理订阅，建议将服务主体和资源管理器结合使用来限制证书泄露所造成的影响。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UseServicePrincipalToProtectSubscriptions.json) |

### <a name="identify-user-status"></a>标识用户状态

**ID**：NIST SP 800-53 Rev. 4 IA-4 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1316 - 标识符管理 \| 标识用户状态](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8ce14753-66e5-465d-9841-26ef55c09c0d) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1316.json) |

### <a name="authenticator-management"></a>验证器管理

**ID**：NIST SP 800-53 Rev. 4 IA-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[添加系统分配的托管标识，在没有标识的虚拟机上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持，但没有任何托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[添加系统分配的托管标识，以在具有用户分配的标识的 VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持、至少有一个用户分配的标识，但没有系统分配的托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[审核未将密码文件权限设为 0644 的 Linux 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Linux 计算机未将密码文件权限设为 0644，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[审核未存储使用可逆加密的密码的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Windows 计算机未存储使用可逆加密的密码，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[对 Linux 虚拟机进行身份验证需要 SSH 密钥](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F630c64f9-8b6b-4c64-b511-6544ceff6fd6) |虽然 SSH 本身提供加密连接，但是将密码用于 SSH 仍会使 VM 易受到暴力攻击。 通过 SSH 对 Azure Linux 虚拟机进行身份验证时，最安全的方法是使用公钥-私钥对，也称为 SSH 密钥。 了解详细信息：[https://docs.microsoft.com/azure/virtual-machines/linux/create-ssh-keys-detailed](../../../virtual-machines/linux/create-ssh-keys-detailed.md)。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxNoPasswordForSSH_AINE.json) |
|[证书应具有指定的最长有效期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |通过指定证书在密钥保管库中的最长有效时间，管理组织的符合性要求。 |审核、拒绝、已禁用 |[2.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[部署 Linux 来宾配置扩展以在 Linux VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |此策略将 Linux 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Linux 虚拟机。 Linux 来宾配置扩展是所有 Linux 来宾配置分配的先决条件，在使用任何 Linux 来宾配置策略定义之前必须将其部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[部署 Windows 来宾配置扩展以在 Windows VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |此策略将 Windows 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Windows 虚拟机。 Windows 来宾配置扩展是所有 Windows 来宾配置分配的先决条件，在使用任何 Windows 来宾配置策略定义之前必须将其部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Key Vault 密钥应具有到期日期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |应为加密密钥定义非永久性到期日期。 密钥永久有效会导致潜在攻击者有更多时间来破解密钥。 建议的安全做法是为加密密钥设置到期日期。 |Audit、Deny、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |
|[Key Vault 机密应具有到期日期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |应为机密定义非永久性到期日期。 机密永久有效会导致潜在攻击者有更多时间来破解密钥。 建议的安全做法是为机密设置到期日期。 |Audit、Deny、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |
|[Microsoft 托管的控制 1317 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8877f519-c166-47b7-81b7-8a8eb4ff3775) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1317.json) |
|[Microsoft 托管的控制 1318 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffced5fda-3bdb-4d73-bfea-0e2c80428b66) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1318.json) |
|[Microsoft 托管的控制 1319 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F66f7ae57-5560-4fc5-85c9-659f204e7a42) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1319.json) |
|[Microsoft 托管的控制 1320 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f54c732-71d4-4f93-a696-4e373eca3a77) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1320.json) |
|[Microsoft 托管的控制 1321 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb627cc6-3a9d-46b5-96b7-5fca49178a37) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1321.json) |
|[Microsoft 托管的控制 1322 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d1d971e-467e-4278-9633-c74c3d4fecc4) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1322.json) |
|[Microsoft 托管的控制 1323 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabe8f70b-680f-470c-9b86-a7edfb664ecc) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1323.json) |
|[Microsoft 托管的控制 1324 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cfea2b3-7f77-497e-ac20-0752f2ff6eee) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1324.json) |
|[Microsoft 托管的控制 1325 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1845796a-7581-49b2-ae20-443121538e19) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1325.json) |
|[Microsoft 托管的控制 1326 - 验证器管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8605fc00-1bf5-4fb3-984e-c95cec4f231d) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1326.json) |

### <a name="password-based-authentication"></a>基于密码的身份验证

**ID**：NIST SP 800-53 Rev. 4 IA-5 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[添加系统分配的托管标识，在没有标识的虚拟机上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持，但没有任何托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[添加系统分配的托管标识，以在具有用户分配的标识的 VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |此策略将系统分配的托管标识添加到托管在 Azure 中的虚拟机，这些虚拟机受来宾配置支持、至少有一个用户分配的标识，但没有系统分配的托管标识。 系统分配的托管标识是所有来宾配置分配的先决条件，在使用任何来宾配置策略定义之前必须被添加到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |modify |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[审核未将密码文件权限设为 0644 的 Linux 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Linux 计算机未将密码文件权限设为 0644，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[审核允许重用之前的 24 个密码的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Windows 计算机允许重用之前的 24 个密码，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[审核未将最长密码期限设为 70 天的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Windows 计算机未将最长密码期限设为 70 天，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[审核未将最短密码期限设为 1 天的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Windows 计算机未将最短密码期限设为 1 天，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[审核未启用密码复杂性设置的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Windows 计算机未启用密码复杂性设置，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[审核未将最短密码长度限制为 14 个字符的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Windows 计算机未将最短密码长度限制为 14 个字符，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[审核未存储使用可逆加密的密码的 Windows 计算机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |要求将先决条件部署到策略分配范围。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 如果 Windows 计算机未存储使用可逆加密的密码，则计算机不合规 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[部署 Linux 来宾配置扩展以在 Linux VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |此策略将 Linux 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Linux 虚拟机。 Linux 来宾配置扩展是所有 Linux 来宾配置分配的先决条件，在使用任何 Linux 来宾配置策略定义之前必须将其部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[部署 Windows 来宾配置扩展以在 Windows VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |此策略将 Windows 来宾配置扩展部署到托管在 Azure 中受来宾配置支持的 Windows 虚拟机。 Windows 来宾配置扩展是所有 Windows 来宾配置分配的先决条件，在使用任何 Windows 来宾配置策略定义之前必须将其部署到计算机。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft 托管的控制 1327 - 验证器管理 \| 基于密码的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03188d8f-1ae5-4fe1-974d-2d7d32ef937d) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1327.json) |
|[Microsoft 托管的控制 1328 - 验证器管理 \| 基于密码的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5c66fdc-3d02-4034-9db5-ba57802609de) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1328.json) |
|[Microsoft 托管的控制 1329 - 验证器管理 \| 基于密码的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F498f6234-3e20-4b6a-a880-cbd646d973bd) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1329.json) |
|[Microsoft 托管的控制 1330 - 验证器管理 \| 基于密码的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff75cedb2-5def-4b31-973e-b69e8c7bd031) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1330.json) |
|[Microsoft 托管的控制 1331 - 验证器管理 \| 基于密码的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05460fe2-301f-4ed1-8174-d62c8bb92ff4) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1331.json) |
|[Microsoft 托管的控制 1332 - 验证器管理 \| 基于密码的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068260be-a5e6-4b0a-a430-cd27071c226a) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1332.json) |

### <a name="pki-based-authentication"></a>基于 PKI 的身份验证

**ID**：NIST SP 800-53 Rev. 4 IA-5 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1333 - 验证器管理 \| 基于 PKI 的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3298d6bf-4bc6-4278-a95d-f7ef3ac6e594) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1333.json) |
|[Microsoft 托管的控制 1334 - 验证器管理 \| 基于 PKI 的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44bfdadc-8c2e-4c30-9c99-f005986fabcd) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1334.json) |
|[Microsoft 托管的控制 1335 - 验证器管理 \| 基于 PKI 的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F382016f3-d4ba-4e15-9716-55077ec4dc2a) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1335.json) |
|[Microsoft 托管的控制 1336 - 验证器管理 \| 基于 PKI 的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77f56280-e367-432a-a3b9-8ca2aa636a26) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1336.json) |

### <a name="in-person-or-trusted-third-party-registration"></a>个人或受信任的第三方注册

**ID**：NIST SP 800-53 Rev. 4 IA-5 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1337 - 验证器管理 \| 自行注册或受信任的第三方注册](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F463e5220-3f79-4e24-a63f-343e4096cd22) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1337.json) |

### <a name="automated-support-for-password-strength-determination"></a>自动支持密码强度确定

**ID**：NIST SP 800-53 Rev. 4 IA-5 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1338 - 验证器管理 \| 自动支持密码强度确定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c59a207-6aed-41dc-83a2-e1ff66e4a4db) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1338.json) |

### <a name="protection-of-authenticators"></a>保护验证器

**ID**：NIST SP 800-53 Rev. 4 IA-5 (6)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1339 - 验证器管理 \| 验证器的保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F367ae386-db7f-4167-b672-984ff86277c0) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1339.json) |

### <a name="no-embedded-unencrypted-static-authenticators"></a>无嵌入式未加密的静态验证器

**ID**：NIST SP 800-53 Rev. 4 IA-5 (7)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1340 - 验证器管理 \| 无嵌入式未加密的静态验证器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe51ff84b-e5ea-408f-b651-2ecc2933e4c6) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1340.json) |

### <a name="multiple-information-system-accounts"></a>多个信息系统帐户

**ID**：NIST SP 800-53 Rev. 4 IA-5 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1341 - 验证器管理 \| 多个信息系统帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34cb7e92-fe4c-4826-b51e-8cd203fa5d35) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1341.json) |

### <a name="hardware-token-based-authentication"></a>基于硬件令牌的身份验证

**ID**：NIST SP 800-53 Rev. 4 IA-5 (11)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1342 - 验证器管理 \| 基于硬件令牌的身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F283a4e29-69d5-4c94-b99e-29acf003c899) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1342.json) |

### <a name="expiration-of-cached-authenticators"></a>缓存的验证器的有效期限

**ID**：NIST SP 800-53 Rev. 4 IA-5 (13)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1343 - 验证器管理 \| 缓存验证器的有效期限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c251a55-31eb-4e53-99c6-e9c43c393ac2) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1343.json) |

### <a name="authenticator-feedback"></a>验证器反馈

**ID**：NIST SP 800-53 Rev. 4 IA-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1344 - 验证器反馈](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c895fe7-2d8e-43a2-838c-3a533a5b355e) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1344.json) |

### <a name="cryptographic-module-authentication"></a>加密模块身份验证

**ID**：NIST SP 800-53 Rev. 4 IA-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1345 - 加密模块身份验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff86aa129-7c07-4aa4-bbf5-792d93ffd9ea) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1345.json) |

### <a name="identification-and-authentication-non-organizational-users"></a>标识和身份验证（非组织用户）

**ID**：NIST SP 800-53 Rev. 4 IA-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1346 - 标识和身份验证（非组织用户）](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464dc8ce-2200-4720-87a5-dc5952924cc6) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1346.json) |

### <a name="acceptance-of-piv-credentials-from-other-agencies"></a>接受来自其他代理的 PIV 凭据

**ID**：NIST SP 800-53 Rev. 4 IA-8 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1347 - 标识和身份验证（非组织用户）\| 接受来自其他机构的 PIV 凭据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F131a2706-61e9-4916-a164-00e052056462) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1347.json) |

### <a name="acceptance-of-third-party-credentials"></a>接受第三方凭据

**ID**：NIST SP 800-53 Rev. 4 IA-8 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1348 - 标识和身份验证（非组织用户）\| 接受第三方凭据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F855ced56-417b-4d74-9d5f-dd1bc81e22d6) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1348.json) |

### <a name="use-of-ficam-approved-products"></a>使用经 Ficam 批准的产品

**ID**：NIST SP 800-53 Rev. 4 IA-8 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1349 - 标识和身份验证（非组织用户）\| 使用经 FICAM 批准的产品](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17641f70-94cd-4a5d-a613-3d1143e20e34) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1349.json) |

### <a name="use-of-ficam-issued-profiles"></a>使用 Ficam 颁发的配置文件

**ID**：NIST SP 800-53 Rev. 4 IA-8 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1350 - 标识和身份验证（非组织用户）\| 使用 FICAM 颁发的配置文件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd77fd943-6ba6-4a21-ba07-22b03e347cc4) |由 Microsoft 实现此标识和身份验证控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1350.json) |

## <a name="incident-response"></a>事件响应

### <a name="incident-response-policy-and-procedures"></a>事件响应策略和过程

**ID**：NIST SP 800-53 Rev. 4 IR-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1351 - 事件响应政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbcfb6683-05e5-4ce6-9723-c3fbe9896bdd) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1351.json) |
|[Microsoft 托管的控制 1352 - 事件响应政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F518cb545-bfa8-43f8-a108-3b7d5037469a) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1352.json) |

### <a name="incident-response-training"></a>事件响应培训

**ID**：NIST SP 800-53 Rev. 4 IR-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1353 - 事件响应培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc785ad59-f78f-44ad-9a7f-d1202318c748) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1353.json) |
|[Microsoft 托管的控制 1354 - 事件响应培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9fd92c17-163a-4511-bb96-bbb476449796) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1354.json) |
|[Microsoft 托管的控制 1355 - 事件响应培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90e01f69-3074-4de8-ade7-0fef3e7d83e0) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1355.json) |

### <a name="simulated-events"></a>模拟事件

**ID**：NIST SP 800-53 Rev. 4 IR-2 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1356 - 事件响应培训 \| 模拟事件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8829f8f5-e8be-441e-85c9-85b72a5d0ef3) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1356.json) |

### <a name="automated-training-environments"></a>自动化培训环境

**ID**：NIST SP 800-53 Rev. 4 IR-2 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1357 - 事件响应培训 \| 自动化培训环境](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe4213689-05e8-4241-9d4e-8dd1cdafd105) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1357.json) |

### <a name="incident-response-testing"></a>事件响应测试

**ID**：NIST SP 800-53 Rev. 4 IR-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1358 - 事件响应测试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feffbaeef-5bf4-400d-895e-ef8cbc0e64c7) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1358.json) |

### <a name="coordination-with-related-plans"></a>与相关计划协调

**ID**：NIST SP 800-53 Rev. 4 IR-3 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1359 - 事件响应测试 \| 与相关计划协调](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47bc7ea0-7d13-4f7c-a154-b903f7194253) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1359.json) |

### <a name="incident-handling"></a>事件处理

**ID**：NIST SP 800-53 Rev. 4 IR-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[应启用高严重性警报的电子邮件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |当某个订阅中存在潜在的安全漏洞时，若要确保组织中的相关人员收到通知，请在安全中心为高严重性警报启用电子邮件通知。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[应启用向订阅所有者发送高严重性警报的电子邮件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |当订阅中存在潜在的安全漏洞时，若要确保订阅所有者收到通知，请在安全中心设置向订阅所有者发送高严重性警报的电子邮件通知。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[Microsoft 托管的控制 1360 - 事件处理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbe5b05e7-0b82-4ebc-9eda-25e447b1a41e) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1360.json) |
|[Microsoft 托管的控制 1361 - 事件处理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ed3be1-7276-4452-9a5d-e4168565ac67) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1361.json) |
|[Microsoft 托管的控制 1362 - 事件处理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5d169442-d6ef-439b-8dca-46c2c3248214) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1362.json) |
|[订阅应有一个联系人电子邮件地址，用于接收安全问题通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |当某个订阅中存在潜在的安全漏洞时，若要确保组织中的相关人员收到通知，请设置一个安全联系人，以接收来自安全中心的电子邮件通知。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="automated-incident-handling-processes"></a>自动化事件处理过程

**ID**：NIST SP 800-53 Rev. 4 IR-4 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1363 - 事件处理 \| 自动化事件处理过程](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea3e8156-89a1-45b1-8bd6-938abc79fdfd) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1363.json) |

### <a name="dynamic-reconfiguration"></a>动态重新配置

**ID**：NIST SP 800-53 Rev. 4 IR-4 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1364 - 事件处理 \| 动态重新配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c615c2a-dc83-4dda-8220-abce7b50c9bc) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1364.json) |

### <a name="continuity-of-operations"></a>操作连续性

**ID**：NIST SP 800-53 Rev. 4 IR-4 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1365 - 事件处理 \| 操作连续性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4116891d-72f7-46ee-911c-8056cc8dcbd5) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1365.json) |

### <a name="information-correlation"></a>信息关联

**ID**：NIST SP 800-53 Rev. 4 IR-4 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1366 - 事件处理 \| 信息关联](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06c45c30-ae44-4f0f-82be-41331da911cc) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1366.json) |

### <a name="insider-threats---specific-capabilities"></a>内部威胁 - 特定力量

**ID**：NIST SP 800-53 Rev. 4 IR-4 (6)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1367 - 事件处理 \| 内部威胁 - 特定力量](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F435b2547-6374-4f87-b42d-6e8dbe6ae62a) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1367.json) |

### <a name="correlation-with-external-organizations"></a>与外部组织关联

**ID**：NIST SP 800-53 Rev. 4 IR-4 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1368 - 事件处理 \| 与外部组织关联](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f32da-0ace-4603-8d1b-7be5a3a702de) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1368.json) |

### <a name="incident-monitoring"></a>事件监视

**ID**：NIST SP 800-53 Rev. 4 IR-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[应启用高严重性警报的电子邮件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |当某个订阅中存在潜在的安全漏洞时，若要确保组织中的相关人员收到通知，请在安全中心为高严重性警报启用电子邮件通知。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[应启用向订阅所有者发送高严重性警报的电子邮件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |当订阅中存在潜在的安全漏洞时，若要确保订阅所有者收到通知，请在安全中心设置向订阅所有者发送高严重性警报的电子邮件通知。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[Microsoft 托管的控制 1369 - 事件监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18cc35ed-a429-486d-8d59-cb47e87304ed) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1369.json) |
|[订阅应有一个联系人电子邮件地址，用于接收安全问题通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |当某个订阅中存在潜在的安全漏洞时，若要确保组织中的相关人员收到通知，请设置一个安全联系人，以接收来自安全中心的电子邮件通知。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="automated-tracking--data-collection--analysis"></a>自动跟踪/数据收集/分析

**ID**：NIST SP 800-53 Rev. 4 IR-5 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1370 - 事件监视 \| 自动跟踪/数据收集/分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F924e1b2d-c502-478f-bfdb-a7e09a0d5c01) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1370.json) |

### <a name="incident-reporting"></a>事件报告

**ID**：NIST SP 800-53 Rev. 4 IR-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1371 - 事件报告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9447f354-2c85-4700-93b3-ecdc6cb6a417) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1371.json) |
|[Microsoft 托管的控制 1372 - 事件报告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25b96717-c912-4c00-9143-4e487f411726) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1372.json) |

### <a name="automated-reporting"></a>自动化报告

**ID**：NIST SP 800-53 Rev. 4 IR-6 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1373 - 事件报告 \| 自动化报告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4cca950f-c3b7-492a-8e8f-ea39663c14f9) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1373.json) |

### <a name="vulnerabilities-related-to-incidents"></a>与事件相关的漏洞

**ID**：NIST SP 800-53 Rev. 4 IR-6 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用高严重性警报的电子邮件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |当某个订阅中存在潜在的安全漏洞时，若要确保组织中的相关人员收到通知，请在安全中心为高严重性警报启用电子邮件通知。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[应启用向订阅所有者发送高严重性警报的电子邮件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |当订阅中存在潜在的安全漏洞时，若要确保订阅所有者收到通知，请在安全中心设置向订阅所有者发送高严重性警报的电子邮件通知。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[订阅应有一个联系人电子邮件地址，用于接收安全问题通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |当某个订阅中存在潜在的安全漏洞时，若要确保组织中的相关人员收到通知，请设置一个安全联系人，以接收来自安全中心的电子邮件通知。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="incident-response-assistance"></a>事件响应协助

**ID**：NIST SP 800-53 Rev. 4 IR-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1374 - 事件响应协助](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc5c8616-52ef-4e5e-8000-491634ed9249) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1374.json) |

### <a name="automation-support-for-availability-of-information--support"></a>对信息/支持可用性的自动化支持

**ID**：NIST SP 800-53 Rev. 4 IR-7 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1375 - 事件响应协助 \| 对信息/支持可用性的自动化支持](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F00379355-8932-4b52-b63a-3bc6daf3451a) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1375.json) |

### <a name="coordination-with-external-providers"></a>与外部提供商协调

**ID**：NIST SP 800-53 Rev. 4 IR-7 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1376 - 事件响应协助 \| 与外部提供商协调](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F493a95f3-f2e3-47d0-af02-65e6d6decc2f) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1376.json) |
|[Microsoft 托管的控制 1377 - 事件响应协助 \| 与外部提供商协调](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68434bd1-e14b-4031-9edb-a4adf5f84a67) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1377.json) |

### <a name="incident-response-plan"></a>事件响应计划

**ID**：NIST SP 800-53 Rev. 4 IR-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1378 - 事件响应计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97fceb70-6983-42d0-9331-18ad8253184d) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1378.json) |
|[Microsoft 托管的控制 1379 - 事件响应计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9442dd2c-a07f-46cd-b55a-553b66ba47ca) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1379.json) |
|[Microsoft 托管的控制 1380 - 事件响应计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4319b7e-ea8d-42ff-8a67-ccd462972827) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1380.json) |
|[Microsoft 托管的控制 1381 - 事件响应计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe5368258-9684-4567-8126-269f34e65eab) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1381.json) |
|[Microsoft 托管的控制 1382 - 事件响应计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F841392b3-40da-4473-b328-4cde49db67b3) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1382.json) |
|[Microsoft 托管的控制 1383 - 事件响应计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4558451-e16a-4d2d-a066-fe12a6282bb9) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1383.json) |

### <a name="information-spillage-response"></a>信息泄漏响应

**ID**：NIST SP 800-53 Rev. 4 IR-9

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1384 - 信息泄漏响应](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79fbc228-461c-4a45-9004-a865ca0728a7) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1384.json) |
|[Microsoft 托管的控制 1385 - 信息泄漏响应](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e495e65-8663-49ca-9b38-9f45e800bc58) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1385.json) |
|[Microsoft 托管的控制 1386 - 信息泄漏响应](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5120193e-91fd-4f9d-bc6d-194f94734065) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1386.json) |
|[Microsoft 托管的控制 1387 - 信息泄漏响应](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3007185-3857-43a9-8237-06ca94f1084c) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1387.json) |
|[Microsoft 托管的控制 1388 - 信息泄漏响应](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c7c575a-d4c5-4f6f-bd49-dee97a8cba55) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1388.json) |
|[Microsoft 托管的控制 1389 - 信息泄漏响应](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39e6fda-ae70-4891-a739-be7bba6d1062) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1389.json) |

### <a name="responsible-personnel"></a>负责人员

**ID**：NIST SP 800-53 Rev. 4 IR-9 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1390 - 信息泄漏响应 \| 负责人员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3b65b63-09ec-4cb5-8028-7dd324d10eb0) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1390.json) |

### <a name="training"></a>培训

**ID**：NIST SP 800-53 Rev. 4 IR-9 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1391 - 信息泄漏响应 \| 培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd6ac1a1-660e-4810-baa8-74e868e2ed47) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1391.json) |

### <a name="post-spill-operations"></a>泄漏后的操作

**ID**：NIST SP 800-53 Rev. 4 IR-9 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1392 - 信息泄漏响应 \| 泄漏后操作](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86dc819f-15e1-43f9-a271-41ae58d4cecc) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1392.json) |

### <a name="exposure-to-unauthorized-personnel"></a>信息遭他人未经授权的访问

**ID**：NIST SP 800-53 Rev. 4 IR-9 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1393 - 信息泄漏响应 \| 信息遭他人未经授权地访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F731856d8-1598-4b75-92de-7d46235747c0) |由 Microsoft 实现此事件响应控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1393.json) |

## <a name="maintenance"></a>维护

### <a name="system-maintenance-policy-and-procedures"></a>系统维护策略和流程

**ID**：NIST SP 800-53 Rev. 4 MA-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1394 - 系统维护政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4db56f68-3f50-45ab-88f3-ca46f5379a94) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1394.json) |
|[Microsoft 托管的控制 1395 - 系统维护策略和流程](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7207a023-a517-41c5-9df2-09d4c6845a05) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1395.json) |

### <a name="controlled-maintenance"></a>受控性维护

**ID**：NIST SP 800-53 Rev. 4 MA-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1396 - 受控性维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F276af98f-4ff9-4e69-99fb-c9b2452fb85f) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1396.json) |
|[Microsoft 托管的控制 1397 - 受控性维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391af4ab-1117-46b9-b2c7-78bbd5cd995b) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1397.json) |
|[Microsoft 托管的控制 1398 - 受控性维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F443e8f3d-b51a-45d8-95a7-18b0e42f4dc4) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1398.json) |
|[Microsoft 托管的控制 1399 - 受控性维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2256e638-eb23-480f-9e15-6cf1af0a76b3) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1399.json) |
|[Microsoft 托管的控制 1400 - 受控性维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96d5098-a604-4cdf-90b1-ef6449a27424) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1400.json) |
|[Microsoft 托管的控制 1401 - 受控性维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb78ee928-e3c1-4569-ad97-9f8c4b629847) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1401.json) |

### <a name="automated-maintenance-activities"></a>自动化维护活动

**ID**：NIST SP 800-53 Rev. 4 MA-2 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1402 - 受控性维护 \| 自动维护活动](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a560d32-8075-4fec-9615-9f7c853f4ea9) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1402.json) |
|[Microsoft 托管的控制 1403 - 受控性维护 \| 自动维护活动](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F57149289-d52b-4f40-9fe6-5233c1ef80f7) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1403.json) |

### <a name="maintenance-tools"></a>维护工具

**ID**：NIST SP 800-53 Rev. 4 MA-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1404 - 维护工具](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d8f903-0cd6-449f-a172-50f6579c182b) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1404.json) |

### <a name="inspect-tools"></a>检查工具

**ID**：NIST SP 800-53 Rev. 4 MA-3 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1405 - 维护工具 \| 检查工具](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1a0bf3-409a-4b00-b60d-0b1f917f7e7b) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1405.json) |

### <a name="inspect-media"></a>检查媒体

**ID**：NIST SP 800-53 Rev. 4 MA-3 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1406 - 维护工具 \| 检查媒体](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0f5339c-9292-43aa-a0bc-d27c6b8e30aa) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1406.json) |

### <a name="prevent-unauthorized-removal"></a>防止未经授权的删除

**ID**：NIST SP 800-53 Rev. 4 MA-3 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1407 - 维护工具 \| 防止未经授权的移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fff9fbd83-1d8d-4b41-aac2-94cb44b33976) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1407.json) |
|[Microsoft 托管的控制 1408 - 维护工具 \| 防止未经授权的移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5f56ac6-4bb2-4086-bc41-ad76344ba2c2) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1408.json) |
|[Microsoft 托管的控制 1409 - 维护工具 \| 防止未经授权的移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1880188-e51a-4772-b2ab-68f5e8bd27f6) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1409.json) |
|[Microsoft 托管的控制 1410 - 维护工具 \| 防止未经授权的移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2596a9f-e59f-420d-9625-6e0b536348be) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1410.json) |

### <a name="nonlocal-maintenance"></a>非本地维护

**ID**：NIST SP 800-53 Rev. 4 MA-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1411 - 非本地维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F898d4fe8-f743-4333-86b7-0c9245d93e7d) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1411.json) |
|[Microsoft 托管的控制 1412 - 非本地维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3492d949-0dbb-4589-88b3-7b59601cc764) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1412.json) |
|[Microsoft 托管的控制 1413 - 非本地维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeedddb6-6bc0-42d5-809b-80048033419d) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1413.json) |
|[Microsoft 托管的控制 1414 - 非本地维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce63a52-e47b-4ae2-adbb-6e40d967f9e6) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1414.json) |
|[Microsoft 托管的控制 1415 - 非本地维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61a1dd98-b259-4840-abd5-fbba7ee0da83) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1415.json) |

### <a name="document-nonlocal-maintenance"></a>记录非本地维护

**ID**：NIST SP 800-53 Rev. 4 MA-4 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1416 - 非本地维护 \| 记录非本地维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38dfd8a3-5290-4099-88b7-4081f4c4d8ae) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1416.json) |

### <a name="comparable-security--sanitization"></a>等效安全性/清理

**ID**：NIST SP 800-53 Rev. 4 MA-4 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1417 - 非本地维护 \| 等效安全性/清理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7522ed84-70d5-4181-afc0-21e50b1b6d0e) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1417.json) |
|[Microsoft 托管的控制 1418 - 非本地维护 \| 等效安全性/清理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e633fd-284e-4ea7-88b4-02ca157ed713) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1418.json) |

### <a name="cryptographic-protection"></a>加密保护

**ID**：NIST SP 800-53 Rev. 4 MA-4 (6)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1419 - 非本地维护 \| 加密保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6747bf9-2b97-45b8-b162-3c8becb9937d) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1419.json) |

### <a name="maintenance-personnel"></a>维护人员

**ID**：NIST SP 800-53 Rev. 4 MA-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1420 - 维护人员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05ae08cc-a282-413b-90c7-21a2c60b8404) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1420.json) |
|[Microsoft 托管的控制 1421 - 维护人员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe539caaa-da8c-41b8-9e1e-449851e2f7a6) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1421.json) |
|[Microsoft 托管的控制 1422 - 维护人员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea556850-838d-4a37-8ce5-9d7642f95e11) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1422.json) |

### <a name="individuals-without-appropriate-access"></a>不具备相应访问权限的人员

**ID**：NIST SP 800-53 Rev. 4 MA-5 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1423 - 维护人员 \| 不具备相应访问权限的人员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7741669e-d4f6-485a-83cb-e70ce7cbbc20) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1423.json) |
|[Microsoft 托管的控制 1424 - 维护人员 \| 不具备相应访问权限的人员](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf55fc87-48e1-4676-a2f8-d9a8cf993283) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1424.json) |

### <a name="timely-maintenance"></a>及时维护

**ID**：NIST SP 800-53 Rev. 4 MA-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1425 - 及时维护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5983d99c-f39b-4c32-a3dc-170f19f6941b) |由 Microsoft 实现此维护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1425.json) |

## <a name="media-protection"></a>媒体保护

### <a name="media-protection-policy-and-procedures"></a>媒体保护策略和过程

**ID**：NIST SP 800-53 Rev. 4 MP-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1426 - 媒体保护政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21f639bc-f42b-46b1-8f40-7a2a389c291a) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1426.json) |
|[Microsoft 托管的控制 1427-媒体保护政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc90e44f-d83f-4bdf-900f-3d5eb4111b31) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1427.json) |

### <a name="media-access"></a>媒体访问

**ID**：NIST SP 800-53 Rev. 4 MP-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1428 - 媒体访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a77fcc7-b8d8-451a-ab52-56197913c0c7) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1428.json) |

### <a name="media-marking"></a>媒体标记

**ID**：NIST SP 800-53 Rev. 4 MP-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1429 - 媒体标记](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb07c9b24-729e-4e85-95fc-f224d2d08a80) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1429.json) |
|[Microsoft 托管的控制 1430 - 媒体标记](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f559588-5e53-4b14-a7c4-85d28ebc2234) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1430.json) |

### <a name="media-storage"></a>媒体存储

**ID**：NIST SP 800-53 Rev. 4 MP-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1431 - 媒体存储](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7173c52-2b99-4696-a576-63dd5f970ef4) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1431.json) |
|[Microsoft 托管的控制 1432 - 媒体存储](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1140e542-b80d-4048-af45-3f7245be274b) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1432.json) |

### <a name="media-transport"></a>媒体传输

**ID**：NIST SP 800-53 Rev. 4 MP-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1433 - 媒体传输](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b879b41-2728-41c5-ad24-9ee2c37cbe65) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1433.json) |
|[Microsoft 托管的控制 1434 - 媒体传输](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c18f06b-a68d-41c3-8863-b8cd3acb5f8f) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1434.json) |
|[Microsoft 托管的控制 1435 - 媒体传输](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa8d221b-d130-4637-ba16-501e666628bb) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1435.json) |
|[Microsoft 托管的控制 1436 - 媒体传输](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28aab8b4-74fd-4b7c-9080-5a7be525d574) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1436.json) |

### <a name="cryptographic-protection"></a>加密保护

**ID**：NIST SP 800-53 Rev. 4 MP-5 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1437 - 媒体传输 \| 加密保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d1eb6ed-bf13-4046-b993-b9e2aef0f76c) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1437.json) |

### <a name="media-sanitization"></a>媒体清理

**ID**：NIST SP 800-53 Rev. 4 MP-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1438 - 媒体清理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40fcc635-52a2-4dbc-9523-80a1f4aa1de6) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1438.json) |
|[Microsoft 托管的控制 1439 - 媒体清理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdce72873-c5f1-47c3-9b4f-6b8207fd5a45) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1439.json) |

### <a name="review--approve--track--document--verify"></a>评审/审核/跟踪/记录/验证

**ID**：NIST SP 800-53 Rev. 4 MP-6 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1440 - 媒体清理 \| 评审/审核/跟踪/记录/验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F881299bf-2a5b-4686-a1b2-321d33679953) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1440.json) |

### <a name="equipment-testing"></a>设备测试

**ID**：NIST SP 800-53 Rev. 4 MP-6 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1441 - 媒体清理 \| 设备测试](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6519d7f3-e8a2-4ff3-a935-9a9497152ad7) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1441.json) |

### <a name="nondestructive-techniques"></a>非销毁性技术

**ID**：NIST SP 800-53 Rev. 4 MP-6 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1442 - 媒体清理 \| 非销毁性技术](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f26049b-2c5a-4841-9ff3-d48a26aae475) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1442.json) |

### <a name="media-use"></a>媒体使用

**ID**：NIST SP 800-53 Rev. 4 MP-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1443 - 媒体使用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd0ec6fa-a2e7-4361-aee4-a8688659a9ed) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1443.json) |

### <a name="prohibit-use-without-owner"></a>禁止使用没有所有者的媒体资产

**ID**：NIST SP 800-53 Rev. 4 MP-7 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1444 - 媒体使用 \| 禁止使用没有所有者的资产](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F666143df-f5e0-45bd-b554-135f0f93e44e) |由 Microsoft 实现此媒体保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1444.json) |

## <a name="physical-and-environmental-protection"></a>物理保护和环境保护

### <a name="physical-and-environmental-protection-policy-and-procedures"></a>物理保护和环境保护政策与过程

**ID**：NIST SP 800-53 Rev. 4 PE-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1445 - 物理保护和环境保护政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32d07d59-2716-4972-b37b-214a67ac4a37) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1445.json) |
|[Microsoft 托管的控制 1446 - 物理保护和环境保护政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf6850fe-abba-468e-9ef4-d09ec7d983cd) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1446.json) |

### <a name="physical-access-authorizations"></a>物理访问授权

**ID**：NIST SP 800-53 Rev. 4 PE-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1447 - 物理访问授权](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9783a99-98fe-4a95-873f-29613309fe9a) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1447.json) |
|[Microsoft 托管的控制 1448 - 物理访问授权](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F825d6494-e583-42f2-a3f2-6458e6f0004f) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1448.json) |
|[Microsoft 托管的控制 1449 - 物理访问授权](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff784d3b0-5f2b-49b7-b9f3-00ba8653ced5) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1449.json) |
|[Microsoft 托管的控制 1450 - 物理访问授权](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F134d7a13-ba3e-41e2-b236-91bfcfa24e01) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1450.json) |

### <a name="physical-access-control"></a>物理访问控制

**ID**：NIST SP 800-53 Rev. 4 PE-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1451 - 物理访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3f1e5a3-25c1-4476-8cb6-3955031f8e65) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1451.json) |
|[Microsoft 托管的控制 1452 - 物理访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82c76455-4d3f-4e09-a654-22e592107e74) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1452.json) |
|[Microsoft 托管的控制 1453 - 物理访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9693b564-3008-42bc-9d5d-9c7fe198c011) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1453.json) |
|[Microsoft 托管的控制 1454 - 物理访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad58985d-ab32-4f99-8bd3-b7e134c90229) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1454.json) |
|[Microsoft 托管的控制 1455 - 物理访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068a88d4-e520-434e-baf0-9005a8164e6a) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1455.json) |
|[Microsoft 托管的控制 1456 - 物理访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F733ba9e3-9e7c-440a-a7aa-6196a90a2870) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1456.json) |
|[Microsoft 托管的控制 1457 - 物理访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2d9d3e6-8886-4305-865d-639163e5c305) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1457.json) |

### <a name="information-system-access"></a>信息系统访问

**ID**：NIST SP 800-53 Rev. 4 PE-3 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1458 - 物理访问控制 \| 信息系统访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c19ceb7-56e9-4488-8ddb-b1eb3aa6d203) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1458.json) |

### <a name="access-control-for-transmission-medium"></a>传输媒体的访问控制

**ID**：NIST SP 800-53 Rev. 4 PE-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1459 - 传输媒体的访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75cc73c7-5cdb-479d-a06f-7b4d0dbb1da0) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1459.json) |

### <a name="access-control-for-output-devices"></a>输出设备的访问控制

**ID**：NIST SP 800-53 Rev. 4 PE-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1460 - 输出设备的访问控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f3ce1bb-4f77-4695-8355-70b08d54fdda) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1460.json) |

### <a name="monitoring-physical-access"></a>监视物理访问

**ID**：NIST SP 800-53 Rev. 4 PE-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1461 - 监视物理访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faafef03e-fea8-470b-88fa-54bd1fcd7064) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1461.json) |
|[Microsoft 托管的控制 1462 - 监视物理访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b1f3a9a-13a1-4b40-8420-36bca6fd8c02) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1462.json) |
|[Microsoft 托管的控制 1463 - 监视物理访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F59721f87-ae25-4db0-a2a4-77cc5b25d495) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1463.json) |

### <a name="intrusion-alarms--surveillance-equipment"></a>入侵警报/监视设备

**ID**：NIST SP 800-53 Rev. 4 PE-6 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1464 - 监视物理访问 \| 入侵警报/监视设备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41256567-1795-4684-b00b-a1308ce43cac) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1464.json) |

### <a name="monitoring-physical-access-to-information-systems"></a>监视对信息系统的物理访问

**ID**：NIST SP 800-53 Rev. 4 PE-6 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1465 - 监视物理访问 \| 监视对信息系统的物理访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6e41554-86b5-4537-9f7f-4fc41a1d1640) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1465.json) |

### <a name="visitor-access-records"></a>访客访问记录

**ID**：NIST SP 800-53 Rev. 4 PE-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1466 - 访客访问记录](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d943a9c-a6f1-401f-a792-740cdb09c451) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1466.json) |
|[Microsoft 托管的控制 1467 - 访客访问记录](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5350cbf9-8bdd-4904-b22a-e88be84ca49d) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1467.json) |

### <a name="automated-records-maintenance--review"></a>自动记录维护/评审

**ID**：NIST SP 800-53 Rev. 4 PE-8 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1468 - 访客访问记录 \| 自动记录维护/评审](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75603f96-80a1-4757-991d-5a1221765ddd) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1468.json) |

### <a name="power-equipment-and-cabling"></a>电源设备和布线

**ID**：NIST SP 800-53 Rev. 4 PE-9

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1469 - 电源设备和布线](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff509c5b6-0de0-4a4e-9b2e-cd9cbf3a58fd) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1469.json) |

### <a name="emergency-shutoff"></a>紧急关闭

**ID**：NIST SP 800-53 Rev. 4 PE-10

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1470 - 紧急关闭](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc89ba09f-2e0f-44d0-8095-65b05bd151ef) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1470.json) |
|[Microsoft 托管的控制 1471 - 紧急关闭](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7dd0e9ce-1772-41fb-a50a-99977071f916) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1471.json) |
|[Microsoft 托管的控制 1472 - 紧急关闭](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef869332-921d-4c28-9402-3be73e6e50c8) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1472.json) |

### <a name="emergency-power"></a>应急电源

**ID**：NIST SP 800-53 Rev. 4 PE-11

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1473 - 应急电源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd7047705-d719-46a7-8bb0-76ad233eba71) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1473.json) |

### <a name="long-term-alternate-power-supply---minimal-operational-capability"></a>长期备用电源 - 最低运作能力

**ID**：NIST SP 800-53 Rev. 4 PE-11 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1474 - 应急电源 \| 长期备用电源 - 最低运作能力](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ad326e-d7a1-44b1-9a76-e17492efc9e4) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1474.json) |

### <a name="emergency-lighting"></a>应急照明

**ID**：NIST SP 800-53 Rev. 4 PE-12

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1475 - 应急照明](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a63848-30cf-4081-937e-ce1a1c885501) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1475.json) |

### <a name="fire-protection"></a>消防

**ID**：NIST SP 800-53 Rev. 4 PE-13

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1476 - 消防](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f3c4ac2-3e35-4906-a80b-473b12a622d7) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1476.json) |

### <a name="detection-devices--systems"></a>检测设备/系统

**ID**：NIST SP 800-53 Rev. 4 PE-13 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1477 - 消防 \| 检测设备/系统](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4862a63c-6c74-4a9d-a221-89af3c374503) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1477.json) |

### <a name="suppression-devices--systems"></a>灭火设备/系统

**ID**：NIST SP 800-53 Rev. 4 PE-13 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1478 - 消防 \| 灭火设备/系统](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff997df46-cfbb-4cc8-aac8-3fecdaf6a183) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1478.json) |

### <a name="automatic-fire-suppression"></a>自动灭火

**ID**：NIST SP 800-53 Rev. 4 PE-13 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1479 - 消防 \| 自动灭火](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe327b072-281d-4f75-9c28-4216e5d72f26) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1479.json) |

### <a name="temperature-and-humidity-controls"></a>温度和湿度控制

**ID**：NIST SP 800-53 Rev. 4 PE-14

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1480 - 温度和湿度控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18a767cc-1947-4338-a240-bc058c81164f) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1480.json) |
|[Microsoft 托管的控制 1481 - 温度和湿度控制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F717a1c78-a267-4f56-ac58-ee6c54dc4339) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1481.json) |

### <a name="monitoring-with-alarms--notifications"></a>使用警报/通知进行监视

**ID**：NIST SP 800-53 Rev. 4 PE-14 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1482 - 温度和湿度控制 \| 监视警报/通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9df4277e-8c88-4d5c-9b1a-541d53d15d7b) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1482.json) |

### <a name="water-damage-protection"></a>水害保护

**ID**：NIST SP 800-53 Rev. 4 PE-15

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1483 - 水害保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5cb81060-3c8a-4968-bcdc-395a1801f6c1) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1483.json) |

### <a name="automation-support"></a>自动化支持

**ID**：NIST SP 800-53 Rev. 4 PE-15 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1484 - 水害保护 \| 自动化支持](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F486b006a-3653-45e8-b41c-a052d3e05456) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1484.json) |

### <a name="delivery-and-removal"></a>传递和删除

**ID**：NIST SP 800-53 Rev. 4 PE-16

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1485 - 传递和删除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50301354-95d0-4a11-8af5-8039ecf6d38b) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1485.json) |

### <a name="alternate-work-site"></a>备用工作站点

**ID**：NIST SP 800-53 Rev. 4 PE-17

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1486 - 备用工作站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb790345-a51f-43de-934e-98dbfaf9dca5) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1486.json) |
|[Microsoft 托管的控制 1487 - 备用工作站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c3371d-c30c-4f58-abd9-30b8a8199571) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1487.json) |
|[Microsoft 托管的控制 1488 - 备用工作站点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8ef30eb-a44f-47af-8524-ac19a36d41d2) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1488.json) |

### <a name="location-of-information-system-components"></a>信息系统组件的位置

**ID**：NIST SP 800-53 Rev. 4 PE-18

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1489 - 信息系统组件的位置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0a794f-1444-4c96-9534-e35fc8c39c91) |由 Microsoft 实现此物理保护和环境保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1489.json) |

## <a name="planning"></a>规划

### <a name="security-planning-policy-and-procedures"></a>安全计划政策和程序

**ID**：NIST SP 800-53 Rev. 4 PL-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1490 - 安全计划政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e61da80-0957-4892-b70c-609d5eaafb6b) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1490.json) |
|[Microsoft 托管的控制 1491 - 安全计划政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1571dd40-dafc-4ef4-8f55-16eba27efc7b) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1491.json) |

### <a name="system-security-plan"></a>系统安全计划

**ID**：NIST SP 800-53 Rev. 4 PL-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1492 - 系统安全计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ad5f307-e045-46f7-8214-5bdb7e973737) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1492.json) |
|[Microsoft 托管的控制 1493 - 系统安全计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22b469b3-fccf-42da-aa3b-a28e6fb113ce) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1493.json) |
|[Microsoft 托管的控制 1494 - 系统安全计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed09d84-3311-4853-8b67-2b55dfa33d09) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1494.json) |
|[Microsoft 托管的控制 1495 - 系统安全计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4978d0e-a596-48e7-9f8c-bbf52554ce8d) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1495.json) |
|[Microsoft 托管的控制 1496 - 系统安全计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ca96127-2f87-46ab-a4fc-0d2a786df1c8) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1496.json) |

### <a name="plan--coordinate-with-other-organizational-entities"></a>与其他组织实体规划/协调

**ID**：NIST SP 800-53 Rev. 4 PL-2 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1497 - 系统安全计划 \| 与其他组织实体规划/协调](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e3c5583-1729-4d36-8771-59c32f090a22) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1497.json) |

### <a name="rules-of-behavior"></a>行为规则

**ID**：NIST SP 800-53 Rev. 4 PL-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1498 - 行为规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F633988b9-cf2f-4323-8394-f0d2af9cd6e1) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1498.json) |
|[Microsoft 托管的控制 1499 - 行为规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe59671ab-9720-4ee2-9c60-170e8c82251e) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1499.json) |
|[Microsoft 托管的控制 1500 - 行为规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9dd5b241-03cb-47d3-a5cd-4b89f9c53c92) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1500.json) |
|[Microsoft 托管的控制 1501 - 行为规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88817b58-8472-4f6c-81fa-58ce42b67f51) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1501.json) |

### <a name="social-media-and-networking-restrictions"></a>社交媒体和网络限制

**ID**：NIST SP 800-53 Rev. 4 PL-4 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1502 - 行为规则 \| 社交媒体和网络限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe901375c-8f01-4ac8-9183-d5312f47fe63) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1502.json) |

### <a name="information-security-architecture"></a>信息安全体系结构

**ID**：NIST SP 800-53 Rev. 4 PL-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1503 - 信息安全体系结构](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc1fa9c2f-d439-4ab9-8b83-81fb1934f81d) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1503.json) |
|[Microsoft 托管的控制 1504 - 信息安全体系结构](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e7c35d0-12d4-4e0c-80a2-8a352537aefd) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1504.json) |
|[Microsoft 托管的控制 1505 - 信息安全体系结构](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F813a10a7-3943-4fe3-8678-00dc52db5490) |由 Microsoft 实现此计划控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1505.json) |

## <a name="personnel-security"></a>人员安全

### <a name="personnel-security-policy-and-procedures"></a>人员安全政策和程序

**ID**：NIST SP 800-53 Rev. 4 PS-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1506 - 人员安全政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d2ff17-d604-4dd9-b607-9ecf63f28ad2) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1506.json) |
|[Microsoft 托管的控制 1507 - 人员安全政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ccd1bf-e7ad-4851-93ce-6ec817469c1e) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1507.json) |

### <a name="position-risk-designation"></a>岗位风险指示

**ID**：NIST SP 800-53 Rev. 4 PS-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1508 - 岗位风险指示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76f500cc-4bca-4583-bda1-6d084dc21086) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1508.json) |
|[Microsoft 托管的控制 1509 - 岗位风险指示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70792197-9bfc-4813-905a-bd33993e327f) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1509.json) |
|[Microsoft 托管的控制 1510 - 岗位风险指示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79da5b09-0e7e-499e-adda-141b069c7998) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1510.json) |

### <a name="personnel-screening"></a>人员筛选

**ID**：NIST SP 800-53 Rev. 4 PS-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1511 - 人员筛选](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9eae324-d327-4539-9293-b48e122465f8) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1511.json) |
|[Microsoft 托管的控制 1512 - 人员筛选](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5a8324ad-f599-429b-aaed-f9c6e8c987a8) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1512.json) |

### <a name="information-with-special-protection-measures"></a>具有特殊保护措施的信息

**ID**：NIST SP 800-53 Rev. 4 PS-3 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1513 - 人员筛选 \| 具有特殊保护措施的信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc416970d-b12b-49eb-8af4-fb144cd7c290) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1513.json) |
|[Microsoft 托管的控制 1514 - 人员筛选 \| 具有特殊保护措施的信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed5ca00-0e43-434e-a018-7aab91461ba7) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1514.json) |

### <a name="personnel-termination"></a>人员解职

**ID**：NIST SP 800-53 Rev. 4 PS-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1515 - 人员解职](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02dd141a-a2b2-49a7-bcbd-ca31142f6211) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1515.json) |
|[Microsoft 托管的控制 1516 - 人员解职](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3cd269-156f-435b-b472-c3af34c032ed) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1516.json) |
|[Microsoft 托管的控制 1517 - 人员解职](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8f5ad423-50d6-4617-b058-69908f5586c9) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1517.json) |
|[Microsoft 托管的控制 1518 - 人员解职](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d58f734-c052-40e9-8b2f-a1c2bff0b815) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1518.json) |
|[Microsoft 托管的控制 1519 - 人员解职](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f13915a-324c-4ab8-b45c-2eefeeefb098) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1519.json) |
|[Microsoft 托管的控制 1520 - 人员解职](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f2c513b-eb16-463b-b469-c10e5fa94f0a) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1520.json) |

### <a name="automated-notification"></a>自动化通知

**ID**：NIST SP 800-53 Rev. 4 PS-4 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1521 - 人员解职 \| 自动通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cbddf9c-a3aa-4330-a0f5-4c0c1f1862e5) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1521.json) |

### <a name="personnel-transfer"></a>人事调动

**ID**：NIST SP 800-53 Rev. 4 PS-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1522 - 人事调动](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38b470cc-f939-4a15-80e0-9f0c74f2e2c9) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1522.json) |
|[Microsoft 托管的控制 1523 - 人事调动](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5577a310-2551-49c8-803b-36e0d5e55601) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1523.json) |
|[Microsoft 托管的控制 1524 - 人事调动](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72f1cb4e-2439-4fe8-88ea-b8671ce3c268) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1524.json) |
|[Microsoft 托管的控制 1525 - 人事调动](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9be2f688-7a61-45e3-8230-e1ec93893f66) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1525.json) |

### <a name="access-agreements"></a>访问协议

**ID**：NIST SP 800-53 Rev. 4 PS-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1526 - 访问协议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F953e6261-a05a-44fd-8246-000e1a3edbb9) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1526.json) |
|[Microsoft 托管的控制 1527 - 访问协议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2823de66-332f-4bfd-94a3-3eb036cd3b67) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1527.json) |
|[Microsoft 托管的控制 1528 - 访问协议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdeb9797c-22f8-40e8-b342-a84003c924e6) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1528.json) |

### <a name="third-party-personnel-security"></a>第三方人员的安全

**ID**：NIST SP 800-53 Rev. 4 PS-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1529 - 第三方人员的安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd74fdc92-1cb8-4a34-9978-8556425cd14c) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1529.json) |
|[Microsoft 托管的控制 1530 - 第三方人员的安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e8f9566-29f1-49cd-b61f-f8628a3cf993) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1530.json) |
|[Microsoft 托管的控制 1531 - 第三方人员的安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0643e0c-eee5-4113-8684-c608d05c5236) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1531.json) |
|[Microsoft 托管的控制 1532 - 第三方人员的安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2c66299-9017-4d95-8040-8bdbf7901d52) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1532.json) |
|[Microsoft 托管的控制 1533 - 第三方人员的安全](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbba2a036-fb3b-4261-b1be-a13dfb5fbcaa) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1533.json) |

### <a name="personnel-sanctions"></a>人事处分

**ID**：NIST SP 800-53 Rev. 4 PS-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1534 - 人事处分](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b2b263e-cd05-4488-bcbf-4debec7a17d9) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1534.json) |
|[Microsoft 托管的控制 1535 - 人事处分](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9a165d2-967d-4733-8399-1074270dae2e) |由 Microsoft 实现此人员安全控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1535.json) |

## <a name="risk-assessment"></a>风险评估

### <a name="risk-assessment-policy-and-procedures"></a>风险评估政策和程序

**ID**：NIST SP 800-53 Rev. 4 RA-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1536 - 风险评估政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e40d9de-2ad4-4cb5-8945-23143326a502) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1536.json) |
|[Microsoft 托管的控制 1537 - 风险评估政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb19454ca-0d70-42c0-acf5-ea1c1e5726d1) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1537.json) |

### <a name="security-categorization"></a>安全分类

**ID**：NIST SP 800-53 Rev. 4 RA-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1538 - 安全分类](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d7658b2-e827-49c3-a2ae-6d2bd0b45874) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1538.json) |
|[Microsoft 托管的控制 1539 - 安全分类](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faabb155f-e7a5-4896-a767-e918bfae2ee0) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1539.json) |
|[Microsoft 托管的控制 1540 - 安全分类](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff771f8cb-6642-45cc-9a15-8a41cd5c6977) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1540.json) |

### <a name="risk-assessment"></a>风险评估

**ID**：NIST SP 800-53 Rev. 4 RA-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1541 - 风险评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70f6af82-7be6-44aa-9b15-8b9231b2e434) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1541.json) |
|[Microsoft 托管的控制 1542 - 风险评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feab340d0-3d55-4826-a0e5-feebfeb0131d) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1542.json) |
|[Microsoft 托管的控制 1543 - 风险评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd00b778-b5b5-49c0-a994-734ea7bd3624) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1543.json) |
|[Microsoft 托管的控制 1544 - 风险评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43ced7c9-cd53-456b-b0da-2522649a4271) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1544.json) |
|[Microsoft 托管的控制 1545 - 风险评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f4b171a-a56b-4328-8112-32cf7f947ee1) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1545.json) |

### <a name="vulnerability-scanning"></a>漏洞扫描

**ID**：NIST SP 800-53 Rev. 4 RA-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在虚拟机上启用漏洞评估解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |审核虚拟机以检测其是否正在运行受支持的漏洞评估解决方案。 每个网络风险和安全计划的核心部分都是识别和分析漏洞。 Azure 安全中心的标准定价层包括对虚拟机进行漏洞扫描，无需额外付费。 此外，安全中心可以自动为你部署此工具。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[Microsoft 托管的控制 1546 - 漏洞扫描](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce1ea7e-4038-4e53-82f4-63e8859333c1) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1546.json) |
|[Microsoft 托管的控制 1547 - 漏洞扫描](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58abf9b8-c6d4-4b4b-bfb9-fe98fe295f52) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1547.json) |
|[Microsoft 托管的控制 1548 - 漏洞扫描](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3afe6c78-6124-4d95-b85c-eb8c0c9539cb) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1548.json) |
|[Microsoft 托管的控制 1549 - 漏洞扫描](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd6976a08-d969-4df2-bb38-29556c2eb48a) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1549.json) |
|[Microsoft 托管的控制 1550 - 漏洞扫描](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F902908fb-25a8-4225-a3a5-5603c80066c9) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1550.json) |
|[SQL 数据库应已解决漏洞结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |监视漏洞评估扫描结果，并提供有关如何消除数据库漏洞的建议。 |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[计算机上的 SQL 服务器应已解决漏洞发现](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ba6d016-e7c3-4842-b8f2-4992ebc0d72d) |SQL 漏洞评估会扫描数据库中的安全漏洞，并显示与最佳做法之间的任何偏差，例如配置错误、权限过多和敏感数据未受保护。 解决发现的漏洞可以极大地改善数据库安全态势。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerSQLVulnerabilityAssessment_Audit.json) |
|[应修正 Azure 容器注册表映像中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |容器映像漏洞评估功能会扫描注册表中每个推送的容器映像上的安全漏洞，并显示每个映像的详细发现结果（由 Qualys 支持）。 修复这些漏洞可以极大改善容器的安全状况，并保护其不受攻击影响。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
|[应修正容器安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在安装了 Docker 的计算机上审核安全配置中的漏洞，并在 Azure 安全中心显示为建议。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[应修复计算机上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |建议通过 Azure 安全中心监视不满足配置的基线的服务器 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[应修复虚拟机规模集上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[应在 SQL 托管实例上启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |审核未启用定期漏洞评估扫描的每个 SQL 托管实例。 漏洞评估可发现、跟踪和帮助你修正潜在数据库漏洞。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[应对 SQL 服务器启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |审核未启用定期漏洞评估扫描的 Azure SQL 服务器。 漏洞评估可发现、跟踪和帮助你修正潜在数据库漏洞。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[应对 Synapse 工作区启用漏洞评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0049a6b3-a662-4f3e-8635-39cf44ace45a) |通过在 Synapse 工作区上配置定期 SQL 漏洞评估扫描来发现、跟踪和修复潜在的漏洞。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/ASC_SQLVulnerabilityAssessmentOnSynapse_Audit.json) |

### <a name="update-tool-capability"></a>更新工具功能

**ID**：NIST SP 800-53 Rev. 4 RA-5 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1551 - 漏洞扫描 \| 更新工具功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bbda922-0172-4095-89e6-5b4a0bf03af7) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1551.json) |

### <a name="update-by-frequency--prior-to-new-scan--when-identified"></a>按频率/新扫描之前/识别到漏洞时更新

**ID**：NIST SP 800-53 Rev. 4 RA-5 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1552 - 漏洞扫描 \| 按频率更新/新扫描之前/识别时](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43684572-e4f1-4642-af35-6b933bc506da) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1552.json) |

### <a name="breadth--depth-of-coverage"></a>覆盖广度/深度

**ID**：NIST SP 800-53 Rev. 4 RA-5 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1553 - 漏洞扫描 \| 作用广度/深度](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e5225fe-cdfb-4fce-9aec-0fe20dd53b62) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1553.json) |

### <a name="discoverable-information"></a>可发现的信息

**ID**：NIST SP 800-53 Rev. 4 RA-5 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1554 - 漏洞扫描 \| 可检测到的信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10984b4e-c93e-48d7-bf20-9c03b04e9eca) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1554.json) |

### <a name="privileged-access"></a>特权访问

**ID**：NIST SP 800-53 Rev. 4 RA-5 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1555 - 漏洞扫描 \| 特权访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5afa8cab-1ed7-4e40-884c-64e0ac2059cc) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1555.json) |

### <a name="automated-trend-analyses"></a>自动趋势分析

**ID**：NIST SP 800-53 Rev. 4 RA-5 (6)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1556 - 漏洞扫描 \| 自动趋势分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391ff8b3-afed-405e-9f7d-ef2f8168d5da) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1556.json) |

### <a name="review-historic-audit-logs"></a>审查历史审核日志

**ID**：NIST SP 800-53 Rev. 4 RA-5 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1557 - 漏洞扫描 \| 查阅历史审核日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36fbe499-f2f2-41b6-880e-52d7ea1d94a5) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1557.json) |

### <a name="correlate-scanning-information"></a>关联扫描信息

**ID**：NIST SP 800-53 Rev. 4 RA-5 (10)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1558 - 漏洞扫描 \| 关联扫描信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65592b16-4367-42c5-a26e-d371be450e17) |由 Microsoft 实现此风险评估控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1558.json) |

## <a name="system-and-services-acquisition"></a>系统和服务获取

### <a name="system-and-services-acquisition-policy-and-procedures"></a>系统和服务采购政策与流程

**ID**：NIST SP 800-53 Rev. 4 SA-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1559 - 系统和服务采购政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45692294-f074-42bd-ac54-16f1a3c07554) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1559.json) |
|[Microsoft 托管的控制 1560 - 系统和服务采购政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe29e0915-5c2f-4d09-8806-048b749ad763) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1560.json) |

### <a name="allocation-of-resources"></a>资源分配

**ID**：NIST SP 800-53 Rev. 4 SA-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1561 - 资源分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40364c3f-c331-4e29-b1e3-2fbe998ba2f5) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1561.json) |
|[Microsoft 托管的控制 1562 - 资源分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4142013-7964-4163-a313-a900301c2cef) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1562.json) |
|[Microsoft 托管的控制 1563 - 资源分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9afe2edf-232c-4fdf-8e6a-e867a5c525fd) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1563.json) |

### <a name="system-development-life-cycle"></a>系统开发生命周期

**ID**：NIST SP 800-53 Rev. 4 SA-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1564 - 系统开发生命周期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F157f0ef9-143f-496d-b8f9-f8c8eeaad801) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1564.json) |
|[Microsoft 托管的控制 1565 - 系统开发生命周期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45ce2396-5c76-4654-9737-f8792ab3d26b) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1565.json) |
|[Microsoft 托管的控制 1566 - 系统开发生命周期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50ad3724-e2ac-4716-afcc-d8eabd97adb9) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1566.json) |
|[Microsoft 托管的控制 1567 - 系统开发生命周期](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe72edbf6-aa61-436d-a227-0f32b77194b3) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1567.json) |

### <a name="acquisition-process"></a>采购流程

**ID**：NIST SP 800-53 Rev. 4 SA-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1568 - 采购流程](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8eae8-9854-495a-ac82-d2cd3eac02a6) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1568.json) |
|[Microsoft 托管的控制 1569 - 采购流程](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad2f8e61-a564-4dfd-8eaa-816f5be8cb34) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1569.json) |
|[Microsoft 托管的控制 1570 - 采购流程](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7fcf38d-bb09-4600-be7d-825046eb162a) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1570.json) |
|[Microsoft 托管的控制 1571 - 采购流程](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb11c985b-f2cd-4bd7-85f4-b52426edf905) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1571.json) |
|[Microsoft 托管的控制 1572 - 采购流程](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04f5fb00-80bb-48a9-a75b-4cb4d4c97c36) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1572.json) |
|[Microsoft 托管的控制 1573 - 采购流程](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58c93053-7b98-4cf0-b99f-1beb985416c2) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1573.json) |
|[Microsoft 托管的控制 1574 - 采购流程](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f935dab-83d6-47b8-85ef-68b8584161b9) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1574.json) |

### <a name="functional-properties-of-security-controls"></a>安全控制的功能属性

**ID**：NIST SP 800-53 Rev. 4 SA-4 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1575 - 采购流程 \| 安全控制措施的功能属性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e1bb73-1b08-4dbe-9c62-8e2e92e7ec41) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1575.json) |

### <a name="design--implementation-information-for-security-controls"></a>安全控制的设计/实施信息

**ID**：NIST SP 800-53 Rev. 4 SA-4 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1576 - 采购流程 \| 安全控制措施的设计/实施信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f18c885-ade3-48c5-80b1-8f9216019c18) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1576.json) |

### <a name="continuous-monitoring-plan"></a>持续监控计划

**ID**：NIST SP 800-53 Rev. 4 SA-4 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1577 - 采购流程 \| 持续监控计划](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd922484a-8cfc-4a6b-95a4-77d6a685407f) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1577.json) |

### <a name="functions--ports--protocols--services-in-use"></a>使用的功能/端口/协议/服务

**ID**：NIST SP 800-53 Rev. 4 SA-4 (9)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1578 - 采购流程 \| 使用的功能/端口/协议/服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45b7b644-5f91-498e-9d89-7402532d3645) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1578.json) |

### <a name="use-of-approved-piv-products"></a>使用批准的 PIV 产品

**ID**：NIST SP 800-53 Rev. 4 SA-4 (10)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1579 - 采购流程 \| 使用批准的 Piv 产品](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e54c7ef-7457-430b-9a3e-ef8881d4a8e0) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1579.json) |

### <a name="information-system-documentation"></a>信息系统文档

**ID**：NIST SP 800-53 Rev. 4 SA-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1580 - 信息系统文档](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F854db8ac-6adf-42a0-bef3-b73f764f40b9) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1580.json) |
|[Microsoft 托管的控制 1581 - 信息系统文档](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F742b549b-7a25-465f-b83c-ea1ffb4f4e0e) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1581.json) |
|[Microsoft 托管的控制 1582 - 信息系统文档](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd9e2f38-259b-462c-bfad-0ad7ab4e65c5) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1582.json) |
|[Microsoft 托管的控制 1583 - 信息系统文档](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0882d488-8e80-4466-bc0f-0cd15b6cb66d) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1583.json) |
|[Microsoft 托管的控制 1584 - 信息系统文档](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5864522b-ff1d-4979-a9f8-58bee1fb174c) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1584.json) |

### <a name="security-engineering-principles"></a>安全工程原则

**ID**：NIST SP 800-53 Rev. 4 SA-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1585 - 安全工程原则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd57f8732-5cdc-4cda-8d27-ab148e1f3a55) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1585.json) |

### <a name="external-information-system-services"></a>外部信息系统服务

**ID**：NIST SP 800-53 Rev. 4 SA-9

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1586 - 外部信息系统服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e3b2fbd-8f37-4766-a64d-3f37703dcb51) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1586.json) |
|[Microsoft 托管的控制 1587 - 外部信息系统服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32820956-9c6d-4376-934c-05cd8525be7c) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1587.json) |
|[Microsoft 托管的控制 1588 - 外部信息系统服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68ebae26-e0e0-4ecb-8379-aabf633b51e9) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1588.json) |

### <a name="risk-assessments--organizational-approvals"></a>风险评估/组织审批

**ID**：NIST SP 800-53 Rev. 4 SA-9 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1589 - 外部信息系统服务 \| 风险评估/组织审批](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ec7f9b-9478-40ff-8cfd-6a0d510081a8) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1589.json) |
|[Microsoft 托管的控制 1590 - 外部信息系统服务 \| 风险评估/组织审批](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf296b8c-f391-4ea4-9198-be3c9d39dd1f) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1590.json) |

### <a name="identification-of-functions--ports--protocols--services"></a>识别功能/端口/协议/服务

**ID**：NIST SP 800-53 Rev. 4 SA-9 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1591 - 外部信息系统服务 \|功能/端口/协议/服务的指定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff751cdb7-fbee-406b-969b-815d367cb9b3) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1591.json) |

### <a name="consistent-interests-of-consumers-and-providers"></a>使用者和提供商的一致利益

**ID**：NIST SP 800-53 Rev. 4 SA-9 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1592 - 外部信息系统服务 \| 使用者和提供商的一致利益](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d01ba6c-289f-42fd-a408-494b355b6222) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1592.json) |

### <a name="processing-storage-and-service-location"></a>处理、存储和服务位置

**ID**：NIST SP 800-53 Rev. 4 SA-9 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1593 - 外部信息系统服务 \| 处理、存储和服务位置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cd0a426-b5f5-4fe0-9539-a6043cdbc6fa) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1593.json) |

### <a name="developer-configuration-management"></a>开发商配置管理

**ID**：NIST SP 800-53 Rev. 4 SA-10

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1594 - 开发商配置管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F042ba2a1-8bb8-45f4-b080-c78cf62b90e9) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1594.json) |
|[Microsoft 托管的控制 1595 - 开发商配置管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e0414e7-6ef5-4182-8076-aa82fbb53341) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1595.json) |
|[Microsoft 托管的控制 1596 - 开发商配置管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21e25e01-0ae0-41be-919e-04ce92b8e8b8) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1596.json) |
|[Microsoft 托管的控制 1597 - 开发商配置管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68b250ec-2e4f-4eee-898a-117a9fda7016) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1597.json) |
|[Microsoft 托管的控制 1598 - 开发商配置管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae7e1f5e-2d63-4b38-91ef-bce14151cce3) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1598.json) |

### <a name="software--firmware-integrity-verification"></a>软件/固件完整性验证

**ID**：NIST SP 800-53 Rev. 4 SA-10 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1599 - 开发商配置管理 \| 软件/固件完整性验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0004bbf0-5099-4179-869e-e9ffe5fb0945) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1599.json) |

### <a name="developer-security-testing-and-evaluation"></a>开发商安全测试和评估

**ID**：NIST SP 800-53 Rev. 4 SA-11

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1600 - 开发人商安全测试和评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc53f3123-d233-44a7-930b-f40d3bfeb7d6) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1600.json) |
|[Microsoft 托管的控制 1601 - 开发人商安全测试和评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ee79a0c-addf-4ce9-9b3c-d9576ed5e20e) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1601.json) |
|[Microsoft 托管的控制 1602 - 开发人商安全测试和评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fddae2e97-a449-499f-a1c8-aea4a7e52ec9) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1602.json) |
|[Microsoft 托管的控制 1603 - 开发人商安全测试和评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b909c26-162f-47ce-8e15-0c1f55632eac) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1603.json) |
|[Microsoft 托管的控制 1604 - 开发人商安全测试和评估](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44dbba23-0b61-478e-89c7-b3084667782f) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1604.json) |

### <a name="static-code-analysis"></a>静态代码分析

**ID**：NIST SP 800-53 Rev. 4 SA-11 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1605 - 开发商安全测试和评估 \| 静态代码分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0062eb8b-dc75-4718-8ea5-9bb4a9606655) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1605.json) |

### <a name="threat-and-vulnerability-analyses"></a>威胁和漏洞分析

**ID**：NIST SP 800-53 Rev. 4 SA-11 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1606 - 开发商安全测试和评估 \| 威胁和漏洞分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaa8a9a4-5bbe-4c72-98f6-a3a47ae2b1ca) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1606.json) |

### <a name="dynamic-code-analysis"></a>动态代码分析

**ID**：NIST SP 800-53 Rev. 4 SA-11 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1607 - 开发商安全测试和评估 \| 动态代码分析](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F976a74cf-b192-4d35-8cab-2068f272addb) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1607.json) |

### <a name="supply-chain-protection"></a>供应链保护

**ID**：NIST SP 800-53 Rev. 4 SA-12

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1608 - 供应链保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb73b7b3b-677c-4a2a-b949-ad4dc4acd89f) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1608.json) |

### <a name="development-process-standards-and-tools"></a>开发流程、标准和工具

**ID**：NIST SP 800-53 Rev. 4 SA-15

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1609 - 开发流程、标准和工具](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e93fa71-42ac-41a7-b177-efbfdc53c69f) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1609.json) |
|[Microsoft 托管的控制 1610 - 开发流程、标准和工具](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9f3fb54-4222-46a1-a308-4874061f8491) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1610.json) |

### <a name="developer-provided-training"></a>开发商提供的培训

**ID**：NIST SP 800-53 Rev. 4 SA-16

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1611 - 开发商提供的培训](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdda8a0c-ac32-43f6-b2f4-7dc1df03f43f) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1611.json) |

### <a name="developer-security-architecture-and-design"></a>开发商安全体系结构和设计

**ID**：NIST SP 800-53 Rev. 4 SA-17

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1612 - 开发商安全体系结构和设计](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2037b3d-8b04-4171-8610-e6d4f1d08db5) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1612.json) |
|[Microsoft 托管的控制 1613 - 开发商安全体系结构和设计](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe2ad78b-8748-4bff-a924-f74dfca93f30) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1613.json) |
|[Microsoft 托管的控制 1614 - 开发商安全体系结构和设计](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8154e3b3-cc52-40be-9407-7756581d71f6) |由 Microsoft 实现此系统和服务获取控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1614.json) |

## <a name="system-and-communications-protection"></a>系统和通信保护

### <a name="system-and-communications-protection-policy-and-procedures"></a>系统和通信保护策略及过程

**ID**：NIST SP 800-53 Rev. 4 SC-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1615 - 系统和通信保护政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff35e02aa-0a55-49f8-8811-8abfa7e6f2c0) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1615.json) |
|[Microsoft 托管的控制 1616 - 系统和通信保护政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2006457a-48b3-4f7b-8d2e-1532287f9929) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1616.json) |

### <a name="application-partitioning"></a>应用分区

**ID**：NIST SP 800-53 Rev. 4 SC-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1617 - 应用程序分区](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa631d8f5-eb81-4f9d-9ee1-74431371e4a3) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1617.json) |

### <a name="security-function-isolation"></a>安全功能隔离

**ID**：NIST SP 800-53 Rev. 4 SC-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应在虚拟机规模集上安装终结点保护解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |审核终结点保护解决方案在虚拟机规模集上的存在性和运行状况 ，以保护其免受威胁和漏洞的侵害。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft 托管的控制 1618 - 安全功能隔离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff52f89aa-4489-4ec4-950e-8c96a036baa9) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1618.json) |
|[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |建议通过 Azure 安全中心监视未安装 Endpoint Protection 代理的服务器 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[应在计算机上启用 Windows Defender 攻击防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender 攻击防护使用 Azure Policy 来宾配置代理。 攻击防护服务具有 4 个组件，旨在锁定设备来阻隔各种攻击途径，并阻止恶意软件攻击中常用的行为，同时让企业能够平衡其安全风险和生产力要求（仅限 Windows）。 |AuditIfNotExists、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="information-in-shared-resources"></a>共享资源中的信息

**ID**：NIST SP 800-53 Rev. 4 SC-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1619 - 共享资源中的信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc722e569-cb52-45f3-a643-836547d016e1) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1619.json) |

### <a name="denial-of-service-protection"></a>拒绝服务保护

**ID**：NIST SP 800-53 Rev. 4 SC-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用 Azure DDoS 防护标准](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |应为属于应用程序网关且具有公共 IP 子网的所有虚拟网络启用 DDoS 保护标准。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[应禁用虚拟机上的 IP 转发](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |在虚拟机的 NIC 上启用 IP 转发可让该计算机接收发往其他目标的流量。 极少需要启用 IP 转发（例如，将 VM 用作网络虚拟设备时），因此，此策略应由网络安全团队评审。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Microsoft 托管的控制 1620 - 拒绝服务保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd17c826b-1dec-43e1-a984-7b71c446649c) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1620.json) |
|[应为应用程序网关启用 Web 应用程序防火墙 (WAF)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |将 Azure Web 应用程序防火墙 (WAF) 部署在面向公众的 Web 应用程序的前面，以便对传入流量进行额外检查。 Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受常见攻击和漏洞的侵害，例如 SQL 注入、跨站脚本以及本地和远程文件执行。 还可以通过自定义规则，按国家/地区、IP 地址范围和其他 http(s) 参数限制对 Web 应用程序的访问。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[应为 Azure Front Door 服务启用 Web 应用程序防火墙 (WAF)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |将 Azure Web 应用程序防火墙 (WAF) 部署在面向公众的 Web 应用程序的前面，以便对传入流量进行额外检查。 Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受常见攻击和漏洞的侵害，例如 SQL 注入、跨站脚本以及本地和远程文件执行。 还可以通过自定义规则，按国家/地区、IP 地址范围和其他 http(s) 参数限制对 Web 应用程序的访问。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |

### <a name="resource-availability"></a>资源可用性

**ID**：NIST SP 800-53 Rev. 4 SC-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1621 - 资源可用性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cb9f731-744a-4691-a481-ca77b0411538) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1621.json) |

### <a name="boundary-protection"></a>边界保护

**ID**：NIST SP 800-53 Rev. 4 SC-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在面向 Internet 的虚拟机上应用自适应网络强化建议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 安全中心会分析面向虚拟机的 Internet 的流量模式，并提供可减小潜在攻击面的网络安全组规则建议 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[所有 Internet 流量都应通过所部署的 Azure 防火墙进行路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure 安全中心已确认，你的某些子网未使用下一代防火墙进行保护。 通过使用 Azure 防火墙或受支持的下一代防火墙限制对子网的访问，保护子网免受潜在威胁的危害 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[应限制在与虚拟机关联的网络安全组上使用所有网络端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure 安全中心已识别到网络安全组的某些入站规则过于宽松。 入站规则不应允许从“任何”或“Internet”范围进行访问。 这有可能使得攻击者能够将你的资源定为攻击目标。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[API 管理服务应使用虚拟网络](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure 虚拟网络部署提供了增强的安全性和隔离，并允许你将 API 管理服务放置在不可经 Internet 路由的网络（你控制对其的访问权限）中。 然后，可以使用各种 VPN 技术将这些网络连接到本地网络，这样就能够访问网络中的和/或本地的后端服务。 可以将开发人员门户和 API 网关配置为可以从 Internet 访问或只能在虚拟网络内访问。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[应用程序配置应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到应用配置实例（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[应在 Kubernetes 服务上定义经授权的 IP 范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |通过仅向特定范围内的 IP 地址授予 API 访问权限，来限制对 Kubernetes 服务管理 API 的访问。 建议将访问权限限制给已获授权的 IP 范围，以确保只有受允许网络中的应用程序可以访问群集。 |Audit、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Azure API for FHIR 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR 应至少具有一个获得批准的专用终结点连接。 虚拟网络中的客户端可以安全地访问通过专用链接获得专用终结点连接的资源。 有关详细信息，请访问：[https://aka.ms/fhir-privatelink](../../../healthcare-apis/fhir/configure-private-link.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |通过专用终结点，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 通过将专用终结点映射到 Azure Cache for Redis 实例，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure 认知搜索服务应使用支持专用链接的 SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |使用 Azure 认知搜索的受支持的 SKU，可以通过 Azure 专用链接在源位置或目标位置没有公共 IP 地址的情况下将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到搜索服务，可以降低数据泄露风险。 有关详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure 认知搜索服务应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |禁用公用网络访问可确保 Azure 认知搜索服务不会在公共 Internet 上公开，从而提高安全性。 创建专用终结点可以限制搜索服务的公开。 有关详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure 认知搜索服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure 认知搜索可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Cosmos DB 帐户应有防火墙规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |应在 Azure Cosmos DB 帐户上定义防火墙规则，以防止来自未经授权的源的流量。 至少定义了一个 IP 规则且启用了虚拟网络筛选器的帐户才会被视为合规。 禁用公共访问的帐户也被视为合规。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure 数据工厂应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 数据工厂，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure 事件网格域应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格域（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure 事件网格主题应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格主题（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 文件同步应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |为指定的存储同步服务资源创建专用终结点，可以从组织网络的专用 IP 地址空间中寻址存储同步服务资源，而不是通过可通过 Internet 访问的公共终结点。 单独创建专用终结点并不会禁用公共终结点。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure 密钥保管库应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |禁用对密钥保管库的公用网络访问，确保无法通过公共 Internet 对其进行访问。 这样可以减少数据泄露风险。 有关详细信息，请访问：[https://aka.ms/akvprivatelink](../../../key-vault/general/private-link-service.md)。 |Audit、Deny、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|[ Azure 机器学习工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 机器学习工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到服务总线命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure SignalR 服务资源而不是整个服务，可降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Synapse 工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure Synapse 工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure Web PubSub 服务可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[认知服务帐户应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |禁用公用网络访问可确保认知服务帐户不会在公共 Internet 上公开，从而提高安全性。 创建专用终结点可以限制认知服务帐户的公开。 有关详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)。  |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[认知服务帐户应限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |应限制对认知服务帐户的网络访问。 配置网络规则，使只有来自允许的网络的应用程序才能访问认知服务帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络或到公共 Internet IP 地址范围的流量授予访问权限。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[认知服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到认知服务将减少数据泄露的可能性。 有关专用链接的详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[容器注册表不得允许无限制的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |默认情况下，Azure 容器注册表接受来自任何网络上的主机的 Internet 连接。 为了防止注册表受到潜在的威胁，只允许来自特定的公共 IP 地址或地址范围的访问。 如果注册表没有 IP/防火墙规则或配置的虚拟网络，它将出现在不正常资源中。 有关容器注册表网络规则的详细信息，请访问 [https://aka.ms/acr/portal/public-network](../../../container-registry/container-registry-access-selected-networks.md) 和 [https://aka.ms/acr/vnet](../../../container-registry/container-registry-vnet.md)。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_AuditDeny.json) |
|[容器注册表应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。通过将专用终结点映射到容器注册表，而不是整个服务，还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB 帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 CosmosDB 帐户，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[磁盘访问资源应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 diskAccesses，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[事件中心命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件中心命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[面向 Internet 的虚拟机应使用网络安全组进行保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范 VM 遭受潜在威胁。 如需详细了解如何使用 NSG 控制流量，请访问 [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IoT 中心设备预配服务实例应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 IoT 中心设备预配服务可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[应禁用虚拟机上的 IP 转发](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |在虚拟机的 NIC 上启用 IP 转发可让该计算机接收发往其他目标的流量。 极少需要启用 IP 转发（例如，将 VM 用作网络虚拟设备时），因此，此策略应由网络安全团队评审。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[应通过即时网络访问控制来保护虚拟机的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |建议通过 Azure 安全中心监视可能的网络适时 (JIT) 访问 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[应关闭虚拟机上的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |打开远程管理端口会使 VM 暴露在较高级别的 Internet 攻击风险之下。 此类攻击试图暴力破解凭据，来获取对计算机的管理员访问权限。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[Microsoft 托管的控制 1622 - 边界保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fecf56554-164d-499a-8d00-206b07c27bed) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1622.json) |
|[Microsoft 托管的控制 1623 - 边界保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02ce1b22-412a-4528-8630-c42146f917ed) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1623.json) |
|[Microsoft 托管的控制 1624 - 边界保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37d079e3-d6aa-4263-a069-dd7ac6dd9684) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1624.json) |
|[应使用网络安全组来保护非面向 Internet 的虚拟机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb91dfba-c30d-4263-9add-9c2384e659a6) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范非面向 Internet 的 VM 遭受潜在威胁。 如需详细了解如何使用 NSG 控制流量，请访问 [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternalVirtualMachines_Audit.json) |
|[应启用 Azure SQL 数据库上的专用终结点连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |专用终结点连接通过启用到 Azure SQL 数据库的专用连接来加强安全通信。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[应为 Key Vault 配置专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |专用链接提供了一种将 Key Vault 连接到 Azure 资源，而无需通过公共 Internet 发送流量的方法。 专用链接提供深度防御，可防范数据外泄。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[应为 MariaDB 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |专用终结点连接通过启用到 Azure Database for MariaDB 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[应为 MySQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |专用终结点连接通过启用到 Azure Database for MySQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[应为 PostgreSQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |专用终结点连接通过启用到 Azure Database for PostgreSQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[应禁用 Azure SQL 数据库上的公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b8ca024-1d5c-4dec-8995-b1a932b41780) |禁用公用网络访问属性可确保只能从专用终结点访问 Azure SQL 数据库，从而提高安全性。 此配置拒绝所有符合基于 IP 或虚拟网络的防火墙规则的登录。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PublicNetworkAccess_Audit.json) |
|[应为 MariaDB 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |禁用公用网络访问属性以提高安全性，并确保只能从专用终结点访问 Azure Database for MariaDB。 此配置严格禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
|[应为 MySQL 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |禁用公用网络访问属性以提高安全性，并确保只能从专用终结点访问 Azure Database for MySQL。 此配置严格禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
|[应为 PostgreSQL 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb52376f7-9612-48a1-81cd-1ffe4b61032c) |禁用公用网络访问属性以提高安全性，并确保只能从专用终结点访问 Azure Database for PostgreSQL。 此配置禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_DisablePublicNetworkAccess_Audit.json) |
|[应禁止存储帐户公共访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |对 Azure 存储中的容器和 blob 进行匿名公共读取访问虽然是共享数据的一种简便方法，但可能会带来安全风险。 为了防止不希望的匿名访问导致数据泄露，Microsoft 建议禁止对存储帐户的公共访问，除非你的方案需要。 |审核、拒绝、已禁用 |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[应限制对存储帐户的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |应限制对存储帐户的网络访问。 配置网络规则，以便只允许来自允许的网络的应用程序访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络的流量或公共 Internet IP 地址范围授予访问权限 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[存储帐户应使用虚拟网络规则来限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |使用虚拟网络规则作为首选方法（而不使用基于 IP 的筛选），保护存储帐户免受潜在威胁危害。 禁用基于 IP 的筛选可以阻止公共 IP 访问你的存储帐户。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |
|[存储帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到存储帐户可以降低数据泄露风险。 有关专用链接的详细信息，请访问 [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[子网应与网络安全组关联](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范子网遭受潜在威胁。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝流向子网的网络流量。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[VM 映像生成器模板应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 VM 映像生成器生成资源可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)。 |审核、已禁用、拒绝 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
|[应为应用程序网关启用 Web 应用程序防火墙 (WAF)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |将 Azure Web 应用程序防火墙 (WAF) 部署在面向公众的 Web 应用程序的前面，以便对传入流量进行额外检查。 Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受常见攻击和漏洞的侵害，例如 SQL 注入、跨站脚本以及本地和远程文件执行。 还可以通过自定义规则，按国家/地区、IP 地址范围和其他 http(s) 参数限制对 Web 应用程序的访问。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[应为 Azure Front Door 服务启用 Web 应用程序防火墙 (WAF)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |将 Azure Web 应用程序防火墙 (WAF) 部署在面向公众的 Web 应用程序的前面，以便对传入流量进行额外检查。 Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受常见攻击和漏洞的侵害，例如 SQL 注入、跨站脚本以及本地和远程文件执行。 还可以通过自定义规则，按国家/地区、IP 地址范围和其他 http(s) 参数限制对 Web 应用程序的访问。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |

### <a name="access-points"></a>接入点

**ID**：NIST SP 800-53 Rev. 4 SC-7 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在面向 Internet 的虚拟机上应用自适应网络强化建议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 安全中心会分析面向虚拟机的 Internet 的流量模式，并提供可减小潜在攻击面的网络安全组规则建议 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[所有 Internet 流量都应通过所部署的 Azure 防火墙进行路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure 安全中心已确认，你的某些子网未使用下一代防火墙进行保护。 通过使用 Azure 防火墙或受支持的下一代防火墙限制对子网的访问，保护子网免受潜在威胁的危害 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[应限制在与虚拟机关联的网络安全组上使用所有网络端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure 安全中心已识别到网络安全组的某些入站规则过于宽松。 入站规则不应允许从“任何”或“Internet”范围进行访问。 这有可能使得攻击者能够将你的资源定为攻击目标。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[API 管理服务应使用虚拟网络](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure 虚拟网络部署提供了增强的安全性和隔离，并允许你将 API 管理服务放置在不可经 Internet 路由的网络（你控制对其的访问权限）中。 然后，可以使用各种 VPN 技术将这些网络连接到本地网络，这样就能够访问网络中的和/或本地的后端服务。 可以将开发人员门户和 API 网关配置为可以从 Internet 访问或只能在虚拟网络内访问。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[应用程序配置应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到应用配置实例（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[应在 Kubernetes 服务上定义经授权的 IP 范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |通过仅向特定范围内的 IP 地址授予 API 访问权限，来限制对 Kubernetes 服务管理 API 的访问。 建议将访问权限限制给已获授权的 IP 范围，以确保只有受允许网络中的应用程序可以访问群集。 |Audit、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Azure API for FHIR 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR 应至少具有一个获得批准的专用终结点连接。 虚拟网络中的客户端可以安全地访问通过专用链接获得专用终结点连接的资源。 有关详细信息，请访问：[https://aka.ms/fhir-privatelink](../../../healthcare-apis/fhir/configure-private-link.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis 应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |通过专用终结点，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 通过将专用终结点映射到 Azure Cache for Redis 实例，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure 认知搜索服务应使用支持专用链接的 SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |使用 Azure 认知搜索的受支持的 SKU，可以通过 Azure 专用链接在源位置或目标位置没有公共 IP 地址的情况下将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到搜索服务，可以降低数据泄露风险。 有关详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure 认知搜索服务应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |禁用公用网络访问可确保 Azure 认知搜索服务不会在公共 Internet 上公开，从而提高安全性。 创建专用终结点可以限制搜索服务的公开。 有关详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure 认知搜索服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure 认知搜索可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Cosmos DB 帐户应有防火墙规则](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |应在 Azure Cosmos DB 帐户上定义防火墙规则，以防止来自未经授权的源的流量。 至少定义了一个 IP 规则且启用了虚拟网络筛选器的帐户才会被视为合规。 禁用公共访问的帐户也被视为合规。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure 数据工厂应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 数据工厂，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure 事件网格域应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格域（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure 事件网格主题应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件网格主题（而不是整个服务），还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 文件同步应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |为指定的存储同步服务资源创建专用终结点，可以从组织网络的专用 IP 地址空间中寻址存储同步服务资源，而不是通过可通过 Internet 访问的公共终结点。 单独创建专用终结点并不会禁用公共终结点。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure 密钥保管库应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |禁用对密钥保管库的公用网络访问，确保无法通过公共 Internet 对其进行访问。 这样可以减少数据泄露风险。 有关详细信息，请访问：[https://aka.ms/akvprivatelink](../../../key-vault/general/private-link-service.md)。 |Audit、Deny、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|[ Azure 机器学习工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure 机器学习工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure 服务总线命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到服务总线命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure SignalR 服务资源而不是整个服务，可降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Synapse 工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 Azure Synapse 工作区，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 Azure Web PubSub 服务可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[认知服务帐户应禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |禁用公用网络访问可确保认知服务帐户不会在公共 Internet 上公开，从而提高安全性。 创建专用终结点可以限制认知服务帐户的公开。 有关详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)。  |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[认知服务帐户应限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |应限制对认知服务帐户的网络访问。 配置网络规则，使只有来自允许的网络的应用程序才能访问认知服务帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络或到公共 Internet IP 地址范围的流量授予访问权限。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[认知服务应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到认知服务将减少数据泄露的可能性。 有关专用链接的详细信息，请访问：[https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[容器注册表不得允许无限制的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |默认情况下，Azure 容器注册表接受来自任何网络上的主机的 Internet 连接。 为了防止注册表受到潜在的威胁，只允许来自特定的公共 IP 地址或地址范围的访问。 如果注册表没有 IP/防火墙规则或配置的虚拟网络，它将出现在不正常资源中。 有关容器注册表网络规则的详细信息，请访问 [https://aka.ms/acr/portal/public-network](../../../container-registry/container-registry-access-selected-networks.md) 和 [https://aka.ms/acr/vnet](../../../container-registry/container-registry-vnet.md)。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_AuditDeny.json) |
|[容器注册表应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。通过将专用终结点映射到容器注册表，而不是整个服务，还可以防范数据泄露风险。 有关详细信息，请访问：[https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB 帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 CosmosDB 帐户，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[磁盘访问资源应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到 diskAccesses，可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[事件中心命名空间应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 通过将专用终结点映射到事件中心命名空间，可以降低数据泄露风险。 有关详细信息，请访问：[https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[面向 Internet 的虚拟机应使用网络安全组进行保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范 VM 遭受潜在威胁。 如需详细了解如何使用 NSG 控制流量，请访问 [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IoT 中心设备预配服务实例应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 IoT 中心设备预配服务可以降低数据泄露风险。 有关专用链接的详细信息，请访问：[https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[应禁用虚拟机上的 IP 转发](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |在虚拟机的 NIC 上启用 IP 转发可让该计算机接收发往其他目标的流量。 极少需要启用 IP 转发（例如，将 VM 用作网络虚拟设备时），因此，此策略应由网络安全团队评审。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[应通过即时网络访问控制来保护虚拟机的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |建议通过 Azure 安全中心监视可能的网络适时 (JIT) 访问 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[应关闭虚拟机上的管理端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |打开远程管理端口会使 VM 暴露在较高级别的 Internet 攻击风险之下。 此类攻击试图暴力破解凭据，来获取对计算机的管理员访问权限。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[Microsoft 托管的控制 1625 - 边界保护 \| 接入点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9b66a4d-70a1-4b47-8fa1-289cec68c605) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1625.json) |
|[应使用网络安全组来保护非面向 Internet 的虚拟机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb91dfba-c30d-4263-9add-9c2384e659a6) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范非面向 Internet 的 VM 遭受潜在威胁。 如需详细了解如何使用 NSG 控制流量，请访问 [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md) |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternalVirtualMachines_Audit.json) |
|[应启用 Azure SQL 数据库上的专用终结点连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |专用终结点连接通过启用到 Azure SQL 数据库的专用连接来加强安全通信。 |Audit、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[应为 Key Vault 配置专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |专用链接提供了一种将 Key Vault 连接到 Azure 资源，而无需通过公共 Internet 发送流量的方法。 专用链接提供深度防御，可防范数据外泄。 |Audit、Deny、Disabled |[1.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[应为 MariaDB 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |专用终结点连接通过启用到 Azure Database for MariaDB 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[应为 MySQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |专用终结点连接通过启用到 Azure Database for MySQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[应为 PostgreSQL 服务器启用专用终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |专用终结点连接通过启用到 Azure Database for PostgreSQL 的专用连接来加强安全通信。 配置专用终结点连接，以启用对仅来自已知网络的流量的访问，并防止访问所有其他 IP 地址，包括 Azure 内的地址。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[应禁用 Azure SQL 数据库上的公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b8ca024-1d5c-4dec-8995-b1a932b41780) |禁用公用网络访问属性可确保只能从专用终结点访问 Azure SQL 数据库，从而提高安全性。 此配置拒绝所有符合基于 IP 或虚拟网络的防火墙规则的登录。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PublicNetworkAccess_Audit.json) |
|[应为 MariaDB 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |禁用公用网络访问属性以提高安全性，并确保只能从专用终结点访问 Azure Database for MariaDB。 此配置严格禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
|[应为 MySQL 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |禁用公用网络访问属性以提高安全性，并确保只能从专用终结点访问 Azure Database for MySQL。 此配置严格禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
|[应为 PostgreSQL 服务器禁用公用网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb52376f7-9612-48a1-81cd-1ffe4b61032c) |禁用公用网络访问属性以提高安全性，并确保只能从专用终结点访问 Azure Database for PostgreSQL。 此配置禁止访问 Azure IP 范围之外的任何公共地址空间，并拒绝与 IP 或基于虚拟网络的防火墙规则匹配的所有登录。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_DisablePublicNetworkAccess_Audit.json) |
|[应禁止存储帐户公共访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |对 Azure 存储中的容器和 blob 进行匿名公共读取访问虽然是共享数据的一种简便方法，但可能会带来安全风险。 为了防止不希望的匿名访问导致数据泄露，Microsoft 建议禁止对存储帐户的公共访问，除非你的方案需要。 |审核、拒绝、已禁用 |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[应限制对存储帐户的网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |应限制对存储帐户的网络访问。 配置网络规则，以便只允许来自允许的网络的应用程序访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络的流量或公共 Internet IP 地址范围授予访问权限 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[存储帐户应使用虚拟网络规则来限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |使用虚拟网络规则作为首选方法（而不使用基于 IP 的筛选），保护存储帐户免受潜在威胁危害。 禁用基于 IP 的筛选可以阻止公共 IP 访问你的存储帐户。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |
|[存储帐户应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到存储帐户可以降低数据泄露风险。 有关专用链接的详细信息，请访问 [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[子网应与网络安全组关联](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用网络安全组 (NSG) 限制对 VM 的访问，以此防范子网遭受潜在威胁。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝流向子网的网络流量。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[VM 映像生成器模板应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |通过 Azure 专用链接，在没有源位置或目标位置的公共 IP 地址的情况下，也可以将虚拟网络连接到 Azure 服务。 专用链接平台处理使用者与服务之间通过 Azure 主干网络进行的连接。 将专用终结点映射到 VM 映像生成器生成资源可以降低数据泄露的风险。 有关专用链接的详细信息，请访问：[https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)。 |审核、已禁用、拒绝 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
|[应为应用程序网关启用 Web 应用程序防火墙 (WAF)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |将 Azure Web 应用程序防火墙 (WAF) 部署在面向公众的 Web 应用程序的前面，以便对传入流量进行额外检查。 Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受常见攻击和漏洞的侵害，例如 SQL 注入、跨站脚本以及本地和远程文件执行。 还可以通过自定义规则，按国家/地区、IP 地址范围和其他 http(s) 参数限制对 Web 应用程序的访问。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[应为 Azure Front Door 服务启用 Web 应用程序防火墙 (WAF)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |将 Azure Web 应用程序防火墙 (WAF) 部署在面向公众的 Web 应用程序的前面，以便对传入流量进行额外检查。 Web 应用程序防火墙 (WAF) 为 Web 应用程序提供集中保护，使其免受常见攻击和漏洞的侵害，例如 SQL 注入、跨站脚本以及本地和远程文件执行。 还可以通过自定义规则，按国家/地区、IP 地址范围和其他 http(s) 参数限制对 Web 应用程序的访问。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |

### <a name="external-telecommunications-services"></a>外部电信服务

**ID**：NIST SP 800-53 Rev. 4 SC-7 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1626 - 边界保护 \| 外部电信服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8f6bddd-6d67-439a-88d4-c5fe39a79341) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1626.json) |
|[Microsoft 托管的控制 1627 - 边界保护 \| 外部电信服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd73310d-76fc-422d-bda4-3a077149f179) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1627.json) |
|[Microsoft 托管的控制 1628 - 边界保护 \| 外部电信服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67de62b4-a737-4781-8861-3baed3c35069) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1628.json) |
|[Microsoft 托管的控制 1629 - 边界保护 \| 外部电信服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc171b095-7756-41de-8644-a062a96043f2) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1629.json) |
|[Microsoft 托管的控制 1630 - 边界保护 \| 外部电信服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3643717a-3897-4bfd-8530-c7c96b26b2a0) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1630.json) |

### <a name="deny-by-default--allow-by-exception"></a>默认拒绝/例外允许

**ID**：NIST SP 800-53 Rev. 4 SC-7 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1631 - 边界保护 \| 默认拒绝/例外允许](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74ae9b8e-e7bb-4c9c-992f-c535282f7a2c) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1631.json) |

### <a name="prevent-split-tunneling-for-remote-devices"></a>阻止远程设备发生隧道分离

**ID**：NIST SP 800-53 Rev. 4 SC-7 (7)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1632 - 边界保护 \| 阻止远程设备发生隧道分离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ce9073a-77fa-48f0-96b1-87aa8e6091c2) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1632.json) |

### <a name="route-traffic-to-authenticated-proxy-servers"></a>将流量路由到经过验证的代理服务器

**ID**：NIST SP 800-53 Rev. 4 SC-7 (8)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1633 - 边界保护 \| 将流量路由到已验证代理服务器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F07557aa0-e02f-4460-9a81-8ecd2fed601a) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1633.json) |

### <a name="prevent-unauthorized-exfiltration"></a>防止未经授权的外泄

**ID**：NIST SP 800-53 Rev. 4 SC-7 (10)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1634 - 边界保护 \| 防止未经授权的渗漏](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F292a7c44-37fa-4c68-af7c-9d836955ded2) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1634.json) |

### <a name="host-based-protection"></a>基于主机的保护

**ID**：NIST SP 800-53 Rev. 4 SC-7 (12)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1635 - 边界保护 \| 基于主机的保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87551b5d-1deb-4d0f-86cc-9dc14cb4bf7e) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1635.json) |

### <a name="isolation-of-security-tools--mechanisms--support-components"></a>隔离安全工具/机制/支持组件

**ID**：NIST SP 800-53 Rev. 4 SC-7 (13)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1636 - 边界保护 \| 隔离安全工具/机制/支持组件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b694eed-7081-43c6-867c-41c76c961043) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1636.json) |

### <a name="fail-secure"></a>失效安全

**ID**：NIST SP 800-53 Rev. 4 SC-7 (18)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1637 - 边界保护 \| 以安全妥善的方式发生故障](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4075bedc-c62a-4635-bede-a01be89807f3) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1637.json) |

### <a name="dynamic-isolation--segregation"></a>动态隔离/分离

**ID**：NIST SP 800-53 Rev. 4 SC-7 (20)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1638 - 边界保护 \| 动态隔离/划分](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49b99653-32cd-405d-a135-e7d60a9aae1f) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1638.json) |

### <a name="isolation-of-information-system-components"></a>隔离信息系统组件

**ID**：NIST SP 800-53 Rev. 4 SC-7 (21)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1639 - 边界保护 \| 隔离信息系统组件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78e8e649-50f6-4fe3-99ac-fedc2e63b03f) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1639.json) |

### <a name="transmission-confidentiality-and-integrity"></a>传输保密性和完整性

**ID**：NIST SP 800-53 Rev. 4 SC-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只能通过 HTTPS 访问 API 应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Azure HDInsight 群集应使用传输中加密来加密 Azure HDInsight 群集节点之间的通信](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |在 Azure HDInsight 群集节点之间的传输过程中，数据可能会被篡改。 启用传输中加密可解决传输过程中的滥用和篡改问题。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
|[应为 MySQL 数据库服务器启用“强制 SSL 连接”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 Azure Database for MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 此配置强制始终启用 SSL 以访问数据库服务器。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 Azure Database for MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 此配置强制始终启用 SSL 以访问数据库服务器。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[应仅在 API 应用中需要 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |启用 FTPS 强制以实现增强的安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[应仅在函数应用中要求使用 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |启用 FTPS 强制以实现增强的安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[应仅在 Web 应用中要求使用 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |启用 FTPS 强制以实现增强的安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |
|[应该只能通过 HTTPS 访问函数应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Kubernetes 群集应只可通过 HTTPS 进行访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |使用 HTTPS 可确保执行身份验证，并保护传输中的数据不受网络层窃听攻击威胁。 此功能目前已面向 Kubernetes 服务 (AKS) 正式发布，并面向 AKS 引擎和启用了 Azure Arc 的 Kubernetes 提供预览版。 有关详细信息，请访问 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md) |审核、拒绝、已禁用 |[6.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly.json) |
|[应在 API 应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[应在函数应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[应在 Web 应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Microsoft 托管的控制 1640 - 传输保密性和完整性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a289ce-6a20-4b75-a0f3-dc8601b6acd0) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1640.json) |
|[只能与 Azure Cache for Redis 建立安全连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |审核是否仅启用通过 SSL 来与 Azure Redis 缓存建立连接。 使用安全连接可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[应启用安全传输到存储帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |审核存储帐户中安全传输的要求。 安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听和会话劫持等网络层攻击 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[只能通过 HTTPS 访问 Web 应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[应将 Windows Web 服务器配置为使用安全通信协议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |为了保护通过 Internet 进行通信的信息的隐私，Web 服务器应使用最新版本的行业标准加密协议，即传输层安全性 (TLS)。 TLS 使用安全证书对计算机之间的连接进行加密来保护网络上的通信。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="cryptographic-or-alternate-physical-protection"></a>加密或备用物理保护

**ID**：NIST SP 800-53 Rev. 4 SC-8 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只能通过 HTTPS 访问 API 应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Azure HDInsight 群集应使用传输中加密来加密 Azure HDInsight 群集节点之间的通信](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |在 Azure HDInsight 群集节点之间的传输过程中，数据可能会被篡改。 启用传输中加密可解决传输过程中的滥用和篡改问题。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
|[应为 MySQL 数据库服务器启用“强制 SSL 连接”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 Azure Database for MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 此配置强制始终启用 SSL 以访问数据库服务器。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 Azure Database for MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 此配置强制始终启用 SSL 以访问数据库服务器。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[应仅在 API 应用中需要 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |启用 FTPS 强制以实现增强的安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[应仅在函数应用中要求使用 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |启用 FTPS 强制以实现增强的安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[应仅在 Web 应用中要求使用 FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |启用 FTPS 强制以实现增强的安全性 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |
|[应该只能通过 HTTPS 访问函数应用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Kubernetes 群集应只可通过 HTTPS 进行访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |使用 HTTPS 可确保执行身份验证，并保护传输中的数据不受网络层窃听攻击威胁。 此功能目前已面向 Kubernetes 服务 (AKS) 正式发布，并面向 AKS 引擎和启用了 Azure Arc 的 Kubernetes 提供预览版。 有关详细信息，请访问 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md) |审核、拒绝、已禁用 |[6.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly.json) |
|[应在 API 应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[应在函数应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[应在 Web 应用中使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升级到最新的 TLS 版本 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Microsoft 托管的控制 1641 - 传输保密性和完整性 \| 加密或备用物理保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd39d4f68-7346-4133-8841-15318a714a24) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1641.json) |
|[只能与 Azure Cache for Redis 建立安全连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |审核是否仅启用通过 SSL 来与 Azure Redis 缓存建立连接。 使用安全连接可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[应启用安全传输到存储帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |审核存储帐户中安全传输的要求。 安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听和会话劫持等网络层攻击 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[只能通过 HTTPS 访问 Web 应用程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可确保执行服务器/服务身份验证服务，并保护传输中的数据不受网络层窃听攻击威胁。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[应将 Windows Web 服务器配置为使用安全通信协议](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |为了保护通过 Internet 进行通信的信息的隐私，Web 服务器应使用最新版本的行业标准加密协议，即传输层安全性 (TLS)。 TLS 使用安全证书对计算机之间的连接进行加密来保护网络上的通信。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="network-disconnect"></a>网络连接中断

**ID**：NIST SP 800-53 Rev. 4 SC-10

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1642 - 网络连接中断](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53397227-5ee3-4b23-9e5e-c8a767ce6928) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1642.json) |

### <a name="cryptographic-key-establishment-and-management"></a>加密密钥建立和管理

**ID**：NIST SP 800-53 Rev. 4 SC-12

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR 应使用客户管理的密钥对数据进行静态加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |根据相关的法规或合规性要求，请使用客户管理的密钥来控制对 Azure API for FHIR 中存储的数据的静态加密。 客户托管密钥还提供双重加密，方法是在使用服务托管密钥完成的默认加密层上添加另一层加密。 |audit、disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure 自动化帐户应使用客户管理的密钥来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |使用客户管理的密钥来管理 Azure 自动化帐户的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/automation-cmk](../../../automation/automation-secure-asset-encryption.md#:~:text=Secure assets in Azure Automation include credentials, certificates, connections,,Using Microsoft-managed keys)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
|[Azure Batch 帐户应使用客户管理的密钥来加密数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99e9ccd8-3db9-4592-b0d1-14b1715a4d8a) |使用客户管理的密钥来管理 Batch 帐户数据的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/Batch-CMK](https://aka.ms/Batch-CMK)。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_CustomerManagedKey_Audit.json) |
|[Azure 容器实例容器组应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |使用客户管理的密钥更灵活地保护容器。 指定客户托管密钥时，该密钥用于保护和控制对数据加密密钥的访问。 使用客户管理的密钥可提供附加功能来控制密钥加密密钥的轮换或以加密方式擦除数据。 |审核、已禁用、拒绝 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
|[Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |使用客户管理的密钥来管理 Azure Cosmos DB 的静态加密。 默认情况下，使用服务管理的密钥对数据进行静态加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/cosmosdb-cmk](../../../cosmos-db/how-to-setup-cmk.md)。 |审核、拒绝、已禁用 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |
|[Azure Data Box 作业应使用客户管理的密钥加密设备解锁密码](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |使用客户管理的密钥控制 Azure Data Box 的设备解锁密码的加密。 客户管理的密钥还有助于通过 Data Box 服务管理对设备解锁密码的访问，以便以自动方式准备设备和复制数据。 设备本身的数据已使用高级加密标准 256 位加密进行静态加密，并且设备解锁密码默认使用 Microsoft 托管密钥进行加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
|[Azure 数据资源管理器静态加密应使用客户管理的密钥](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |对 Azure 数据资源管理器群集使用客户管理的密钥启用静态加密，可提供针对静态加密所使用密钥的额外控制。 此功能通常适用于具有特殊合规性要求并且需要使用 Key Vault 管理密钥的客户。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[应使用客户管理的密钥对 Azure 数据工厂进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |使用客户管理的密钥来管理 Azure 数据工厂的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足法规合规性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/adf-cmk](../../../data-factory/enable-customer-managed-key.md)。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
|[Azure HDInsight 群集应使用客户管理的密钥来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |使用客户管理的密钥来管理 Azure HDInsight 群集的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/hdi.cmk](../../../hdinsight/disk-encryption.md)。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight 群集应使用主机加密来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |启用主机加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 启用主机加密时，存储在 VM 主机上的数据将静态加密，且已加密的数据将流向存储服务。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[应使用客户管理的密钥对 Azure 机器学习工作区进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fba769a63-b8cc-4b2d-abf6-ac33c7204be8) |使用客户管理的密钥管理 Azure 机器学习工作区数据的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/azureml-workspaces-cmk](../../../machine-learning/how-to-create-workspace-template.md)。 |Audit、Deny、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_CMKEnabled_Audit.json) |
|[应使用客户管理的密钥对 Azure Monitor 日志群集进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f68a601-6e6d-4e42-babf-3f643a047ea2) |创建 Azure Monitor 日志群集并使用客户管理的密钥进行加密。 默认情况下，使用服务管理的密钥对日志数据进行加密，但为了满足法规合规性，通常需要使用客户管理的密钥。 借助 Azure Monitor 中客户管理的密钥，可更好地控制对数据的访问，请参阅 [https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys](../../../azure-monitor/logs/customer-managed-keys.md)。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsClusters_CMKEnabled_Deny.json) |
|[Azure 恢复服务保管库应使用客户管理的密钥来加密备份数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e94d99a-8a36-4563-bc77-810d8893b671) |使用客户管理的密钥来管理备份数据的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/AB-CmkEncryption](../../../backup/encryption-at-rest-with-cmk.md)。 |Audit、Deny、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/AzBackupRSVault_CMKEnabled_Audit.json) |
|[Azure 流分析作业应使用客户管理的密钥来加密数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |当你想要将流分析作业的任何元数据和专用数据资产安全地存储在存储帐户中时，请使用客户管理的密钥。 这样就可以完全控制流分析数据的加密方式。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |
|[Azure Synapse 工作区应使用客户管理的密钥来加密静态数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |使用客户管理的密钥来控制对 Azure Synapse 工作区中存储的数据的静态加密。 客户管理的密钥提供双重加密，方法是在使用服务托管密钥完成的默认加密层上添加另一层加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[机器人服务应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure 机器人服务自动加密你的资源，以保护数据并实现组织安全性和合规性承诺。 默认使用 Microsoft 管理的加密密钥。 为了能够更灵活地管理密钥或控制对订阅的访问，请选择“客户管理的密钥”（亦称为“创建自己的密钥 (BYOK)”）。 详细了解 Azure 机器人服务加密：[https://docs.microsoft.com/azure/bot-service/bot-service-encryption](/azure/bot-service/bot-service-encryption)。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
|[Azure Kubernetes 服务群集中的操作系统和数据磁盘都应使用客户托管的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d7be79c-23ba-4033-84dd-45e2a5ccdd67) |使用客户管理的密钥加密 OS 和数据磁盘可以更好地控制密钥管理并提高密钥管理的灵活性。 这是许多法规和行业合规性标准中的常见要求。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_CMK_Deny.json) |
|[认知服务帐户应启用使用客户管理的密钥进行数据加密的功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67121cc7-ff39-4ab8-b7e3-95b84dab487d) |为了满足法规符合性标准，通常需要使用客户管理的密钥。 利用客户管理的密钥，可以使用由你创建并拥有的 Azure Key Vault 密钥对存储在认知服务中的数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 通过 [https://go.microsoft.com/fwlink/?linkid=2121321](../../../cognitive-services/encryption/cognitive-services-encryption-keys-portal.md) 详细了解客户管理的密钥。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_CustomerManagedKey_Audit.json) |
|[应使用客户管理的密钥对容器注册表进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |使用客户管理的密钥来管理注册表内容的静态加密。 默认情况下，使用服务管理的密钥对数据进行静态加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/acr/CMK](../../../container-registry/container-registry-customer-managed-keys.md)。 |Audit、Deny、Disabled |[1.1.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[事件中心命名空间应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1ad735a-e96f-45d2-a7b2-9a4932cab7ec) |Azure 事件中心支持通过 Microsoft 管理的密钥（默认）或客户管理的密钥来加密静态数据。 如果选择使用客户管理的密钥加密数据，则可分配、轮换、禁用和撤销对密钥的访问权限，事件中心将使用密钥加密命名空间中的数据。 请注意，事件中心仅支持使用客户管理的密钥对专用群集中的命名空间进行加密。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_CustomerManagedKeyEnabled_Audit.json) |
|[HPC 缓存帐户应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |使用客户管理的密钥来管理 Azure HPC 缓存的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 |审核、已禁用、拒绝 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
|[应使用客户管理的密钥 (CMK) 来加密 IoT 中心设备预配服务数据](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47031206-ce96-41f8-861b-6a915f3de284) |使用客户管理的密钥来管理 IoT 中心设备预配服务的静态加密。 将使用服务管理的密钥自动对数据进行静态加密，但为了满足监管合规标准，通常需要使用客户管理的密钥 (CMK)。 CMK 允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 访问 [https://aka.ms/dps/CMK](../../../iot-dps/iot-dps-customer-managed-keys.md)，了解有关 CMK 加密的详细信息。 |Audit、Deny、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_CMKEncryptionEnabled_AuditDeny.json) |
|[应使用客户管理的密钥对逻辑应用集成服务环境进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |使用客户管理的密钥部署到集成服务环境，以管理逻辑应用数据的静态加密。 默认情况下，使用服务管理的密钥对客户数据进行加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[应使用平台管理的密钥和客户管理的密钥双重加密托管磁盘](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca91455f-eace-4f96-be59-e6e2c35b4816) |对安全性高度敏感的客户如果担心出现与任何特定加密算法、实现或密钥泄露相关的风险，可以选择在使用平台管理的加密密钥的基础结构层上使用其他加密算法/模式的额外加密层。 需要提供磁盘加密集才能使用双重加密。 更多信息请访问 [https://aka.ms/disks-doubleEncryption](../../../virtual-machines/disk-encryption.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DoubleEncryptionRequired_Deny.json) |
|[Microsoft 托管的控制 1643 - 加密密钥建立和管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8d492c-dd7a-46f7-a723-fa66a425b87c) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1643.json) |
|[MySQL 服务器应使用客户管理的密钥进行静态数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |使用客户管理的密钥来管理 MySQL 服务器的静态加密。 默认情况下，使用服务管理的密钥对数据进行静态加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 |AuditIfNotExists、Disabled |[1.0.4](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[应使用客户管理的密钥来加密 OS 和数据磁盘](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F702dd420-7fcc-42c5-afe8-4026edd20fe0) |使用客户管理的密钥来管理托管磁盘内容的静态加密。 默认情况下，使用平台管理的密钥对数据进行静态加密，但为了满足合规性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 更多信息请访问 [https://aka.ms/disks-cmk](../../../virtual-machines/disk-encryption.md)。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/OSAndDataDiskCMKRequired_Deny.json) |
|[PostgreSQL 服务器应使用客户管理的密钥进行静态数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18adea5e-f416-4d0f-8aa8-d24321e3e274) |使用客户管理的密钥来管理 PostgreSQL 服务器的静态加密。 默认情况下，使用服务管理的密钥对数据进行静态加密，但为了满足法规符合性标准，通常需要使用客户管理的密钥。 客户管理的密钥允许使用由你创建并拥有的 Azure Key Vault 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 |AuditIfNotExists、Disabled |[1.0.4](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableByok_Audit.json) |
|[应将 Azure Monitor 中已保存的查询保存在客户存储帐户中以进行日志加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa298e57-9444-42ba-bf04-86e8470e32c7) |将存储帐户链接到 Log Analytics 工作区，以通过存储帐户加密保护保存的查询。 为了满足法规合规性并更好地控制对 Azure Monitor 中保存的查询的访问，通常需要使用客户管理的密钥。 有关上述内容的更多详细信息，请参阅 [https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys?tabs=portal#customer-managed-key-for-saved-queries](../../../azure-monitor/logs/customer-managed-keys.md?tabs=portal)。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsWorkspaces_CMKBYOSQueryEnabled_Deny.json) |
|[服务总线高级命名空间应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure 服务总线支持通过 Microsoft 管理的密钥（默认）或客户管理的密钥来加密静态数据。 如果选择使用客户管理的密钥加密数据，则可分配、轮换、禁用和撤销对服务总线将用于加密命名空间中的数据的密钥的访问权限。 请注意，服务总线仅支持使用客户管理的密钥对高级命名空间进行加密。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |
|[SQL 托管实例应使用客户管理的密钥进行静态数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |使用你自己的密钥实现透明数据加密 (TDE) 可增加透明度和对 TDE 保护器的控制，增强由 HSM 提供支持的外部服务的安全性，并促进职责划分。 此建议适用于具有相关合规性要求的组织。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL Server 应使用客户管理的密钥进行静态数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |使用你自己的密钥实现透明数据加密 (TDE) 可增加透明度和对 TDE 保护器的控制，增强由 HSM 提供支持的外部服务的安全性，并促进职责划分。 此建议适用于具有相关合规性要求的组织。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[存储帐户加密范围应使用客户管理的密钥对静态数据进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5ec538c-daa0-4006-8596-35468b9148e8) |使用客户管理的密钥来管理存储帐户加密范围的静态加密。 客户管理的密钥允许使用由你创建并拥有的 Azure 密钥保管库密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。 若要详细了解存储帐户加密范围，请访问 [https://aka.ms/encryption-scopes-overview](../../../storage/blobs/encryption-scope-overview.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_EncryptionScopesShouldUseCMK_Audit.json) |
|[存储帐户应使用客户管理的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |使用客户管理的密钥更灵活地保护存储帐户。 指定客户托管密钥时，该密钥用于保护和控制对数据加密密钥的访问。 使用客户管理的密钥可提供附加功能来控制密钥加密密钥的轮换或以加密方式擦除数据。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

### <a name="availability"></a>可用性

**ID**：NIST SP 800-53 Rev. 4 SC-12 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1644 - 加密密钥建立和管理 \| 可用性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7211477-c970-446b-b4af-062f37461147) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1644.json) |

### <a name="symmetric-keys"></a>对称密钥

**ID**：NIST SP 800-53 Rev. 4 SC-12 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1645 - 加密密钥建立和管理 \| 对称密钥](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafbd0baf-ff1a-4447-a86f-088a97347c0c) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1645.json) |

### <a name="asymmetric-keys"></a>非对称密钥

**ID**：NIST SP 800-53 Rev. 4 SC-12 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1646 - 加密密钥建立和管理 \| 非对称密钥](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F506814fa-b930-4b10-894e-a45b98c40e1a) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1646.json) |

### <a name="cryptographic-protection"></a>加密保护

**ID**：NIST SP 800-53 Rev. 4 SC-13

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1647 - 加密保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F791cfc15-6974-42a0-9f4c-2d4b82f4a78c) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1647.json) |

### <a name="collaborative-computing-devices"></a>协作计算设备

**ID**：NIST SP 800-53 Rev. 4 SC-15

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1648 - 协作计算设备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a9eb14b-495a-4ebb-933c-ce4ef5264e32) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1648.json) |
|[Microsoft 托管的控制 1649 - 协作计算设备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26d292cc-b0b8-4c29-9337-68abc758bf7b) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1649.json) |

### <a name="public-key-infrastructure-certificates"></a>公钥基础结构证书

**ID**：NIST SP 800-53 Rev. 4 SC-17

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1650 - 公钥基础结构证书](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F201d3740-bd16-4baf-b4b8-7cda352228b7) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1650.json) |

### <a name="mobile-code"></a>移动代码

**ID**：NIST SP 800-53 Rev. 4 SC-18

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1651 - 移动代码](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6db63528-c9ba-491c-8a80-83e1e6977a50) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1651.json) |
|[Microsoft 托管的控制 1652 - 移动代码](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6998e84a-2d29-4e10-8962-76754d4f772d) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1652.json) |
|[Microsoft 托管的控制 1653 - 移动代码](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1c00a7-7fd0-42b0-8c5b-c45f6fa1f71b) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1653.json) |

### <a name="voice-over-internet-protocol"></a>IP 语音 (VoIP)

**ID**：NIST SP 800-53 Rev. 4 SC-19

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1654 - IP 语音 (VoIP)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a2ee16e-ab1f-414a-800b-d1608835862b) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1654.json) |
|[Microsoft 托管的控制 1655 - IP 语音 (VoIP)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F121eab72-390e-4629-a7e2-6d6184f57c6b) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1655.json) |

### <a name="secure-name--address-resolution-service-authoritative-source"></a>安全名称/地址解析服务（权威源）

**ID**：NIST SP 800-53 Rev. 4 SC-20

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1656 - 安全名称/地址解析服务（权威源）](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cb067d5-c8b5-4113-a7ee-0a493633924b) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1656.json) |
|[Microsoft 托管的控制 1657 - 安全名称/地址解析服务（权威源）](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90f01329-a100-43c2-af31-098996135d2b) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1657.json) |

### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>安全名称/地址解析服务（递归或缓存解析程序）

**ID**：NIST SP 800-53 Rev. 4 SC-21

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1658 - 安全名称/地址解析服务（递归或缓存解析程序）](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063b540e-4bdc-4e7a-a569-3a42ddf22098) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1658.json) |

### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>名称体系结构和预配/地址解析服务

**ID**：NIST SP 800-53 Rev. 4 SC-22

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1659 - 名称体系结构和预配/地址解析服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35a4102f-a778-4a2e-98c2-971056288df8) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1659.json) |

### <a name="session-authenticity"></a>会话验证

**ID**：NIST SP 800-53 Rev. 4 SC-23

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1660 - 会话验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63096613-ce83-43e5-96f4-e588e8813554) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1660.json) |

### <a name="invalidate-session-identifiers-at-logout"></a>注销时让会话标识符失效

**ID**：NIST SP 800-53 Rev. 4 SC-23 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1661 - 会话验证 \| 注销时让会话标识符失效](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c643c9a-1be7-4016-a5e7-e4bada052920) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1661.json) |

### <a name="fail-in-known-state"></a>发生故障时处于已知状态

**ID**：NIST SP 800-53 Rev. 4 SC-24

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1662 - 发生故障时处于已知状态](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F165cb91f-7ea8-4ab7-beaf-8636b98c9d15) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1662.json) |

### <a name="protection-of-information-at-rest"></a>保护静态信息

**ID**：NIST SP 800-53 Rev. 4 SC-28

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应用服务环境应启用内部加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb74e86f-d351-4b8d-b034-93da7391c01f) |如果将 InternalEncryption 设置为 true，会对应用服务环境中前端和辅助角色之间的页面文件、辅助角色磁盘和内部网络流量进行加密。 若要了解详细信息，请查看 [https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-custom-settings#enable-internal-encryption](../../../app-service/environment/app-service-app-service-environment-custom-settings.md#enable-internal-encryption)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_HostingEnvironment_InternalEncryption_Audit.json) |
|[自动化帐户变量应加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |存储敏感数据时，请务必启用自动化帐户变量资产加密 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Azure Data Box 作业应为设备上静态数据启用双重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |为设备上的静态数据启用第二层基于软件的加密。 设备已通过用于静态数据的高级加密标准 256 位加密受到保护。 此选项将添加第二层数据加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[应创建启用了基础结构加密（双重加密）的 Azure Monitor 日志群集](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea0dfaed-95fb-448c-934e-d6e713ce393d) |若要确保使用两种不同的加密算法和两个不同的密钥在服务级别和基础结构级别启用安全数据加密，请使用 Azure Monitor 专用群集。 在区域受支持时，默认情况下会启用此选项，请参阅 [https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#customer-managed-key-overview](../../../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview)。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsClusters_CMKDoubleEncryptionEnabled_Deny.json) |
|[Azure Stack Edge 设备应使用双重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |若要在设备上保护静态数据，请确保静态数据已双重加密，已控制对数据的访问，并在设备停用后从数据磁盘安全地清除数据。 双重加密使用两层加密：数据卷上的 BitLocker XTS-AES 256 位加密和硬盘驱动器上的内置加密。 有关详细信息，请参阅特定 Stack Edge 设备的安全概述文档。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
|[应为 Azure 数据资源管理器启用磁盘加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |启用磁盘加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[应为 Azure 数据资源管理器启用双重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |启用双重加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 启用双重加密后，将使用两种不同的加密算法和两个不同的密钥对存储帐户中的数据进行两次加密，一次在服务级别，一次在基础结构级别。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[应为 Azure Database for MySQL 服务器启用基础结构加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a58212a-c829-4f13-9872-6371df2fd0b4) |为 Azure Database for MySQL 服务器启用基础结构加密，以便增强数据安全。 启用基础结构加密后，将使用符合 FIPS 140-2 的 Microsoft 托管密钥对静态数据进行两次加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_InfrastructureEncryption_Audit.json) |
|[应为 Azure Database for PostgreSQL 服务器启用基础结构加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24fba194-95d6-48c0-aea7-f65bf859c598) |为 Azure Database for PostgreSQL 服务器启用基础结构加密，以便增强数据安全。 启用基础结构加密后，将使用符合 FIPS 140-2 的 Microsoft 托管密钥对静态数据进行两次加密 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_InfrastructureEncryption_Audit.json) |
|[Microsoft 托管的控制 1663 - 保护静态信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60171210-6dde-40af-a144-bf2670518bfa) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1663.json) |
|[应对 SQL 数据库中的敏感数据进行分类](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure 安全中心监视 SQL 数据库的数据发现和分类扫描结果，并建议将数据库中的敏感数据分类以改善监视效果并提升安全性 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |
|[Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric 使用主要群集证书为节点之间的通信提供三个保护级别（None、Sign 和 EncryptAndSign）。 设置保护级别以确保所有节点到节点消息均已进行加密和数字签名 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[存储帐户应具有基础结构加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4733ea7b-a883-42fe-8cac-97454c2a9e4a) |启用基础结构加密，以便增强数据安全。 启用基础结构加密后，存储帐户中的数据将加密两次。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountInfrastructureEncryptionEnabled_Audit.json) |
|[应在主机处加密 Azure Kubernetes 服务群集中代理节点池的临时磁盘和缓存](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41425d9f-d1a5-499a-9932-f8ed8453932c) |为了增强数据安全性，应对存储在 Azure Kubernetes 服务节点 VM 的虚拟机 (VM) 主机上的数据进行静态加密。 这是许多法规和行业合规性标准中的常见要求。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_EncryptionAtHost_Deny.json) |
|[应在 SQL 数据库上启用透明数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |应启用透明数据加密以保护静态数据并满足符合性要求 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[虚拟机和虚拟机规模集应启用主机中加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc4d8e41-e223-45ea-9bf5-eada37891d87) |使用主机中加密可对虚拟机和虚拟机规模集数据进行端到端加密。 主机中加密可对临时磁盘和 OS/数据磁盘缓存实现静态加密。 启用主机中加密后，将使用平台管理的密钥对临时 OS 磁盘进行加密。 OS/数据磁盘缓存使用客户管理的密钥或平台管理的密钥进行静态加密，具体取决于在磁盘中选择的加密类型。 更多信息请访问 [https://aka.ms/vm-hbe](../../../virtual-machines/disks-enable-host-based-encryption-portal.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/HostBasedEncryptionRequired_Deny.json) |
|[虚拟机应该加密计算和存储资源之间的临时磁盘、缓存和数据流](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Azure 安全中心建议对未启用磁盘加密的虚拟机进行监视。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="cryptographic-protection"></a>加密保护

**ID**：NIST SP 800-53 Rev. 4 SC-28 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应用服务环境应启用内部加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb74e86f-d351-4b8d-b034-93da7391c01f) |如果将 InternalEncryption 设置为 true，会对应用服务环境中前端和辅助角色之间的页面文件、辅助角色磁盘和内部网络流量进行加密。 若要了解详细信息，请查看 [https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-custom-settings#enable-internal-encryption](../../../app-service/environment/app-service-app-service-environment-custom-settings.md#enable-internal-encryption)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_HostingEnvironment_InternalEncryption_Audit.json) |
|[自动化帐户变量应加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |存储敏感数据时，请务必启用自动化帐户变量资产加密 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Azure Data Box 作业应为设备上静态数据启用双重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |为设备上的静态数据启用第二层基于软件的加密。 设备已通过用于静态数据的高级加密标准 256 位加密受到保护。 此选项将添加第二层数据加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[应创建启用了基础结构加密（双重加密）的 Azure Monitor 日志群集](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea0dfaed-95fb-448c-934e-d6e713ce393d) |若要确保使用两种不同的加密算法和两个不同的密钥在服务级别和基础结构级别启用安全数据加密，请使用 Azure Monitor 专用群集。 在区域受支持时，默认情况下会启用此选项，请参阅 [https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#customer-managed-key-overview](../../../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview)。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsClusters_CMKDoubleEncryptionEnabled_Deny.json) |
|[Azure Stack Edge 设备应使用双重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |若要在设备上保护静态数据，请确保静态数据已双重加密，已控制对数据的访问，并在设备停用后从数据磁盘安全地清除数据。 双重加密使用两层加密：数据卷上的 BitLocker XTS-AES 256 位加密和硬盘驱动器上的内置加密。 有关详细信息，请参阅特定 Stack Edge 设备的安全概述文档。 |审核、拒绝、已禁用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
|[应为 Azure 数据资源管理器启用磁盘加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |启用磁盘加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[应为 Azure 数据资源管理器启用双重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |启用双重加密有助于保护数据，使组织能够信守在安全性与合规性方面作出的承诺。 启用双重加密后，将使用两种不同的加密算法和两个不同的密钥对存储帐户中的数据进行两次加密，一次在服务级别，一次在基础结构级别。 |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[应为 Azure Database for MySQL 服务器启用基础结构加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a58212a-c829-4f13-9872-6371df2fd0b4) |为 Azure Database for MySQL 服务器启用基础结构加密，以便增强数据安全。 启用基础结构加密后，将使用符合 FIPS 140-2 的 Microsoft 托管密钥对静态数据进行两次加密。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_InfrastructureEncryption_Audit.json) |
|[应为 Azure Database for PostgreSQL 服务器启用基础结构加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24fba194-95d6-48c0-aea7-f65bf859c598) |为 Azure Database for PostgreSQL 服务器启用基础结构加密，以便增强数据安全。 启用基础结构加密后，将使用符合 FIPS 140-2 的 Microsoft 托管密钥对静态数据进行两次加密 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_InfrastructureEncryption_Audit.json) |
|[Microsoft 托管的控制 1664 - 保护静态信息 \| 加密保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2cdf6b8-9505-4619-b579-309ba72037ac) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1664.json) |
|[应对 SQL 数据库中的敏感数据进行分类](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure 安全中心监视 SQL 数据库的数据发现和分类扫描结果，并建议将数据库中的敏感数据分类以改善监视效果并提升安全性 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |
|[Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric 使用主要群集证书为节点之间的通信提供三个保护级别（None、Sign 和 EncryptAndSign）。 设置保护级别以确保所有节点到节点消息均已进行加密和数字签名 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[存储帐户应具有基础结构加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4733ea7b-a883-42fe-8cac-97454c2a9e4a) |启用基础结构加密，以便增强数据安全。 启用基础结构加密后，存储帐户中的数据将加密两次。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountInfrastructureEncryptionEnabled_Audit.json) |
|[应在主机处加密 Azure Kubernetes 服务群集中代理节点池的临时磁盘和缓存](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41425d9f-d1a5-499a-9932-f8ed8453932c) |为了增强数据安全性，应对存储在 Azure Kubernetes 服务节点 VM 的虚拟机 (VM) 主机上的数据进行静态加密。 这是许多法规和行业合规性标准中的常见要求。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_EncryptionAtHost_Deny.json) |
|[应在 SQL 数据库上启用透明数据加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |应启用透明数据加密以保护静态数据并满足符合性要求 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[虚拟机和虚拟机规模集应启用主机中加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc4d8e41-e223-45ea-9bf5-eada37891d87) |使用主机中加密可对虚拟机和虚拟机规模集数据进行端到端加密。 主机中加密可对临时磁盘和 OS/数据磁盘缓存实现静态加密。 启用主机中加密后，将使用平台管理的密钥对临时 OS 磁盘进行加密。 OS/数据磁盘缓存使用客户管理的密钥或平台管理的密钥进行静态加密，具体取决于在磁盘中选择的加密类型。 更多信息请访问 [https://aka.ms/vm-hbe](../../../virtual-machines/disks-enable-host-based-encryption-portal.md)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/HostBasedEncryptionRequired_Deny.json) |
|[虚拟机应该加密计算和存储资源之间的临时磁盘、缓存和数据流](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Azure 安全中心建议对未启用磁盘加密的虚拟机进行监视。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="process-isolation"></a>进程隔离

**ID**：NIST SP 800-53 Rev. 4 SC-39

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1665 - 进程隔离](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5df3a55c-8456-44d4-941e-175f79332512) |由 Microsoft 实现此系统和通信保护控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1665.json) |

## <a name="system-and-information-integrity"></a>系统和信息完整性

### <a name="system-and-information-integrity-policy-and-procedures"></a>系统和信息完整性策略和过程

**ID**：NIST SP 800-53 Rev. 4 SI-1

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1666 - 系统和信息完整性政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12e30ee3-61e6-4509-8302-a871e8ebb91e) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1666.json) |
|[Microsoft 托管的控制 1667 - 系统和信息完整性政策和程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd61880dc-6e38-4f2a-a30c-3406a98f8220) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1667.json) |

### <a name="flaw-remediation"></a>缺陷修正

**ID**：NIST SP 800-53 Rev. 4 SI-2

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应在虚拟机上启用漏洞评估解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |审核虚拟机以检测其是否正在运行受支持的漏洞评估解决方案。 每个网络风险和安全计划的核心部分都是识别和分析漏洞。 Azure 安全中心的标准定价层包括对虚拟机进行漏洞扫描，无需额外付费。 此外，安全中心可以自动为你部署此工具。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[确保用于运行 API 应用的“HTTP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |我们定期发布适用于 HTTP 的更高版本来解决安全漏洞或包含更多功能。 使用 Web 应用的最新 HTTP 版本可以利用更高版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[确保用于运行函数应用的“HTTP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |我们定期发布适用于 HTTP 的更高版本来解决安全漏洞或包含更多功能。 使用 Web 应用的最新 HTTP 版本可以利用更高版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[确保用于运行 Web 应用的“HTTP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |我们定期发布适用于 HTTP 的更高版本来解决安全漏洞或包含更多功能。 使用 Web 应用的最新 HTTP 版本可以利用更高版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |
|[确保用作 API 应用一部分的“Java 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |我们定期发布适用于 Java 的更高版本来解决安全漏洞或包含更多功能。 建议使用 API 应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[确保用作函数应用一部分的“Java 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |我们定期发布适用于 Java 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用函数应用的最新 Java 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[确保用作 Web 应用一部分的“Java 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |我们定期发布适用于 Java 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 Java 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |
|[确保用作 API 应用一部分的“PHP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |我们定期发布适用于 PHP 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 API 应用的最新 PHP 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[确保用作 WEB 应用一部分的“PHP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |我们定期发布适用于 PHP 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 PHP 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |
|[确保用作 API 应用一部分的“Python 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |我们定期发布适用于 Python 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 API 应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[确保用作函数应用一部分的“Python 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |我们定期发布适用于 Python 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用函数应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[确保用作 Web 应用一部分的“Python 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |我们定期发布适用于 Python 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |
|[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |将 Kubernetes 服务群集升级到更高 Kubernetes 版本，以抵御当前 Kubernetes 版本中的已知漏洞。 Kubernetes 版本 1.11.9+、1.12.7+、1.13.5+ 和 1.14.0+ 中已修补漏洞 CVE-2019-9946 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |
|[Microsoft 托管的控制 1668 - 缺陷修正](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fb0966e-be1d-42c3-baca-60df5c0bcc61) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1668.json) |
|[Microsoft 托管的控制 1669 - 缺陷修正](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48f2f62b-5743-4415-a143-288adc0e078d) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1669.json) |
|[Microsoft 托管的控制 1670 - 缺陷修正](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6108469-57ee-4666-af7e-79ba61c7ae0c) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1670.json) |
|[Microsoft 托管的控制 1671 - 缺陷修正](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5bbef7-a316-415b-9b38-29753ce8e698) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1671.json) |
|[SQL 数据库应已解决漏洞结果](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |监视漏洞评估扫描结果，并提供有关如何消除数据库漏洞的建议。 |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[应在虚拟机规模集上安装系统更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |审核是否缺少系统安全更新和关键更新，为了确保 Windows 和 Linux 虚拟机规模集的安全，应安装这些更新。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[应在计算机上安装系统更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |建议通过 Azure 安全中心监视服务器上缺失的安全系统更新 |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[应修复计算机上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |建议通过 Azure 安全中心监视不满足配置的基线的服务器 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[应修复虚拟机规模集上安全配置中的漏洞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |审核虚拟机规模集上的 OS 漏洞，以保护其免受攻击。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="central-management"></a>集中管理

**ID**：NIST SP 800-53 Rev. 4 SI-2 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1672 - 缺陷修正 \| 集中管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb45fe972-904e-45a4-ac20-673ba027a301) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1672.json) |

### <a name="automated-flaw-remediation-status"></a>自动缺陷修正状态

**ID**：NIST SP 800-53 Rev. 4 SI-2 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1673 - 缺陷修正 \| 自动缺陷修正状态](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdff0b90d-5a6f-491c-b2f8-b90aa402d844) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1673.json) |

### <a name="time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>修正缺陷的时间/纠正措施的基准

**ID**：NIST SP 800-53 Rev. 4 SI-2 (3)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1674 - 缺陷修正 \| 修正缺陷的时间/纠正措施的基准](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e9e233-dd0a-4bde-aea5-1371bce0e002) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1674.json) |
|[Microsoft 托管的控制 1675 - 缺陷修正 \| 修正缺陷的时间/纠正措施的基准](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffacb66e0-1c48-478a-bed5-747a312323e1) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1675.json) |

### <a name="removal-of-previous-versions-of-software--firmware"></a>删除以前版本的软件/固件

**ID**：NIST SP 800-53 Rev. 4 SI-2 (6)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[确保用于运行 API 应用的“HTTP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |我们定期发布适用于 HTTP 的更高版本来解决安全漏洞或包含更多功能。 使用 Web 应用的最新 HTTP 版本可以利用更高版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[确保用于运行函数应用的“HTTP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |我们定期发布适用于 HTTP 的更高版本来解决安全漏洞或包含更多功能。 使用 Web 应用的最新 HTTP 版本可以利用更高版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[确保用于运行 Web 应用的“HTTP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |我们定期发布适用于 HTTP 的更高版本来解决安全漏洞或包含更多功能。 使用 Web 应用的最新 HTTP 版本可以利用更高版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |
|[确保用作 API 应用一部分的“Java 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |我们定期发布适用于 Java 的更高版本来解决安全漏洞或包含更多功能。 建议使用 API 应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[确保用作函数应用一部分的“Java 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |我们定期发布适用于 Java 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用函数应用的最新 Java 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[确保用作 Web 应用一部分的“Java 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |我们定期发布适用于 Java 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 Java 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |
|[确保用作 API 应用一部分的“PHP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |我们定期发布适用于 PHP 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 API 应用的最新 PHP 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[确保用作 WEB 应用一部分的“PHP 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |我们定期发布适用于 PHP 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 PHP 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |
|[确保用作 API 应用一部分的“Python 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |我们定期发布适用于 Python 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 API 应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[确保用作函数应用一部分的“Python 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |我们定期发布适用于 Python 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用函数应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[确保用作 Web 应用一部分的“Python 版本”是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |我们定期发布适用于 Python 软件的更高版本来解决安全漏洞或包含更多功能。 建议使用 Web 应用的最新 Python 版本，以充分利用最新版本的安全修复（如果有）和/或新功能。 目前，此策略仅适用于 Linux Web 应用。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |
|[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |将 Kubernetes 服务群集升级到更高 Kubernetes 版本，以抵御当前 Kubernetes 版本中的已知漏洞。 Kubernetes 版本 1.11.9+、1.12.7+、1.13.5+ 和 1.14.0+ 中已修补漏洞 CVE-2019-9946 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

### <a name="malicious-code-protection"></a>恶意代码防护

**ID**：NIST SP 800-53 Rev. 4 SI-3

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应在虚拟机规模集上安装终结点保护解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |审核终结点保护解决方案在虚拟机规模集上的存在性和运行状况 ，以保护其免受威胁和漏洞的侵害。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft 托管的控制 1676 - 恶意代码防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10fb58b-56a8-489e-9ce3-7ffe24e78e4b) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1676.json) |
|[Microsoft 托管的控制 1677 - 恶意代码防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a248e1e-040f-43e5-bff2-afc3a57a3923) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1677.json) |
|[Microsoft 托管的控制 1678 - 恶意代码防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd533cb0-b416-4be7-8e86-4d154824dfd7) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1678.json) |
|[Microsoft 托管的控制 1679 - 恶意代码防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cf42a28-193e-41c5-98df-7688e7ef0a88) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1679.json) |
|[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |建议通过 Azure 安全中心监视未安装 Endpoint Protection 代理的服务器 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[应在计算机上启用 Windows Defender 攻击防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender 攻击防护使用 Azure Policy 来宾配置代理。 攻击防护服务具有 4 个组件，旨在锁定设备来阻隔各种攻击途径，并阻止恶意软件攻击中常用的行为，同时让企业能够平衡其安全风险和生产力要求（仅限 Windows）。 |AuditIfNotExists、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="central-management"></a>集中管理

**ID**：NIST SP 800-53 Rev. 4 SI-3 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应在虚拟机规模集上安装终结点保护解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |审核终结点保护解决方案在虚拟机规模集上的存在性和运行状况 ，以保护其免受威胁和漏洞的侵害。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft 托管的控制 1680 - 恶意代码防护 \| 集中管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399cd6ee-0e18-41db-9dea-cde3bd712f38) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1680.json) |
|[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |建议通过 Azure 安全中心监视未安装 Endpoint Protection 代理的服务器 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[应在计算机上启用 Windows Defender 攻击防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender 攻击防护使用 Azure Policy 来宾配置代理。 攻击防护服务具有 4 个组件，旨在锁定设备来阻隔各种攻击途径，并阻止恶意软件攻击中常用的行为，同时让企业能够平衡其安全风险和生产力要求（仅限 Windows）。 |AuditIfNotExists、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="automatic-updates"></a>自动更新

**ID**：NIST SP 800-53 Rev. 4 SI-3 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1681 - 恶意代码防护 \| 自动更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12623e7e-4736-4b2e-b776-c1600f35f93a) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1681.json) |

### <a name="nonsignature-based-detection"></a>基于非签名的检测

**ID**：NIST SP 800-53 Rev. 4 SI-3 (7)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1682 - 恶意代码防护 \| 基于非签名的检测](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62b638c5-29d7-404b-8d93-f21e4b1ce198) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1682.json) |

### <a name="information-system-monitoring"></a>信息系统监视

**ID**：NIST SP 800-53 Rev. 4 SI-4

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[所有 Internet 流量都应通过所部署的 Azure 防火墙进行路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure 安全中心已确认，你的某些子网未使用下一代防火墙进行保护。 通过使用 Azure 防火墙或受支持的下一代防火墙限制对子网的访问，保护子网免受潜在威胁的危害 |AuditIfNotExists、Disabled |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[你的订阅应启用 Log Analytics 代理自动预配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |为了监视安全漏洞和威胁，Azure 安全中心会从 Azure 虚拟机收集数据。 数据是使用 Log Analytics 代理收集的，该代理以前称为 Microsoft Monitoring Agent (MMA)，它从计算机中读取各种安全相关的配置和事件日志，然后将数据复制到 Log Analytics 工作区以用于分析。 建议启用自动预配，将代理自动部署到所有受支持的 Azure VM 和任何新创建的 VM。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[已启用 Azure Arc 的 Kubernetes 群集应安装 Azure Defender 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc 的 Azure Defender 扩展为你启用了 Arc 的 Kubernetes 群集提供威胁防护。 该扩展从群集中的节点收集数据，并将数据发送到云中的 Azure Defender for Kubernetes 后端做进一步分析。 若要了解详细信息，请访问 [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)。 |AuditIfNotExists、Disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[应启用适用于应用服务的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |适用于应用服务的 Azure Defender 利用云的规模以及 Azure 作为云提供商的可见性来监视常见的 Web 应用攻击。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[应启用适用于 Azure SQL 数据库服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[应启用适用于容器注册表的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |适用于容器注册表的 Azure Defender 提供过去 30 天内拉取、推送到注册表或导入的任何图像的漏洞扫描，并公开每个映像的详细发现。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[应启用 Azure Defender for DNS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for DNS 的功能，请参阅 [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[应启用 Azure Defender for Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |适用于 Key Vault 的 Azure Defender 通过检测旨在访问或利用密钥保管库帐户的不寻常和可能有害的尝试，提供额外的保护层和安全智能。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[应启用 Azure Defender for Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes 为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[应启用 Azure Defender for Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。 若要详细了解 Azure Defender for Resource Manager 的功能，请参阅 [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)。 启用此 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页：[https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[应启用适用于计算机上的 SQL 服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL 提供了以下功能：呈现和缓解潜在数据库漏洞、检测可能指示对 SQL 数据库产生威胁的异常活动，以及发现敏感数据并对其进行分类。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[应为未受保护的 Azure SQL 服务器启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[应为未受保护的 SQL 托管实例启用 Azure Defender for SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |审核所有未启用高级数据安全的 SQL 托管实例。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[应启用适用于存储的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |适用于存储的 Azure Defender 可检测旨在访问或恶意利用存储帐户的异常和可能有害的企图。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[应在计算机上安装来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |若要确保安全配置计算机的来宾内设置，请安装来宾配置扩展。 该扩展监视的来宾内设置包括操作系统的配置、应用程序配置或状态以及环境设置。 安装后，来宾内策略将可用，如“应启用 Windows 攻击防护”。 更多信息请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md)。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[应在计算机上解决 Log Analytics 代理运行状况问题](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |安全中心使用 Log Analytics 代理，它之前被称为 Microsoft Monitoring Agent (MMA)。 为了确保成功监视虚拟机，需要确保此代理安装在虚拟机上，并能正确地将安全事件收集到配置的工作区中。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Log Analytics 代理应安装在 Linux Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |如果 Log Analytics 代理未安装，此策略审核 Linux Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Log Analytics 代理应安装在虚拟机上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |此策略审核是否有任何 Windows/Linux 虚拟机 (VM) 没有安装安全中心用于监视安全漏洞和威胁的 Log Analytics 代理 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Log Analytics 代理应安装在虚拟机规模集上，用于 Azure 安全中心监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |安全中心从 Azure 虚拟机 (VM) 收集数据，以监视安全漏洞和威胁。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Log Analytics 代理应安装在 Windows Azure Arc 计算机中](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |如果 Log Analytics 代理未安装，此策略审核 Windows Azure Arc 计算机。 |AuditIfNotExists、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 托管的控制 1683 - 信息系统监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c79fee4-88dd-44ce-bbd4-4de88948c4f8) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1683.json) |
|[Microsoft 托管的控制 1684 - 信息系统监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16bfdb59-db38-47a5-88a9-2e9371a638cf) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1684.json) |
|[Microsoft 托管的控制 1685 - 信息系统监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36b0ef30-366f-4b1b-8652-a3511df11f53) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1685.json) |
|[Microsoft 托管的控制 1686 - 信息系统监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe17085c5-0be8-4423-b39b-a52d3d1402e5) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1686.json) |
|[Microsoft 托管的控制 1687 - 信息系统监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a87fc7f-301e-49f3-ba2a-4d74f424fa97) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1687.json) |
|[Microsoft 托管的控制 1688 - 信息系统监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063c3f09-e0f0-4587-8fd5-f4276fae675f) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1688.json) |
|[Microsoft 托管的控制 1689 - 信息系统监视](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fde901f2f-a01a-4456-97f0-33cda7966172) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1689.json) |
|[应在 Linux 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[应在 Windows 虚拟机上安装网络流量数据收集代理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |安全中心使用 Microsoft Dependency Agent 从 Azure 虚拟机收集网络流量数据，以启用高级网络保护功能，如网络映射上的流量可视化、网络强化建议和特定网络威胁。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[应启用网络观察程序](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 需要在存在虚拟网络的每个区域中创建一个网络观察程序资源组。 如果网络观察程序资源组在特定区域中不可用，则会启用警报。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[应使用系统分配的托管标识来部署虚拟机的来宾配置扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |来宾配置扩展需要系统分配的托管标识。 如果安装了来宾配置扩展，但没有系统分配的托管标识，则此策略作用域内的 Azure 虚拟机是不合规的。 有关详细信息，请访问 [https://aka.ms/gcpol](../concepts/guest-configuration.md) |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="system-wide-intrusion-detection-system"></a>系统范围的入侵检测系统

**ID**：NIST SP 800-53 Rev. 4 SI-4 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1690 - 信息系统监视 \| 系统范围的入侵检测系统](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2567a23-d1c3-4783-99f3-d471302a4d6b) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1690.json) |

### <a name="automated-tools-for-real-time-analysis"></a>用于实时分析的自动化工具

**ID**：NIST SP 800-53 Rev. 4 SI-4 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1691 - 信息系统监视 \| 用于实时分析的自动化工具](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71475fb4-49bd-450b-a1a5-f63894c24725) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1691.json) |

### <a name="inbound-and-outbound-communications-traffic"></a>入站和出站通信流量

**ID**：NIST SP 800-53 Rev. 4 SI-4 (4)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1692 - 信息系统监视 \| 入站和出站通信流量](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ecda928-9df4-4dd7-8f44-641a91e470e8) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1692.json) |

### <a name="system-generated-alerts"></a>系统生成的警报

**ID**：NIST SP 800-53 Rev. 4 SI-4 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1693 - 信息系统监视 \| 系统生成的警报](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa450eba6-2efc-4a00-846a-5804a93c6b77) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1693.json) |

### <a name="analyze-communications-traffic-anomalies"></a>分析通信流量异常

**ID**：NIST SP 800-53 Rev. 4 SI-4 (11)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1694 - 信息系统监视 \| 分析通信流量异常](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F426c4ac9-ff17-49d0-acd7-a13c157081c0) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1694.json) |

### <a name="automated-alerts"></a>自动警报

**ID**：NIST SP 800-53 Rev. 4 SI-4 (12)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用高严重性警报的电子邮件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |当某个订阅中存在潜在的安全漏洞时，若要确保组织中的相关人员收到通知，请在安全中心为高严重性警报启用电子邮件通知。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[应启用向订阅所有者发送高严重性警报的电子邮件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |当订阅中存在潜在的安全漏洞时，若要确保订阅所有者收到通知，请在安全中心设置向订阅所有者发送高严重性警报的电子邮件通知。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[订阅应有一个联系人电子邮件地址，用于接收安全问题通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |当某个订阅中存在潜在的安全漏洞时，若要确保组织中的相关人员收到通知，请设置一个安全联系人，以接收来自安全中心的电子邮件通知。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="wireless-intrusion-detection"></a>无线入侵检测

**ID**：NIST SP 800-53 Rev. 4 SI-4 (14)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1695 - 信息系统监视 \| 无线入侵检测](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13fcf812-ec82-4eda-9b89-498de9efd620) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1695.json) |

### <a name="correlate-monitoring-information"></a>关联监视信息

**ID**：NIST SP 800-53 Rev. 4 SI-4 (16)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1696 - 信息系统监视 \| 关联监视信息](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69d2a238-20ab-4206-a6dc-f302bf88b1b8) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1696.json) |

### <a name="analyze-traffic--covert-exfiltration"></a>分析流量/隐蔽外泄

**ID**：NIST SP 800-53 Rev. 4 SI-4 (18)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1697 - 信息系统监视 \| 分析流量/隐蔽渗透](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9873db2-18ad-46b3-a11a-1a1f8cbf0335) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1697.json) |

### <a name="individuals-posing-greater-risk"></a>个人带来的更大风险

**ID**：NIST SP 800-53 Rev. 4 SI-4 (19)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1698 - 信息系统监视 \| 个人带来的更大风险](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F31b752c1-05a9-432a-8fce-c39b56550119) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1698.json) |

### <a name="privileged-users"></a>特权用户

**ID**：NIST SP 800-53 Rev. 4 SI-4 (20)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1699 - 信息系统监视 \| 特权用户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69c7bee8-bc19-4129-a51e-65a7b39d3e7c) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1699.json) |

### <a name="unauthorized-network-services"></a>未经授权的网络服务

**ID**：NIST SP 800-53 Rev. 4 SI-4 (22)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1700 - 信息系统监视 \| 未经授权的网络服务](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7831b4ba-c3f4-4cb1-8c11-ef8d59438cd5) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1700.json) |

### <a name="host-based-devices"></a>基于主机的设备

**ID**：NIST SP 800-53 Rev. 4 SI-4 (23)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1701 - 信息系统监视 \| 基于主机的设备](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff25bc08f-27cb-43b6-9a23-014d00700426) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1701.json) |

### <a name="indicators-of-compromise"></a>入侵指标

**ID**：NIST SP 800-53 Rev. 4 SI-4 (24)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1702 - 信息系统监视 \| 泄露指示器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4dfc0855-92c4-4641-b155-a55ddd962362) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1702.json) |

### <a name="security-alerts-advisories-and-directives"></a>安全警报、公告和指令

**ID**：NIST SP 800-53 Rev. 4 SI-5

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1703 - 安全警报、公告和指令](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F804faf7d-b687-40f7-9f74-79e28adf4205) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1703.json) |
|[Microsoft 托管的控制 1704 - 安全警报、公告和指令](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d44b6fa-1134-4ea6-ad4e-9edb68f65429) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1704.json) |
|[Microsoft 托管的控制 1705 - 安全警报、公告和指令](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff82e3639-fa2b-4e06-a786-932d8379b972) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1705.json) |
|[Microsoft 托管的控制 1706 - 安全警报、公告和指令](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff475ee0e-f560-4c9b-876b-04a77460a404) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1706.json) |

### <a name="automated-alerts-and-advisories"></a>自动警报和公告

**ID**：NIST SP 800-53 Rev. 4 SI-5 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1707 - 安全警报、公告和指令 \| 自动警报和公告](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4a2ac8-868a-4702-a345-6c896c3361ce) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1707.json) |

### <a name="security-function-verification"></a>安全功能验证

**ID**：NIST SP 800-53 Rev. 4 SI-6

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1708 - 安全功能验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a1e2c88-13de-4959-8ee7-47e3d74f1f48) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1708.json) |
|[Microsoft 托管的控制 1709 - 安全功能验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F025992d6-7fee-4137-9bbf-2ffc39c0686c) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1709.json) |
|[Microsoft 托管的控制 1710 - 安全功能验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf2a93c8-e6dd-4c94-acdd-4a2eedfc478e) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1710.json) |
|[Microsoft 托管的控制 1711 - 安全功能验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb083a535-a66a-41ec-ba7f-f9498bf67cde) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1711.json) |

### <a name="software-firmware-and-information-integrity"></a>软件、固件和信息完整性

**ID**：NIST SP 800-53 Rev. 4 SI-7

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1712 - 软件、固件和信息完整性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44e543aa-41db-42aa-98eb-8a5eb1db53f0) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1712.json) |

### <a name="integrity-checks"></a>完整性检查

**ID**：NIST SP 800-53 Rev. 4 SI-7 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1713 - 软件、固件和信息完整性 \| 完整性检查](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d87c70b-5012-48e9-994b-e70dd4b8def0) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1713.json) |

### <a name="automated-notifications-of-integrity-violations"></a>自动通知完整性冲突

**ID**：NIST SP 800-53 Rev. 4 SI-7 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1714 - 软件、固件和信息完整性 \| 自动通知完整性冲突](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe12494fa-b81e-4080-af71-7dbacc2da0ec) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1714.json) |

### <a name="automated-response-to-integrity-violations"></a>自动响应完整性冲突

**ID**：NIST SP 800-53 Rev. 4 SI-7 (5)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1715 - 软件、固件和信息完整性 \| 自动响应完整性冲突](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd469ae0-71a8-4adc-aafc-de6949ca3339) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1715.json) |

### <a name="integration-of-detection-and-response"></a>检测和响应的集成

**ID**：NIST SP 800-53 Rev. 4 SI-7 (7)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1716 - 软件、固件和信息完整性 \| 检测和响应的集成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe54c325e-42a0-4dcf-b105-046e0f6f590f) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1716.json) |

### <a name="binary-or-machine-executable-code"></a>二进制或计算机可执行代码

**ID**：NIST SP 800-53 Rev. 4 SI-7 (14)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1717 - 软件、固件和信息完整性 \| 二进制或计算机可执行代码](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967773fc-d9ab-4a4e-8ff6-f5e9e3f5dbef) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1717.json) |
|[Microsoft 托管的控制 1718 - 软件、固件和信息完整性 \| 二进制或计算机可执行代码](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0dced7ab-9ce5-4137-93aa-14c13e06ab17) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1718.json) |

### <a name="spam-protection"></a>垃圾邮件防护

**ID**：NIST SP 800-53 Rev. 4 SI-8

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1719 - 垃圾邮件防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc13da9b4-fe14-4fe2-853a-5997c9d4215a) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1719.json) |
|[Microsoft 托管的控制 1720 - 垃圾邮件防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44b9a7cd-f36a-491a-a48b-6d04ae7c4221) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1720.json) |

### <a name="central-management"></a>集中管理

**ID**：NIST SP 800-53 Rev. 4 SI-8 (1)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1721 - 垃圾邮件防护 \| 集中管理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd207aaef-7c4d-4f8c-9dce-4d62dfa3d29a) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1721.json) |

### <a name="automatic-updates"></a>自动更新

**ID**：NIST SP 800-53 Rev. 4 SI-8 (2)

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1722 - 垃圾邮件防护 \| 自动更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1da06bd-25b6-4127-a301-c313d6873fff) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1722.json) |

### <a name="information-input-validation"></a>信息输入验证

**ID**：NIST SP 800-53 Rev. 4 SI-10

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1723 - 信息输入验证](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe91927a0-ac1d-44a0-95f8-5185f9dfce9f) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1723.json) |

### <a name="error-handling"></a>错误处理

**ID**：NIST SP 800-53 Rev. 4 SI-11

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1724 - 错误处理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd07594d1-0307-4c08-94db-5d71ff31f0f6) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1724.json) |
|[Microsoft 托管的控制 1725 - 错误处理](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafc234b5-456b-4aa5-b3e2-ce89108124cc) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1725.json) |

### <a name="information-handling-and-retention"></a>信息处理和保留

**ID**：NIST SP 800-53 Rev. 4 SI-12

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 托管的控制 1726 - 信息处理和保留](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaff1279-05e0-4463-9a70-8ba5de4c7aa4) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1726.json) |

### <a name="memory-protection"></a>内存保护

**ID**：NIST SP 800-53 Rev. 4 SI-16

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应启用适用于服务器的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |适用于服务器的 Azure Defender 可为服务器工作负载提供实时威胁防护，并生成强化建议以及有关可疑活动的警报。 |AuditIfNotExists、Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Microsoft 托管的控制 1727 - 内存保护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F697175a7-9715-4e89-b98b-c6f605888fa3) |由 Microsoft 实现此系统和信息完整性控制 |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1727.json) |
|[应在计算机上启用 Windows Defender 攻击防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender 攻击防护使用 Azure Policy 来宾配置代理。 攻击防护服务具有 4 个组件，旨在锁定设备来阻隔各种攻击途径，并阻止恶意软件攻击中常用的行为，同时让企业能够平衡其安全风险和生产力要求（仅限 Windows）。 |AuditIfNotExists、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

> [!NOTE]
> 特定 Azure Policy 定义的可用性在 Azure 政府和其他国家云中可能会有所不同。

## <a name="next-steps"></a>后续步骤

有关 Azure Policy 的其他文章：

- [法规符合性](../concepts/regulatory-compliance.md)概述。
- 请参阅[计划定义结构](../concepts/initiative-definition-structure.md)。
- 在 [Azure Policy 示例](./index.md)中查看其他示例。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。