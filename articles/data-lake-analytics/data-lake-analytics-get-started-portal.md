---
title: Skapa & fråga Azure Data Lake Analytics - Azure-portal
description: Använd Azure Portal till att skapa ett Azure Data Lake Analytics-konto och skicka ett U-SQL-jobb.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 46da3750e4d0ac78c5fd9df91ae37670e541302d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315763"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Kom igång med Azure Data Lake Analytics med hjälp av Azure Portal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Den här artikeln beskriver hur du använder Azure Portal till att skapa Azure Data Lake Analytics-konton, definiera jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-tjänsten.

## <a name="prerequisites"></a>Krav

Innan du börjar följa de här självstudierna måste du ha en **Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto

Nu ska du skapa ett Data Lake Analytics och ett Azure Data Lake Storage Gen1-konto samtidigt.  Det här steget är enkelt och tar bara ungefär 60 sekunder att slutföra.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Skapa en resursData** >  **+ Analytics** > **DataSjöanalys**.
3. Välj värden för följande objekt:
   * **Namn**: Ange ett namn på ditt Data Lake Analytics-konto (endast gemena bokstäver och siffror tillåts).
   * **Prenumeration**: Välj den Azure-prenumeration som används för Analytics-kontot.
   * **Resursgrupp**. Välj en befintlig Azure-resursgrupp eller skapa en ny.
   * **Plats**. Välj ett Azure-datacenter för Data Lake Analytics-kontot.
   * **Data Lake Storage Gen1**: Följ instruktionerna för att skapa ett nytt Data Lake Storage Gen1-konto, eller välj ett befintligt. 
4. Alternativt,kan du välja en prisnivå för ditt Data Lake Analytics-konto.
5. Klicka på **Skapa**. 


## <a name="your-first-u-sql-script"></a>Skriv ditt första U-SQL-skript

Följande text är ett enkelt U-SQL-skript. Allt den gör är att definiera en liten datauppsättning i skriptet och sedan skriva att datauppsättning `/data.csv`ut till standard Data Lake Storage Gen1 konto som en fil som heter .

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>Skicka ett U-SQL-jobb

1. Välj **Nytt jobb** från Data Lake Analytics-kontot.
2. Klistra in texten från U-SQL-skriptet ovan. Ge jobbet ett namn. 
3. Starta jobbet genom att välja **Skicka**.   
4. Övervaka jobbets **status** och vänta tills statusen ändras till **Lyckades**.
5. Välj fliken **Data** och välj sedan fliken **Utdata.** `data.csv`

## <a name="see-also"></a>Se även

* Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med Azure-portalen](data-lake-analytics-manage-use-portal.md).
