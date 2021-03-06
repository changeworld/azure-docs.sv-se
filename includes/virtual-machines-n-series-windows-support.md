---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135125"
---
## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) drivrutiner

NVIDIA Tesla (CUDA) drivrutiner för NC, NCv2, NCv3, ND och NDv2-serien virtuella datorer (valfritt för NV-serien) stöds endast på de operativsystem som anges i följande tabell. Hämtningslänkar för drivrutiner är aktuella vid publiceringstillfället. De senaste drivrutinerna finns på [NVIDIA:s](https://www.nvidia.com/) webbplats.

> [!TIP]
> Som ett alternativ till manuell CUDA-drivrutinsinstallation på en Virtuell Windows Server kan du distribuera en [virtuell Azure Data Science-datoravbildning.](../articles/machine-learning/data-science-virtual-machine/overview.md) DSVM-utgåvorna för Windows Server 2016 förinstallerar NVIDIA CUDA-drivrutiner, CUDA Deep Neural Network Library och andra verktyg.


| Operativsystem | Drivrutin |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID-drivrutiner

Microsoft omfördelar NVIDIA GRID-drivrutinsinstallatörer för virtuella nv- och NVv3-serie virtuella datorer som används som virtuella arbetsstationer eller för virtuella program. Installera endast dessa GRID-drivrutiner på virtuella azure NV-seriens virtuella datorer, endast på de operativsystem som anges i följande tabell. Dessa drivrutiner inkluderar licensiering för GRID Virtual GPU Software i Azure. Du behöver inte konfigurera en LICENSserver för NVIDIA vGPU-programvara.

Observera att Nvidia-tillägget alltid kommer att installera den senaste drivrutinen. Vi tillhandahåller länkar till den tidigare versionen här för kunder, som har beroende av en äldre version.

För Windows Server 2019, Windows Server 2016 och Windows 10(upp till build 1909):
- [RUTNÄT 10,1 (442,06)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [RUTNÄT 10,0 (441,66)](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

För Windows Server 2012 R2, Windows Server 2008 R2, Windows 8 och Windows 7: 
- [RUTNÄT 10,1 (442,06)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [RUTNÄT 10,0 (441,66)](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (.exe)  
