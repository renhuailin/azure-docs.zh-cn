---
title: include 文件
description: include 文件
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 01512f0e37761915fcb6eeba8c162e1a3db62c48
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491657"
---
```console
Robocopy /MT:16 /R:5 /W:5 /B /MIR /IT /COPY:DATSO /DCOPY:DAT /NP /NFL /NDL /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| 开关                | 含义 |
|-----------------------|---------|
| `/MT:n`               | 允许 RoboCopy 多线程运行。 默认 n 为 8，最多 128 个线程。 将此值与处理器核心数和每个核心的线程数相匹配。 考虑是否需要为生产服务器可能具有的其他任务预留核心。 |
| `/R:n`                | 通过为第一次复制尝试失败的文件指定最大重试次数，可以提高 RoboCopy 运行的速度。 n 是在此 RoboCopy 运行中文件复制永久失败之前的最大重试次数。 在已明确会出现更多 RoboCopy 运行的情况下，此开关的效果最佳。 如果文件在此运行中复制失败，则下一个 RoboCopy 作业将重试。 通常，由于正在使用或超时问题而失败的文件最终可能会以这种方式成功复制。 |
| `/W:n`                | 此开关指定在尝试复制上一次复制尝试未成功的文件之前 RoboCopy 等待的时间。 n 是重试之间等待的秒数。 `/W:n` 通常与 `/R:n` 一起使用。 |
| `/B`                  | 在备份应用程序使用的相同模式下运行 RoboCopy。 允许 RoboCopy 移动当前用户没有权限的文件。 |
| `/MIR`                | 将源镜像映射到目标使 RoboCopy 只需复制源和目标之间的增量。 将复制空子目录。 将复制目标上已更改或不存在的项（文件或文件夹）。 将从目标上清除（删除）目标上存在但源上没有的项。 使用此开关时，必须完全匹配源和目标文件夹结构。 “匹配”表示从正确的源和文件夹级别复制到目标上的匹配文件夹级别。 只有这样，才能成功完成“追赶”复制。 当源和目标不匹配时，使用 `/MIR` 将导致大规模删除和重新复制。 |
| `/IT`                 | 确保在某些镜像方案中保留保真度。 </br>示例 - 如果在两个 RoboCopy 运行之间，文件遇到 ACL 更改和属性更新的情况：例如，它被标记为“已隐藏” 。 如果没有 /IT，RoboCopy 可能会忽略 ACL 更改，并且不会将其传输到目标位置。 |
|`/COPY:[copyflags]`    | 文件复制的保真度（如果未指定，默认值为 `/COPY:DAT`），复制标志：`D`=数据，`A`=属性，`T`=时间戳，`S`=安全性=NTFS ACL，`O`=所有者信息，`U`=审核信息 (A<u>u</u>diting)。 审核信息无法存储在 Azure 文件共享中。 |
| `/DCOPY:[copyflags]`  | 目录复制的保真度（如果未指定，默认值为 `/DCOPY:DA`），复制标志：`D`=数据，`A`=属性，`T`=时间戳。 |
| `/NP`                 | 不会显示每个文件和文件夹的复制进度。 显示进度会明显降低复制性能。 |
| `/NFL`                | 指定不记录文件名。 提高复制性能。 |
| `/NDL`                | 指定不记录目录名。 提高复制性能。 |
| `/UNILOG:<file name>` | 将状态作为 UNICODE 输出到 LOG 文件（覆盖现有日志）。 |
| `/LFSM`               | **仅适用于具有分层存储的目标** </br>使用 /LFSM 请求 RoboCopy 在“低可用空间模式”下运行。 此开关仅对具有分层存储的目标有用，这些目标可能会在 RoboCopy 完成之前耗尽本地容量。 此开关是为了与启用了 Azure 文件同步云分层的目标一起使用而专门添加的。 可独立于 Azure 文件同步使用。在此模式下，每当文件复制导致目标卷的可用空间低于“下限”值时，RoboCopy 都会暂停。 可通过标志的 `/LFSM:n` 形式指定此值。 参数 `n` 在基数 2 `nKB`、`nMB` 或 `nGB` 中指定。 如果指定的 `/LFSM` 没有明确的下限值，则下限将设置为目标卷大小的 10%。 低可用空间模式与 /MT、/EFSRAW、/B 和 /ZB 不兼容。 |
| `/Z`                  | **小心使用** </br>以重启模式复制文件，建议仅在不稳定的网络环境中使用。 由于额外的日志记录，此选项会明显降低复制性能。 |
| `/ZB`                 | **小心使用** </br>使用重启模式。 如果访问被拒绝，此选项将使用备份模式。 由于检查点，此选项会明显降低复制性能。 |
   