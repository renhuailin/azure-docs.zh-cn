---
title: 合并注册故障排除 - Azure Active Directory
description: Azure AD 多重身份验证和自助式密码重置合并注册故障排除
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: db87887fc2b51c7cb8cb300eb8e711d3ae9b6ac8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610772"
---
# <a name="troubleshooting-combined-security-information-registration"></a>合并安全信息注册故障排除

本文信息旨在指导管理员对用户报告的合并注册体验问题进行故障排除。

## <a name="audit-logs"></a>审核日志

合并注册的事件日志位于 Azure AD 审核日志中的身份验证方法服务中。

![显示注册事件的 Azure AD 审核日志界面](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

下表列出了合并注册生成的所有审核事件：

| 活动 | 状态 | 原因 | 说明 |
| --- | --- | --- | --- |
| 用户已注册所有必需的安全信息 | 成功 | 用户已注册所有必需的安全信息。 | 当用户成功完成注册时会发生此事件。|
| 用户已注册所有必需的安全信息 | 失败 | 用户取消了安全信息注册。 | 当用户从中断模式取消注册时发生此事件。|
| 用户已注册安全信息 | 成功 | 用户已注册“方法”。 | 当用户注册单个方法时发生此事件。 “方法”可以是 Authenticator 应用、电话、电子邮件、安全问题、应用密码、备用电话等。| 
| 用户已审阅安全信息 | 成功 | 用户已成功审阅安全信息。 | 当用户在“安全信息审阅”页上选择“看上去不错”时发生此事件。|
| 用户已审阅安全信息 | 失败 | 用户无法审阅安全信息。 | 当用户在“安全信息审阅”页上选择“看上去不错”但后端出现失败时发生此事件。|
| 用户已删除安全信息 | 成功 | 用户已删除“方法”。 | 当用户删除单个方法时发生此事件。 “方法”可以是 Authenticator 应用、电话、电子邮件、安全问题、应用密码、备用电话等。|
| 用户已删除安全信息 | 失败 | 用户无法删除“方法”。 | 当用户尝试删除某方法，但出于某种原因尝试失败时发生此事件。 “方法”可以是 Authenticator 应用、电话、电子邮件、安全问题、应用密码、备用电话等。|
| 用户已更改默认安全信息 | 成功 | 用户已更改“方法”的默认安全信息。 | 当用户更改默认方法时发生此事件。 “方法”可以是 Authenticator 应用通知、来自我的验证器应用或令牌的代码、拨打 +X XXXXXXXXXX、将代码发到 +X XXXXXXXXX 等。|
| 用户已更改默认安全信息 | 失败 | 用户无法更改“方法”的默认安全信息。 | 当用户尝试更改默认方法，但出于某种原因尝试失败时发生此事件。 “方法”可以是 Authenticator 应用通知、来自我的验证器应用或令牌的代码、拨打 +X XXXXXXXXXX、将代码发到 +X XXXXXXXXX 等。|

## <a name="troubleshooting-interrupt-mode"></a>中断模式故障排除

| 症状 | 疑难解答步骤 |
| --- | --- |
| 我没有看到预期应看到的方法。 | 1.检查用户是否拥有 Azure AD 管理员角色。 如果拥有，请查看 SSPR 管理员策略差异。 <br> 2.确定用户是否由于强制多重身份验证注册或强制 SSPR 注册而被中断。 查看“合并注册模式”下的[流程图](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes)来确定应当显示的方法。 <br> 3.确定多重身份验证或 SSPR 策略最近的更改情况。 如果更改在最近发生，则更新的策略可能需要一些时间传播。|

## <a name="troubleshooting-manage-mode"></a>管理模式故障排除

| 症状 | 疑难解答步骤 |
| --- | --- |
| 我没有添加特定方法的选项。 | 1.确定是否为多重身份验证或 SSPR 启用了方法。 <br> 2.如果已启用方法，请再次保存策略并等待 1-2 小时，然后再进行测试。 <br> 3.如果方法已启用，请确保用户尚未设置允许其设置的该方法的最大数目。|

## <a name="disable-combined-registration"></a>禁用合并注册

当用户在新的合并体验中注册电话号码和/或移动应用时，我们的服务将为该用户的这些方法标记一组标志 (StrongAuthenticationMethods)。 通过此功能，用户可在需要进行多重身份验证时使用这些方法执行多重身份验证。

如果管理员启用预览，用户通过新体验注册，则在管理员禁用预览后，用户也可能在不知情的情况下注册了多重身份验证。

如果已完成合并注册的用户转到当前的自助式密码重置 (SSPR) 注册页面 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)，系统会提示用户进行多重身份验证，然后才能访问该页面。 从技术角度看此步骤在意料之中，但对于之前仅注册了 SSPR 的用户来说是新内容。 尽管这一额外步骤通过提供另一级别的安全性来提升用户的安全状况，但管理员可能希望回退用户，使其不能再执行多重身份验证。  

### <a name="how-to-roll-back-users"></a>如何回退用户

如果你作为管理员想要重置用户的多重身份验证设置，则可以使用下一节提供的 PowerShell 脚本。 此脚本将清除用户的移动应用和/或电话号码的 StrongAuthenticationMethods 属性。 如果为用户运行此脚本，则用户（如有需要）需重新注册多重身份验证。 我们建议在回退所有受影响的用户之前，先通过一个或两个用户测试回退。

下面的步骤将帮助回退一个或一组用户。

#### <a name="prerequisites"></a>先决条件

1. 安装相应的 Azure AD PowerShell 模块。 在 PowerShell 窗口中，运行以下命令以安装模块：

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 在计算机中将受影响的用户对象 ID 列表另存为每行一个 ID 的文本文件。 记下该文件的位置。
1. 将以下脚本保存到计算机，并记下该脚本的位置：

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>回退

在 PowerShell 窗口中，运行以下命令，提供脚本和用户文件位置。 出现提示时，输入全局管理员凭据。 该脚本将输出每个用户更新操作的结果。

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>禁用更新体验

若要禁用用户的更新体验，请完成这些步骤：

1. 以用户管理员的身份登录到 Azure 门户。
2. 转到“Azure Active Directory” > “用户设置” > “管理访问面板预览功能的设置”  。
3. 在“用户可以使用预览功能来注册和管理安全信息”下，将选择器设置为“无”，然后选择“保存”。

系统将不再提示用户使用更新体验进行注册。

## <a name="next-steps"></a>后续步骤

* [详细了解自助式密码重置和 Azure AD 多重身份验证的合并注册](concept-registration-mfa-sspr-combined.md)
