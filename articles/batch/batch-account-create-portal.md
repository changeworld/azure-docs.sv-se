---
title: Skapa ett konto i Azure-portalen – Azure Batch | Microsoft Docs
description: Lär dig hur du skapar ett Azure Batch-konto på Azure-portalen för att köra storskaliga parallella arbetsbelastningar i molnet
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3723631609a04f6d12abcaac1f9d7733bf3caa01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247649"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Skapa ett Batch-konto med Azure Portal

Lär dig hur du skapar ett Azure Batch-konto på [Azure Portal][azure_portal] och välj de kontoegenskaper som passar ditt beräkningsscenario. Lär dig var du hittar viktiga kontoegenskaper som snabbtangenter och konto-URL:er.

Bakgrundsinformation om Batch-konton och Batch-scenarier finns i [funktionsöversikten](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Logga in på [Azure-portalen][azure_portal].

1. Välj Skapa en**resursberäkningsbatchtjänst** > **Batch Service** **Create a resource** > .

    ![Batch på Marketplace][marketplace_portal]

1. Ange inställningar för **Nytt Batch-konto**. Se följande information.

    ![Skapa ett Batch-konto][account_portal]

    a. **Prenumeration**: Prenumerationen som Batch-kontot skapas i. Om du bara har en prenumeration väljs den som standard.

    b. **Resursgrupp**: Välj en befintlig resursgrupp för ditt nya Batch-konto. Du kan också skapa en ny resursgrupp.

    c. **Kontonamn**: Det namn du väljer måste vara unikt i den Azure-region där kontot skapas (se **Plats** nedan). Kontonamnet får bara innehålla gemener eller siffror och måste vara mellan 3 och 24 tecken långt.

    d. **Plats**: Azure-regionen som Batch-kontot skapas i. Endast de regioner som stöds av din prenumeration och resursgrupp visas som alternativ.

    e. **Lagringskonto**: Ett valfritt Azure Storage-konto som du associerar med ditt batchkonto. Ett v2-lagringskonto för generell användning rekommenderas för bästa prestanda. Samtliga alternativ för lagringskonton i batch finns i [översikten över Batch-funktionen](batch-api-basics.md#azure-storage-account). I portalen väljer du ett befintligt lagringskonto eller skapar ett nytt.

      ![Skapa ett lagringskonto][storage_account]

    f. **Poolallokeringsläge:** På fliken **Avancerade** inställningar kan du ange poolallokeringsläge som **batchtjänst** eller **användarprenumeration**. För de flesta scenarier kan du acceptera standardinställningarna för **Batch-tjänsten**.

      ![Batch-poolallokeringsläge][pool_allocation]

1. Skapa kontot genom att klicka på **Skapa**.

## <a name="view-batch-account-properties"></a>Visa egenskaper för ett Batch-konto

När kontot har skapats kan du välja kontot för att komma åt dess inställningar och egenskaper. Du kan komma åt alla kontoinställningar och kontoegenskaper från den vänstra menyn.

![Sidan för Batch-kontot på Azure Portal][account_blade]

* **Batch-kontonamn, URL och nycklar**: När du utvecklar ett program med [Batch-API:erna](batch-apis-tools.md#azure-accounts-for-batch-development) behöver du en konto-URL och nyckel för att komma åt Batch-resurserna. (Batch har även stöd för Azure Active Directory-autentisering.)

    Om du vill visa åtkomstinformationen för Batch-kontot väljer du **Nycklar**.

    ![Batch-kontonycklar på Azure Portal][account_keys]

* Om du vill visa namnet på och nycklarna för lagringskontot som är associerat med Batch-kontot väljer du **Lagringskonto**.

* Om du vill visa resurskvoterna som gäller för Batch-kontot väljer du **Kvoter**. Mer information finns i [Kvoter och begränsningar för Batch-tjänsten](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Ytterligare konfiguration för läget Användarprenumeration

Om du väljer att skapa ett Batch-konto i läget Användarprenumeration ska du utföra följande åtgärder innan du skapar kontot.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Tillåt att Azure Batch får åtkomst till prenumerationen (engångsåtgärd)

När du skapar ditt första Batch-konto i användarprenumerationsläge behöver du registrera prenumerationen med Batch. (Om du redan har gjort detta går du vidare till nästa avsnitt.)

1. Logga in på [Azure-portalen][azure_portal].

1. Välj **Alla tjänster** > **Prenumerationer**och välj den prenumeration som du vill använda för batchkontot.

1. På sidan **Prenumeration** väljer du **resursproviders** och sök efter **Microsoft.Batch**. Kontrollera att resursprovidern **Microsoft.Batch** har registrerats i prenumerationen. Om den inte är registrerad väljer du länken **Registrera**.

    ![Registrera providern Microsoft.Batch][register_provider]

1. På sidan **Prenumeration** väljer du **Åtkomstkontroll (IAM)** > **Rolltilldelningar** > **Lägg till rolltilldelning**.

    ![Åtkomstkontroll för prenumeration][subscription_access]

1. På sidan **Lägg till rolltilldelning** väljer du rollen **Deltagare** och söker efter Batch-API:t. Sök efter var och en av de här strängarna tills du hittar API:t:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Nyare Azure AD-klientorganisationer kan använda det här namnet.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** är id:t för API:t.

1. När du har hittat Batch-API:t markerar du det och klickar på **Spara**.

    ![Lägg till Batch-behörigheter][add_permission]

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

I användarprenumerationsläge krävs ett Azure-nyckelvalv som tillhör samma resursgrupp som Batch-kontot som ska skapas. Kontrollera att resursgruppen finns i en region där Batch är [tillgängligt](https://azure.microsoft.com/regions/services/) och som din prenumeration stöder.

1. På [Azure Portal][azure_portal] väljer du **Nytt** > **Säkerhet** > **Key Vault**.

1. På sidan **Skapa nyckelvalv** anger du ett namn för nyckelvalvet och skapar en resursgrupp i den region som du vill använda för ditt Batch-konto. Lämna standardvärdena för resten av inställningarna och väljer sedan **Skapa**.

När du skapar batchkontot i användarprenumerationsläge använder du resursgruppen för nyckelvalvet. Ange **Användarprenumeration** som poolallokeringsläge, markera nyckelvalvet och markera kryssrutan för att bevilja Azure Batch-åtkomst till nyckelvalvet. 

Om du föredrar att bevilja åtkomst till nyckelvalvet manuellt går du till avsnittet **Åtkomstprinciper** i nyckelvalvet och väljer **Lägg till åtkomstprincip** och söker efter **Microsoft Azure Batch**. När du har valt måste du konfigurera de **hemliga behörigheterna** med hjälp av den nedrullningsna menyn. Azure Batch måste få minst **behörigheter hämta**, **lista**, **ange**och **ta bort.**

![Hemliga behörigheter för Azure Batch](./media/batch-account-create-portal/secret-permissions.png)


> [!NOTE]
> Kontrollera att **kryssrutorna Azure Virtual Machines for deployment** och Azure Resource Manager för **malldistribution** väljs under **Åtkomstprinciper** för den länkade **Key Vault-resursen.**
> 
> ![Obligatorisk åtkomstprincip](./media/batch-account-create-portal/key-vault-access-policy.png) för nyckelvalv Detta är inte obligatoriskt när du skapar ett batchkonto i Azure-portalen. Alternativet är markerat som standard.



### <a name="configure-subscription-quotas"></a>Konfigurera prenumerationskvoter

Kärnkvoter anges inte som standard på Batch-konton för användarprenumerationer. Kärnkvoter måste anges manuellt eftersom standardmässiga Batch-kärnkvoter inte gäller för konton i läget för användarprenumeration.

1. I [Azure-portalen][azure_portal] väljer du ditt Batch-konto för läget för användarprenumeration för att visa dess inställningar och egenskaper.

1. På den vänstra menyn väljer du **Kvoter** för att visa och konfigurera de kärnkvoter som är associerade med ditt Batch-konto.

Se [Kvoter och begränsningar för Batch-tjänsten](batch-quota-limit.md) för mer information om kärnkvoter i läget för användarprenumeration.

## <a name="other-batch-account-management-options"></a>Andra alternativ för Batch-kontohantering

Förutom att använda Azure Portal kan du skapa och hantera Batch-konton med verktyg som följande:

* [PowerShell-cmdlets för Batch](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Nästa steg

* Mer information om begrepp och funktioner relaterade till Batch-tjänsten finns i [funktionsöversikten för Batch](batch-api-basics.md). Artikeln beskriver de viktigaste Batch-resurserna, t.ex. pooler, beräkningsnoder, jobb och aktiviteter, och innehåller en översikt över funktioner i tjänsten för storskaliga arbetsbelastningar.
* Lär dig hur du utvecklar ett enkelt Batch-aktiverat program med hjälp av [Batch .NET-klientbiblioteket](quick-run-dotnet.md) eller [Python](quick-run-python.md). I de här snabbstarterna beskriver vi ett exempelprogram som använder Batch-tjänsten för att köra en arbetsbelastning på flera beräkningsnoder och förklarar hur du använder Azure Storage för mellanlagring och hämtning av filer i arbetsbelastningar.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
