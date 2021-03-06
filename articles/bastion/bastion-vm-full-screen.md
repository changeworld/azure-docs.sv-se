---
title: 'Azure Bastion: Visa session för virtuella datorer: helskärm'
description: I den här artikeln får du lära dig hur du ändrar vyn till helskärm.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 2e72eb9ffc3affa0bf09692ac0780f9310308521
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619295"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>Ändra till helskärmsläge för en vm-session: Azure Bastion

Den här artikeln hjälper dig att ändra vyn för den virtuella datorn till helskärm och tillbaka i webbläsaren. Innan du arbetar med en virtuell dator kontrollerar du att du har följt stegen för att [skapa en bastionvärd](bastion-create-host-portal.md). Anslut sedan till den virtuella datorn som du vill arbeta med med hjälp av [rdp](bastion-connect-vm-rdp.md) eller [SSH](bastion-connect-vm-ssh.md).

## <a name="launch-the-clipboard-tool"></a>Starta urklippsverktyget

Under fjärrsessionen startar du verktygspaletten för åtkomstverktyg för Bastion genom att välja de två pilarna som finns till vänster i mitten av sessionen.

![verktyg](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Välj helskärm

Välj **helskärmsknappen** om du vill växla sessionen till en helskärmsupplevelse. När du har växlat kommer sessionen att initieras igen till helskärm.

![helskärm](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Nästa steg

Läs [Vanliga frågor om Bastion](bastion-faq.md).
Lär dig hur du [kopierar och klistrar in](bastion-vm-copy-paste.md) till och från en virtuell Azure-dator.
