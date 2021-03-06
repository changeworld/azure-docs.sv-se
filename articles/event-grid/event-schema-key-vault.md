---
title: Azure Event Grid-händelseschema för Azure Key Vault
description: Beskriver de egenskaper och schema som tillhandahålls för Azure Key Vault-händelser med Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: fe186e2ba8f3cafeb4d186066ba65ae036302f70
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010519"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Event Grid-händelseschema för Azure Key Vault (förhandsversion)

Den här artikeln innehåller egenskaper och schema för händelser i [Azure Key Vault](../key-vault/index.yml), för närvarande i förhandsversion. En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Ett Azure Key Vault-konto genererar följande händelsetyper:

| Evenemanget fullständigt namn | Namn på visning av händelser | Beskrivning |
| ---------- | ----------- |---|
| Microsoft.KeyVault.certificateNewVersionSkaperad | Certifikat ny version skapad | Utlöses när ett nytt certifikat eller ny certifikatversion skapas. |
| Microsoft.KeyVault.certificateNearExpiry | Certifikat nära utgångsdatum | Utlöses när den aktuella versionen av certifikatet håller på att upphöra att gälla. (Händelsen utlöses 30 dagar före utgångsdatumet.) |
| Microsoft.KeyVault.certificateExpirerat | Certifikatet har utgått | Utlöses när certifikatet har upphört att gälla. |
| Microsoft.KeyVault.KeyNewVersionSkaperad | Nyckel ny version skapad | Utlöses när en ny nyckel eller ny nyckelversion skapas. |
| Microsoft.KeyVault.KeyNearExpiry | Nyckel nära utgångsdatum | Utlöses när den aktuella versionen av en nyckel håller på att upphöra att gälla. (Händelsen utlöses 30 dagar före utgångsdatumet.) |
| Microsoft.KeyVault.KeyExpirerad | Nyckeln har upphört att gälla | Utlöses när en nyckel har upphört att gälla. |
| Microsoft.KeyVault.SecretNewVersionSkaperad | Hemlig ny version skapad | Utlöses när en ny hemlig eller ny hemlig version skapas. |
| Microsoft.KeyVault.SecretNearExpiry | Hemlighet nära utgångsdatum | Utlöses när den aktuella versionen av en hemlighet håller på att upphöra att gälla. (Händelsen utlöses 30 dagar före utgångsdatumet.) |
| Microsoft.KeyVault.SecretExpirerad | Hemligheten har upphört att gälla | Utlöses när en hemlighet har upphört att gälla. |

## <a name="event-examples"></a>Exempel på evenemang

I följande exempel visas schema för **Microsoft.KeyVault.SecretNewVersionSkaperad:**

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>Händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| ---------- | ----------- |---|
| id | sträng | ID för objektet som utlöste den här händelsen |
| vaultName (valvNamn) | sträng | Nyckelvalvets namn på objektet som utlöste den här händelsen |
| objectType (objekttyp) | sträng | Typen av objekt som utlöste den här händelsen |
| Objektnamn | sträng | Namnet på objektet som utlöste den här händelsen |
| version | sträng | Den version av objektet som utlöste den här händelsen |
| Nbf | nummer | Ej-före-datumet i sekunder sedan 1970-01-01T00:00:00Z för objektet som utlöste den här händelsen |
| exp | nummer | Utgångsdatumet i sekunder sedan 1970-01-01T00:00:00Z för objektet som utlöste den här händelsen |


## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är händelserutnät?](overview.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
* Mer information om Integrering av Key Vault med Event Grid finns i [Övervaka Nyckelvalv med Azure Event Grid (förhandsversion).](../key-vault/event-grid-overview.md)
* En självstudiekurs om Integrering av Key Vault med Event Grid finns i [Ta emot och svara på viktiga valvmeddelanden med Azure Event Grid (förhandsversion).](../key-vault/event-grid-tutorial.md)
* Mer information om hur du får ytterligare vägledning för Key Vault och Azure Automation finns i:
    - [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)
    - [Övervaka nyckelvalv med Azure Event Grid (förhandsversion)](../key-vault/event-grid-overview.md)
    - [Ta emot och svara på viktiga meddelande om nyckelvalv med Azure Event Grid (förhandsversion)](../key-vault/event-grid-tutorial.md)
    - [Översikt över Azure Automation](../automation/index.yml)
