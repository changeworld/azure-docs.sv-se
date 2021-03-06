---
title: Så här tillämpas reservationsrabatter för Azure SQL Data Warehouse | Microsoft Docs
description: Läs om hur reservationsrabatter tillämpas för Azure SQL Data Warehouse så att du kan spara pengar.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b08f11799c1471af22138fefcd57ed1eb951a1a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199389"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Så här tillämpas reservationsrabatter för Azure SQL Data Warehouse

När du har köpt reserverad kapacitet för Azure SQL Data Warehouse tillämpas reservationsrabatten automatiskt på informationslager i den aktuella regionen. Reservationsrabatten gäller den användning som genereras av cDWU-mätaren för SQL Data Warehouse. Lagring och nätverk debiteras enligt Betala per användning-priser.

## <a name="reservation-discount-application"></a>Tillämpning av reservationsrabatter

Rabatten för reserverad SQL Data Warehouse-kapacitet tillämpas per timme för informationslager som körs. Om du inte har något informationslager distribuerat under en timme så går den reserverade kapaciteten för timmen förlorad. Den överförs inte.

Efter köpet matchas den reservation du har köpt mot SQL Data Warehouse-användningen som genereras av de informationslager som körs vid varje given tidpunkt. Om du stänger av ett informationslager tillämpas reservationsrabatten automatiskt på övriga matchande informationslager.

För informationslager som inte körs under hela timmen tillämpas reservationen automatiskt på andra matchande instanser under timmen.

## <a name="discount-examples"></a>Exempel på rabatter

I följande exempel visas hur rabatten för reserverad SQL Data Warehouse-kapacitet tillämpas beroende på distributionen.

- **Exempel 1**: Du köper 5 enheter om 100 cDWU reserverad kapacitet. Du kör en DW1500c SQL Data Warehouse-instans under en timme. I det här fallet genereras en användning på 15 enheter om 100 cDWU. Reservationsrabatten gäller de 5 enheter du använde. Du debiteras enligt Betala per användning-priser för de återstående 10 enheterna om 100 cDWU som du använde. Med andra ord är delvis täckning möjlig för flera reservationer.

- **Exempel 2**: Du köper 5 enheter om 100 cDWU reserverad kapacitet. Du kör två DW100c SQL Data Warehouse-instanser under en timme. I det här fallet genereras två användningshändelser på 1 enhet om 100 cDWU. Båda användningshändelserna erhåller rabatt för reserverad kapacitet. Återstående 3 enheter om 100 cDWU reserverad kapacitet går förlorade och kan inte användas för framtida användning. Med andra ord kan en enda reservation matchas mot flera SQL Data Warehouse-instanser.

- **Exempel 3**: Du köper 1 enhet om 100 cDWU reserverad kapacitet. Du kör två DW100c SQL Data Warehouse-instanser. Varje instans körs i 30 minuter. I det här fallet erhåller båda användningshändelserna rabatt för reserverad kapacitet. Ingen användning debiteras enligt Betala per användning-priser.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

- Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
- [Visa reservationstransaktioner](view-reservations.md)
- [Hämta reservationstransaktioner och användning via API:et](reservation-apis.md)
- [Hantera reservationer](manage-reserved-vm-instance.md)
