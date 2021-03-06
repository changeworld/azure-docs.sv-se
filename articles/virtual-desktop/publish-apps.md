---
title: Publicera inbyggda appar i Windows Virtual Desktop - Azure
description: Så här publicerar du inbyggda appar i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127751"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publicera inbyggda appar i Windows Virtual Desktop

I den här artikeln får du veta hur du publicerar appar i windows virtual desktop-miljö.

## <a name="publish-built-in-apps"></a>Publicera inbyggda appar

Så här publicerar du en inbyggd app:

1. Anslut till en av de virtuella datorerna i värdpoolen.
2. Hämta **PackageFamilyName** för den app som du vill publicera genom att följa instruktionerna i den [här artikeln](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Kör slutligen följande cmdlet `<PackageFamilyName>` med ersatt av **PackageFamilyName** som du hittade i föregående steg:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows Virtual Desktop stöder endast publiceringsappar med installationsplatser som börjar med `C:\Program Files\Windows Apps`.

## <a name="update-app-icons"></a>Uppdatera appikoner

När du har publicerat en app har den standardikonen för Windows-appar i stället för den vanliga ikonbilden. Om du vill ändra ikonen till den vanliga ikonen placerar du bilden av ikonen du vill ha på en nätverksresurs. Bildformat som stöds är PNG, BMP, GIF, JPG, JPEG och ICO.

## <a name="publish-microsoft-edge"></a>Publicera Microsoft Edge

Den process du använder för att publicera Microsoft Edge skiljer sig lite från publiceringsprocessen för andra appar. Om du vill publicera Microsoft Edge med standardstartsidan kör du den här cmdleten:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du konfigurerar feeds för att ordna hur appar visas för användare i [Anpassa flödet för Windows Virtual Desktop-användare](customize-feed-for-virtual-desktop-users.md).
- Lär dig mer om funktionen för bifoga msix-app på [Konfigurera MSIX-apprenad](app-attach.md).

