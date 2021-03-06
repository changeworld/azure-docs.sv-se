---
title: Om säkerhetskopiering av Azure-fildelning
description: Lär dig hur du säkerhetskopierar Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: fd5bb51b2c7b5c09e9d859b69c3094eb50c205b5
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396269"
---
# <a name="about-azure-file-share-backup"></a>Om säkerhetskopiering av Azure-fildelning

Azure-filresurssäkerhetskopiering är en inbyggd, molnbaserad säkerhetskopieringslösning som skyddar dina data i molnet och eliminerar ytterligare underhållskostnader som ingår i lokala säkerhetskopieringslösningar. Azure Backup-tjänsten integreras smidigt med Azure-filsynkronisering och låter dig centralisera dina fildelningsdata och dina säkerhetskopior. Med den här enkla, tillförlitliga och säkra lösningen kan du konfigurera skyddet för företagets filresurser i några enkla steg med en försäkran om att du kan återställa dina data i händelse av ett katastrofscenario.

## <a name="key-benefits-of-azure-file-share-backup"></a>Viktiga fördelar med säkerhetskopiering av Azure-filresurs

* Noll infrastruktur: Ingen distribution behövs för att konfigurera skydd för dina filresurser.
* Inbyggda hanteringsfunktioner: Du kan schemalägga säkerhetskopior och ange önskad kvarhållningsperiod utan ytterligare information om databeskärning.
* Omedelbar återställning: Azure-filresurssäkerhetskopiering använder ögonblicksbilder av fildelning, så att du kan välja bara de filer som du vill återställa direkt.
* Avisering och rapportering: Du kan konfigurera aviseringar för säkerhetskopiering och återställning av fel och använda rapporteringslösningen som tillhandahålls av Azure Backup för att få insikter om säkerhetskopior över dina filresurser.

## <a name="architecture"></a>Arkitektur

![Azure-säkerhetskopieringsarkitektur för filresurs](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Så här fungerar säkerhetskopieringsprocessen

1. Det första steget i att konfigurera säkerhetskopiering för Azure File-resurser är att skapa ett valv för återställningstjänster. Valvet ger dig en konsoliderad vy över de säkerhetskopior som konfigurerats över olika arbetsbelastningar.

2. När du har skapat ett valv identifierar Azure Backup-tjänsten de lagringskonton som kan registreras med valvet. Du kan välja det lagringskonto som är värd för de filresurser som du vill skydda.

3. När du har valt lagringskontot listar Azure Backup-tjänsten den uppsättning filresurser som finns i lagringskontot och lagrar deras namn i hanteringslagrets katalog.

4. Du konfigurerar sedan säkerhetskopieringsprincipen (schema och kvarhållning) enligt dina krav och väljer de filresurser som ska säkerhetskopieras. Azure Backup-tjänsten registrerar scheman i kontrollplanet för att göra schemalagda säkerhetskopior.

5. Baserat på den angivna principen utlöser Azure Backup-schemaläggaren säkerhetskopieringar vid den schemalagda tiden. Som en del av det jobbet skapas ögonblicksbilden av filresursen med hjälp av API:et för filresurs. Endast url:en för ögonblicksbild lagras i metadataarkivet.

    >[!NOTE]
    >Filresursdata överförs inte till säkerhetskopieringstjänsten eftersom säkerhetskopieringstjänsten skapar och hanterar ögonblicksbilder som ingår i ditt lagringskonto.

6. Du kan återställa Azure-filresursinnehållet (enskilda filer eller hela resursen) från ögonblicksbilder som är tillgängliga på källfilresursen. När åtgärden har utlösts hämtas url:en ögonblicksbild från metadataarkivet och data visas och överförs från källögonblicksbilden till den målfilsresurs du väljer.

7. Säkerhetskopierings- och återställningsjobbövervakningsdata överförs till azure backup monitoring-tjänsten. På så sätt kan du övervaka säkerhetskopieringar i molnet för filresurserna på en enda instrumentpanel. Dessutom kan du också konfigurera aviseringar eller e-postmeddelanden när säkerhetskopieringshälsan påverkas. E-postmeddelanden skickas via Azure-e-posttjänsten.

## <a name="backup-costs"></a>Kostnader för säkerhetskopiering

Azure File share backup är en ögonblicksbild-baserad lösning och lagringsavgifter som uppkommit för ögonblicksbilder faktureras tillsammans med Azure Files Usage enligt prisinformation som nämns [här](https://azure.microsoft.com/pricing/details/storage/files/).

Den skyddade instansavgiften för att utnyttja säkerhetskopieringslösningen är dock enligt den prismodell som beskrivs under avsnittet [Backup For Azure Files.](https://azure.microsoft.com/pricing/details/backup/) För närvarande har det faktiska priset uppdaterats endast för Västra centrala USA. För andra regioner kommer exakta priser att uppdateras snart med vissa regionala variationer, men med samma prismodell.

>[!NOTE]
>Under förhandsversionen finns det ingen avgift för "skyddad instans" och du debiteras endast för ögonblicksbilder enligt den prissättning som nämns [här](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [säkerhetskopierar Azure-filresurser](backup-afs.md)
* Hitta svar [på frågor om säkerhetskopiering av Azure-filer](backup-azure-files-faq.md)
