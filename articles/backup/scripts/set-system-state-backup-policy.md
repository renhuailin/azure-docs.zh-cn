---
title: 脚本示例 - 创建新的备份策略或修改当前系统状态备份策略
description: 了解如何使用脚本创建新的备份策略或修改当前系统状态备份策略。
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 5adebca2bbf98307b33a13318334baf1670f5f19
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560791"
---
#  <a name="powershell-script-to-create-a-new-or-modify-the-current-system-state-backup-policy"></a>用于创建新的备份策略或修改当前系统状态备份策略的 PowerShell 脚本

此脚本可帮助你为受 MARS 代理保护的服务器创建新的备份策略或修改当前系统状态备份策略集。

## <a name="sample-script"></a>示例脚本

```azurepowershell
<#

.SYNOPSIS
Modify system state policy

.DESCRIPTION
Modify system state policy

.ROLE
Administrators

#>
param (
    [Parameter(Mandatory = $true)]
    [string[]]
    $daysOfWeek,
    [Parameter(Mandatory = $true)]
    [string[]]
    $timesOfDay,
    [Parameter(Mandatory = $true)]
    [int]
    $weeklyFrequency,

    [Parameter(Mandatory = $false)]
    [int]
    $retentionDays,

    [Parameter(Mandatory = $false)]
    [Boolean]
    $retentionWeeklyPolicy,
    [Parameter(Mandatory = $false)]
    [int]
    $retentionWeeks,

    [Parameter(Mandatory = $false)]
    [Boolean]
    $retentionMonthlyPolicy,
    [Parameter(Mandatory = $false)]
    [int]
    $retentionMonths
)
Set-StrictMode -Version 5.0
$env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
Import-Module MSOnlineBackup
$ErrorActionPreference = "Stop"
Try {
    $oldPolicy = Get-OBSystemStatePolicy
    if ($oldPolicy) {
        return
    }
    $policy = New-OBPolicy
    $policy = Add-OBSystemState -Policy $policy

    $timesOfDaySchedule = @()
    foreach ($time in $timesOfDay) {
        $timesOfDaySchedule += ([TimeSpan]$time)
    }
    $daysOfWeekSchedule = @()
    foreach ($day in $daysOfWeek) {
        $daysOfWeekSchedule += ([System.DayOfWeek]$day)
    }

    $schedule = New-OBSchedule -DaysOfWeek $daysOfWeekSchedule -TimesOfDay $timesOfDaySchedule -WeeklyFrequency $weeklyFrequency
    if ($daysOfWeekSchedule.Count -eq 7) {
        if ($retentionWeeklyPolicy -and $retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        elseif ($retentionWeeklyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks
        }
        elseif ($retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        else {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays
        }
    }
    else {
        if ($retentionWeeklyPolicy -and $retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        elseif ($retentionWeeklyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks
        }
        elseif ($retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
    }
    Set-OBSchedule -Policy $policy -Schedule $schedule
    Set-OBRetentionPolicy -Policy $policy -RetentionPolicy $retention
    Set-OBSystemStatePolicy -Policy $policy -Confirm:$false
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="how-to-execute-the-script"></a>如何执行脚本

1. 使用所选名称和 .ps1 扩展名将上述脚本保存在计算机上。
1. 通过提供以下参数来执行脚本： <br> 备份计划和备份需要保留的天数/周数/月数或年数。

## <a name="next-steps"></a>后续步骤

[详细了解](../backup-client-automation.md)如何使用 PowerShell 通过 MARS 代理部署和管理本地备份。