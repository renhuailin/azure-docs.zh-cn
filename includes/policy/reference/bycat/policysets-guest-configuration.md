---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/16/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 06f337cb524b1ca158baf0627ccdfa88c4ae4e40
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114387769"
---
|名称 |说明 |策略 |版本 |
|---|---|---|---|
|[审核带有不安全密码安全设置的计算机](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |此计划可部署策略要求，并审核带有不安全的密码安全设置的计算机。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) |9 |1.0.0 |
|[部署先决条件以在虚拟机上启用来宾配置策略](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |此计划添加系统分配的托管标识，并向符合来宾配置策略监视条件的虚拟机部署平台相应的来宾配置扩展。 这是所有来宾配置策略的先决条件，在使用任何来宾配置策略之前必须被分配到策略分配范围。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md)。 |4 |1.0.0 |
|[\[预览版\]：Windows 计算机应符合 Azure 计算安全基线的要求](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |此计划审核其设置不符合 Azure 计算安全基线的 Windows 计算机。 有关详细信息，请访问 [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md) |29 |2.0.1-preview |