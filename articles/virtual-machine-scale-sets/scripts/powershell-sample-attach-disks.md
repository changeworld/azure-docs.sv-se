---
title: Azure PowerShell-exempel – bifoga och använda datadiskar
description: Det här skriptet skapar en Azure-skalningsuppsättning för virtuella datorer och kopplar och förbereder datadiskar med PowerShell.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: sample
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: bff0bb0673862d614aac5bb783bc501516871b29
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010415"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-powershell"></a>Anslut och använd datadiskar med en VM-skalningsuppsättning med PowerShell
Det här skriptet skapar en VM-skalningsuppsättning och bifogar och förbereder datadiskar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.ps1 "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Skapar VM-skalningsuppsättningen och alla stödresurser, inklusive virtuellt nätverk, lastbalansering och NAT-regler. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Hämtar information om en VM-skalningsuppsättning. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Lägger till ett virtuellt datortillägg för att det anpassade skriptet ska installera ett grundläggande webbprogram. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Uppdaterar modellen för VM-skalningsuppsättningen för att använda det virtuella datortillägget. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för VM-skalningsuppsättningar finns i [dokumentationen för skalningsuppsättningar för virtuella Azure-datorer](../powershell-samples.md).
