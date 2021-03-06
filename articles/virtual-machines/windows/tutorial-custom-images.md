---
title: Självstudiekurs - Skapa anpassade VM-avbildningar med Azure PowerShell
description: I den här självstudiekursen lär du dig hur du skapar en anpassad avbildning av en virtuell dator i Azure med hjälp av Azure PowerShell
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7360798f2f95184145a856babf501e3080cbaaf4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76274192"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Självstudier: Skapa en anpassad avbildning av en virtuell dator i Azure med hjälp av Azure PowerShell

Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för att starta distributioner och säkerställa konsekvens mellan flera virtuella datorer. I den här självstudien skapar du en egen anpassad avbildning av en virtuell Azure-dator med hjälp av PowerShell. Lär dig att:

> [!div class="checklist"]
> * Förbereda systemet med Sysprep och generalisera virtuella datorer
> * Skapa en anpassad avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Göra en lista med alla avbildningar i din prenumeration
> * Ta bort en avbildning

I offentlig förhandsversion har vi Azure [VM Image Builder-tjänsten.](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview) Beskriv bara dina anpassningar i en mall och hantera stegen för att skapa bilder i den här artikeln. [Prova Azure Image Builder (förhandsgranskning)](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder).

## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan visar hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan använda för att skapa nya VM-instanser.

Du måste ha en befintlig virtuell dator för att kunna utföra exemplet i den här självstudiekursen. Om det behövs kan det här [skriptexemplet](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) skapa ett åt dig. När du använder självstudien ersätter du namn på resursgrupp och VM där det behövs.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/powershell](https://shell.azure.com/powershell)en separat webbläsarflik genom att gå till . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="prepare-vm"></a>Förbereda den virtuella datorn

För att skapa en avbildning av en virtuell dator behöver du förbereda den virtuella källdatorn genom att generalisera, frigöra och sedan markera den som generaliserad med Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalisera den virtuella Windows-datorn med hjälp av Sysprep

Sysprep tar bland annat bort all din personliga kontoinformation och förbereder datorn så att den kan användas som en avbildning. Mer information om Sysprep finns i [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Använda Sysprep: En introduktion).


1. Ansluta till den virtuella datorn.
2. Öppna Kommandotolken som administratör. Ändra katalogen till *%windir%\system32\sysprep* och kör sedan `sysprep.exe`.
3. Välj **Starta OOBE för systemet (Out-of-Box Experience)** i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan **Generalisera** är markerad.
4. Välj **Avstängning** i **Avstängningsalternativ** och klicka på **OK**.
5. När Sysprep har slutförts stängs den virtuella datorn av. **Starta inte om den virtuella datorn**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Frigöra och markera den virtuella datorn som generaliserad

När du skapar en avbildning måste den virtuella datorn frigöras och markeras som generaliserad i Azure.

Deallocate den virtuella datorn med [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Ange statusen för den virtuella datorn till `-Generalized` med hjälp av [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Skapa avbildningen

Nu kan du skapa en avbildning av den virtuella datorn med hjälp av [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) och [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). I följande exempel skapas en avbildning med namnet *myImage* från en virtuell dator med namnet *myVM*.

Hämta den virtuella datorn. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Skapa avbildningskonfigurationen.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Skapa avbildningen.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Skapa virtuella datorer från avbildningen

Nu när du har en avbildning kan du skapa en eller flera nya virtuella datorer från avbildningen. Att skapa en virtuell dator från en anpassad avbildning påminner om att skapa en virtuell dator med hjälp av en Marketplace-avbildning. När du använder en Marketplace-avbildning måste du ange information om avbildningen, avbildningsprovidern, produkten, SKU och version. Med den förenklade parameteruppsättningen för cmdleten [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) behöver du i stället bara ange namnet på den anpassade avbildningen, förutsatt att den finns i samma resursgrupp. 

I det här exemplet skapas en virtuell dator med namnet *myVMfromImage* från avbildningen *myImage* i *myResourceGroup*.


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

Vi rekommenderar att du begränsar antalet samtidiga distributioner till 20 virtuella datorer från en enda avbildning. Om du planerar storskaliga samtidiga distributioner av över 20 virtuella datorer från samma anpassade avbildning bör du använda ett [delat bildgalleri](shared-image-galleries.md) med flera bildrepliker. 


## <a name="image-management"></a>Avbildningshantering 

Här följer några exempel på vanliga uppgifter för hanterade avbildningar samt anvisningar som beskriver hur du utför dem med hjälp av PowerShell.

Visa alla avbildningar efter namn.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Ta bort en avbildning. I det här exemplet tar vi bort avbildningen med namnet *myImage* från *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I självstudien skapade du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Förbereda systemet med Sysprep och generalisera virtuella datorer
> * Skapa en anpassad avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Göra en lista med alla avbildningar i din prenumeration
> * Ta bort en avbildning

Gå vidare till nästa handledning för att lära dig mer om hur du skapar virtuella datorer med hög tillgång.

> [!div class="nextstepaction"]
> [Skapa virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md)



