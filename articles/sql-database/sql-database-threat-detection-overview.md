---
title: Advanced Threat Protection
description: Avancerat skydd mot hot identifierar avvikande databasaktiviteter som anger potentiella säkerhetshot i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 17ca8cbb7a55e9c0d44af099f4884f71b1cd457a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124765"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection för Azure SQL Database

Avancerat skydd mot hot för [Azure SQL Database](sql-database-technical-overview.md) och Azure [Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Advanced Threat Protection är en del av [ADS-erbjudandet (Advanced Data Security),](sql-database-advanced-data-security.md) som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Advanced Threat Protection kan nås och hanteras via den centrala SQL ADS-portalen.

> [!NOTE]
> Det här avsnittet gäller Azure SQL-server och både SQL Database och Azure Synapse som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när du refererar till både SQL Database och Azure Synapse.

## <a name="what-is-advanced-threat-protection"></a>Vad är avancerat hotskydd

 Avancerat skydd mot hot ger ett nytt säkerhetslager som gör det möjligt för kunder att identifiera och reagera på potentiella hot när de uppstår genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användare får en avisering om misstänkta databasaktiviteter, potentiella sårbarheter och SQL-injektionsattacker, samt avvikande databasåtkomst och frågorsmönster. Avancerat skydd mot hot integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder för hur du undersöker och minskar hotet. Avancerat skydd mot hot gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera avancerade säkerhetsövervakningssystem.

För en fullständig undersökningsupplevelse rekommenderas att aktivera [SQL Database Auditing](sql-database-auditing.md), som skriver databashändelser till en granskningslogg i ditt Azure-lagringskonto.  

## <a name="advanced-threat-protection-alerts"></a>Varningar för avancerat skydd mot hot

Avancerat skydd mot hot för Azure SQL Database identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och kan utlösa följande aviseringar:

- **Säkerhetsproblem för SQL-injektion:** Den här aviseringen utlöses när ett program genererar ett felaktigt SQL-uttryck i databasen. Aviseringen kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Det finns två möjliga orsaker till att en felaktig instruktion genereras:

  - Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
  - Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
- **Potential SQL injection** (Potentiell SQL-inmatning): Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.
- **Access from unusual location** (Åtkomst från ovanlig plats): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from unusual Azure data center** (Åtkomst från ovanligt Azure-datacenter): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern från ett ovanligt Azure-datacenter som nyligen setts på den här servern. I vissa fall identifierar aviseringen en giltig åtgärd (som ditt nya program i Azure, Power BI eller Azure SQL Query Editor). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Access from unfamiliar principal** (Åtkomst från ovanligt huvudkonto): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern med ett ovanligt huvudkonto (SQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force SQL credentials** (Nyckelsökningsattack mot SQL-autentiseringsuppgifter): Den här aviseringen utlöses när det sker ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Utforska avvikande databasaktiviteter vid identifiering av en misstänkt händelse

Du får ett e-postmeddelande när du har upptäckt avvikande databasaktiviteter. E-postmeddelandet innehåller information om den misstänkta säkerhetshändelsen, inklusive arten av avvikande aktiviteter, databasnamn, servernamn, programnamn och händelsetid. Dessutom innehåller e-postmeddelandet information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minska det potentiella hotet mot databasen.

![Avvikelseaktivitetsrapport](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Klicka på länken **Visa senaste SQL-aviseringar** i e-postmeddelandet för att starta Azure-portalen och visa sidan Azure Security Center-aviseringar, som ger en översikt över aktiva hot som upptäckts i SQL-databasen.

   ![Aktivitetshot](./media/sql-database-threat-detection/active_threats.png)

2. Klicka på en specifik avisering för att få ytterligare information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.

   SQL-injektion är till exempel ett av de vanligaste säkerhetsproblemen för webbprogram på Internet som används för att angripa datadrivna program. Angripare utnyttjar programsäkerhetsproblem för att injicera skadliga SQL-satser i programinmatningsfält, bryta mot eller ändra data i databasen. För SQL Injection-aviseringar innehåller aviseringens information det sårbara SQL-uttrycket som utnyttjades.

   ![Specifik varning](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Utforska avancerade hotskyddsaviseringar för din databas i Azure-portalen

Avancerat skydd mot hot integrerar sina aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). Live SQL Advanced Threat Protection-paneler i databasen och SQL ADS-bladen i Azure-portalen spårar status för aktiva hot.

Klicka på **Avancerad threat protection-avisering** om du vill starta sidan Azure Security Center-aviseringar och få en översikt över aktiva SQL-hot som identifierats i databasen.

   ![Avancerad varning om skydd mot hot](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Avancerad varning för skydd mot hot2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [avancerat skydd mot hot i enstaka och poolade databaser](sql-database-threat-detection.md).
- Läs mer om [avancerat skydd mot hot i hanterad instans](sql-database-managed-instance-threat-detection.md).
- Läs mer om [avancerad datasäkerhet](sql-database-advanced-data-security.md).
- Läs mer om [Granskning av Azure SQL Database](sql-database-auditing.md)
- Läs mer om [Azure security center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om prissättning finns på [prissidan för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
