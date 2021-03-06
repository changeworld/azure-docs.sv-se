---
title: Massåterställning borttagna användare (förhandsversion) i Azure Active Directory-portalen | Microsoft-dokument
description: Återställa borttagna användare i grupp i Azure AD-administrationscentret i Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174256"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Massåterställning borttagna användare (förhandsversion) i Azure Active Directory

Azure Active Directory (Azure AD) stöder massanvändare skapa och ta bort åtgärder, massinbjudan för gäster och stöder nedladdning av listor över användare, grupper och gruppmedlemmar.

## <a name="to-bulk-restore-users"></a>Till massåterställning användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett konto som är användaradministratör i Azure AD-organisationen.
1. I Azure AD väljer du **Användare** > **som har tagits bort**.
1. På sidan **Borttagna användare** väljer du **Massåterställning** för att ladda upp en giltig CSV-fil med egenskaper för användarna som ska återställas.

   ![Markera kommandot massåterställning på sidan Borttagna användare](./media/users-bulk-restore/bulk-restore.png)

1. Öppna CSV-filen och lägg till en rad för varje användare som du vill återställa. Det enda värde som krävs är **ObjectID**. Spara sedan filen.

   ![Välj en lokal CSV-fil där du listar de användare du vill lägga till](./media/users-bulk-restore/upload-button.png)

1. Bläddra till filen under Ladda **upp csv-filen**på sidan **Massåterställning (förhandsgranskning)** . När du markerar filen och klickar på **Skicka**startar valideringen av CSV-filen.
1. När filinnehållet har **validerats visas Filen uppladdad**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När filen godkänns väljer du **Skicka** för att starta azure-massåtgärden som återställer användarna.
1. När återställningen är klar visas ett meddelande om att massåtgärden lyckades.

Om det finns fel kan du hämta och visa resultatfilen på sidan **Massåtgärdsresultat.** Filen innehåller orsaken till varje fel.

## <a name="check-status"></a>Kontrollera status

Du kan se status för alla väntande massbegäranden på sidan **Massåtgärdsresultat (förhandsversion).**

   ![Kontrollera uppladdningsstatus på sidan Resultat för massåtgärder](./media/users-bulk-restore/bulk-center.png)

Därefter kan du kontrollera att de användare som du har återställt finns i Azure AD-organisationen antingen i Azure-portalen eller med PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Visa återställda användare i Azure-portalen

1. [Logga in på Azure AD-administrationscentret](https://aad.portal.azure.com) med ett konto som är användaradministratör i organisationen.
1. Välj Azure Active **Directory**i navigeringsfönstret .
1. Under **Hantera** väljer du **Användare**.
1. Under **Visa**väljer du **Alla användare** och kontrollerar att de användare som du har återställt visas.

### <a name="view-users-with-powershell"></a>Visa användare med PowerShell

Kör följande kommando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Du bör se att de användare som du har återställt visas.

## <a name="next-steps"></a>Nästa steg

- [Användare av massimport](users-bulk-add.md)
- [Massborttagning användare](users-bulk-delete.md)
- [Ladda ner lista över användare](users-bulk-download.md)
