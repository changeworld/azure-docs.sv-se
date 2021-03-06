---
title: Microsoft Azure-certifieringar för SAP | Microsoft-dokument
description: Uppdaterad lista över aktuella konfigurationer och certifieringar av SAP på Azure-plattformen.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 43fc2e9369b9d11b1ac0205beddea01b633fb633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598382"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-certifieringar och konfigurationer som körs på Microsoft Azure

SAP och Microsoft har en lång historia av att arbeta tillsammans i ett starkt partnerskap som har ömsesidiga fördelar för sina kunder. Microsoft uppdaterar ständigt sin plattform och skickar in nya certifieringsinformation till SAP för att säkerställa att Microsoft Azure är den bästa plattformen för att köra dina SAP-arbetsbelastningar. Följande tabeller beskriver Konfigurationer som stöds av Azure och en lista över växande SAP-certifieringar. 

## <a name="sap-hana-certifications"></a>SAP HANA-certifieringar
Referenser:

- [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) för SAP HANA-stöd för inbyggda virtuella Azure-datorer och STORA HANA-instanser.

| SAP-produkt | Operativsystem som stöds | Azure-erbjudanden |
| --- | --- | --- |
| SAP HANA Developer Edition (inklusive HANA-klientprogramvaran som består av SQLODBC, endast ODBO-Windows, ODBC, JDBC-drivrutiner, HANA-studio och HANA-databas) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Vm-familj i D-serien |
| Business One på HANA | SUSE Linux Företag | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Kontrollerad tillgänglighet för GS5. Fullt stöd för M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA på Azure (Stora instanser) [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Svit på HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA på Azure (Stora instanser) [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise för BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA på Azure (Stora instanser) [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA på Azure (Stora instanser) <br /> [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Tänk på att SAP använder termen "klustring" i [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) som synonym för "utskalning" och INTE för hög tillgänglighet "klustring"

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-certifieringar
Microsoft Azure är certifierat för följande SAP-produkter, med fullständig support från Microsoft och SAP.
Referenser:

- [1928533 - SAP-program på Azure: Produkter som stöds och Azure VM-typer](https://launchpad.support.sap.com/#/notes/1928533) för alla SAP NetWeaver-baserade program, inklusive SAP TREX, SAP LiveCache och SAP Content Server. Och alla databaser, utom SAP HANA.


| SAP-produkt | Gästoperativsystem | RDBMS | Typer av virtuella datorer |
| --- | --- | --- | --- |
| Programvara för SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (endast Windows och Oracle Linux), DB2, SAP ASE |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3, D2as_v4 till D64as_v4, E2s_v3 till E64s_v3, E2as_v4 till E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |
| Sap Business allt-i-ett | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (endast Windows och Oracle Linux), DB2, SAP ASE |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3, D2as_v4 till D64as_v4, E2s_v3 till E64s_v3, E2as_v4 till E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP BusinessObjects BI | Windows |Ej tillämpligt |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3, D2as_v4 till D64as_v4, E2s_v3 till E64s_v3, E2as_v4 till E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (endast Windows och Oracle Linux), DB2, SAP ASE |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3, D2as_v4 till D64as_v4, E2s_v3 till E64s_v3, E2as_v4 till E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Annan SAP-arbetsbelastning som stöds på Azure

| SAP-produkt | Gästoperativsystem | RDBMS | Typer av virtuella datorer |
| --- | --- | --- | --- |
| SAP Business One på SQL Server | Windows  | SQL Server | Alla NetWeaver-certifierade VM-typer<br /> [SAP-#928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows och Linux | | Alla NetWeaver-certifierade VM-typer<br /> SAP-#2451795 |
| SAP Business Objects BI-plattform | Windows och Linux | | SAP-#2145537 |
| SAP-datatjänster 4.2 | | | SAP-#2288344 |
| SAP Hybris Commerce Platform 5.x och 6.x | Windows | SQL Server, Oracle | Alla NetWeaver-certifierade VM-typer<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
