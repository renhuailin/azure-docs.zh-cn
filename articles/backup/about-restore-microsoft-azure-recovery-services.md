---
title: 使用 Microsoft Azure 恢复服务 (MARS) 代理的还原选项
description: 了解 Microsoft Azure 恢复服务 (MARS) 代理的可用还原选项。
ms.reviewer: mepand
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: 6c77ee568b4f6c7643672d38ba543f5eab7db369
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295712"
---
# <a name="about-restore-using-the-microsoft-azure-recovery-services-mars-agent"></a>关于使用 Microsoft Azure 恢复服务 (MARS) 代理进行复原 

本文描述了 Microsoft Azure 恢复服务 (MARS) 代理的可用还原选项。

## <a name="before-you-begin"></a>准备阶段

- 确保安装了最新版本的 [MARS 代理](https://aka.ms/azurebackup_agent)。
- 确保禁用了[网络限制](backup-windows-with-mars-agent.md#enable-network-throttling)。
- 确保[代理缓存文件夹](/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)有高速度、大空间的存储。
- 监视内存和 CPU 资源，并确保有足够的资源可用于解压缩和解密数据。
- 在使用“即时还原”功能将恢复点挂载为磁盘时，请将 robocopy 与多线程复制选项（/MT 开关）结合使用，以从挂载的恢复点高效地复制文件。 

## <a name="restore-options"></a>还原选项

MARS 代理提供多个还原选项。 每个选项都有独特的优势，使其适用于特定方案。

使用 MARS 代理，可以实现以下功能：

- [还原 Windows Server 系统状态备份](backup-azure-restore-system-state.md)：此选项有助于在 Azure 备份中以文件的形式从某个恢复点还原系统状态，并使用 Windows Server 备份实用程序将这些文件应用到 Windows Server。  
- [还原卷中的所有已备份文件](restore-all-files-volume-mars.md)：此选项将在 Azure 备份从某个恢复点还原某个指定卷中的所有备份数据。 此选项能够加快传输速度（最快为 40 MBPS）。<br>建议在恢复大量数据或整个卷时使用此选项。
- [使用 PowerShell 还原卷中一组特定的已备份文件和文件夹](backup-client-automation.md#restore-data-from-azure-backup)：如果已经知道文件和文件夹相对于卷根目录的路径，则可以使用此选项从恢复点还原一组指定的文件和文件夹，这样比还原整个卷的传输速度更快。 但是，此选项没有使用“即时还原”选项浏览恢复点中的文件和文件夹时的便利性。
- [使用即时还原还原单个文件和文件夹](backup-azure-restore-windows-server.md)：使用此选项，可以通过在恢复点中装载卷作为驱动器来快速访问备份数据。 然后便可以浏览和复制文件和文件夹。 此选项可以实现最高复制速度 6 MBPS，适用于恢复总大小小于 80 GB 的单个文件和文件夹。 复制所需的文件后，你可以卸载恢复点。

## <a name="next-steps"></a>后续步骤

- 有关更多常见问题，请参阅 [MARS 代理常见问题解答](backup-azure-file-folder-backup-faq.yml)。
- 有关支持的方案和限制的详细信息，请参阅 [MARS 代理的备份支持矩阵](backup-support-matrix-mars-agent.md)。