---
title: Vägledning för Begränsning av Azure Key Vault
description: Begränsning av nyckelvalv begränsar antalet samtidiga anrop för att förhindra överanvändning av resurser.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c4923e86f8678458d6301503043413fb8a5629b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197376"
---
# <a name="azure-key-vault-throttling-guidance"></a>Vägledning för Begränsning av Azure Key Vault

Begränsning är en process som du initierar som begränsar antalet samtidiga anrop till Azure-tjänsten för att förhindra överanvändning av resurser. Azure Key Vault (AKV) är utformat för att hantera en hög mängd begäranden. Om ett överväldigande antal begäranden inträffar bidrar begränsning av klientens begäranden till att upprätthålla optimal prestanda och tillförlitlighet för AKV-tjänsten.

Begränsningsgränserna varierar beroende på scenariot. Om du till exempel utför en stor mängd skrivningar är möjligheten för begränsning högre än om du bara utför läsningar.

## <a name="how-does-key-vault-handle-its-limits"></a>Hur hanterar Key Vault sina gränser?

Servicegränser i Key Vault förhindrar missbruk av resurser och säkerställer servicekvalitet för alla Key Vaults klienter. När ett tjänsttröskel överskrids begränsar Key Vault alla ytterligare begäranden från den klienten under en viss tid, returnerar HTTP-statuskod 429 (för många begäranden) och begäran misslyckas. Misslyckade begäranden som returnerar ett antal 429 mot begränsningsgränserna som spåras av Key Vault. 

Key Vault var ursprungligen avsedd att användas för att lagra och hämta dina hemligheter vid distributionen.  Världen har utvecklats, och Key Vault används vid körning för att lagra och hämta hemligheter, och ofta appar och tjänster vill använda Key Vault som en databas.  Aktuella gränser stöder inte höga dataflödeshastigheter.

Key Vault skapades ursprungligen med de gränser som anges i [Azure Key Vault-tjänstgränser](key-vault-service-limits.md).  För att maximera din Key Vault genom put priser, här är några rekommenderade riktlinjer / metodtips för att maximera ditt dataflöde:
1. Se till att du har strypning på plats.  Kunden måste hedra exponentiell back-off politik för 429-er och se till att du gör återförsök enligt vägledningen nedan.
1. Dela upp trafiken i Key Vault mellan flera valv och olika regioner.   Använd ett separat valv för varje säkerhets-/tillgänglighetsdomän.   Om du har fem appar, var och en i två regioner, rekommenderar vi 10 valv vardera som innehåller hemligheter som är unika för appen och regionen.  En gräns för hela prenumerationen för alla transaktionstyper är fem gånger den enskilda nyckelvalvsgränsen. Till exempel är HSM-andra transaktioner per prenumeration begränsade till 5 000 transaktioner på 10 sekunder per prenumeration. Överväg att cachelagra hemligheten i din tjänst eller app för att också minska RPS direkt till nyckelvalv och / eller hantera burst-baserad trafik.  Du kan också dela upp trafiken mellan olika regioner för att minimera svarstiden och använda en annan prenumeration/valv.  Skicka inte mer än prenumerationsgränsen till Key Vault-tjänsten i en enda Azure-region.
1. Cacheminnet du hämtar från Azure Key Vault i minnet och återanvänd från minnet när det är möjligt.  Läs om från Azure Key Vault endast när den cachelagrade kopian slutar fungera (t.ex. eftersom den roterades vid källan). 
1. Key Vault är utformad för dina egna tjänster hemligheter.   Om du lagrar dina kunders hemligheter (särskilt för viktiga lagringsscenarier med hög dataflöde) kan du överväga att placera nycklarna i en databas eller ett lagringskonto med kryptering och lagra bara huvudnyckeln i Azure Key Vault.
1. Kryptera, radbryt och verifiera åtgärder för offentliga nyckel kan utföras utan åtkomst till Key Vault, vilket inte bara minskar risken för begränsning, utan också förbättrar tillförlitligheten (så länge du cachelagrar det offentliga nyckelmaterialet på rätt sätt).
1. Om du använder Key Vault för att lagra autentiseringsuppgifter för en tjänst kontrollerar du om tjänsten stöder AAD-autentisering för att autentisera direkt. Detta minskar belastningen på Key Vault, förbättrar tillförlitligheten och förenklar din kod eftersom Key Vault nu kan använda AAD-token.  Många tjänster har flyttats till att använda AAD Auth.  Se den aktuella listan på [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Överväg att du gör din belastning/distribution under en längre tidsperiod för att hålla dig under de aktuella RPS-gränserna.
1. Om din app består av flera noder som behöver läsa samma hemlighet(er), sedan överväga att använda en fläkt ut mönster, där en enhet läser hemligheten från Key Vault, och fläktar ut till alla noder.   Cachelagra de hämtade hemligheterna endast i minnet.
Om du upptäcker att ovanstående fortfarande inte uppfyller dina behov, fyll i tabellen nedan och kontakta oss för att avgöra vilken ytterligare kapacitet som kan läggas till (exempel nedan endast för belysande ändamål).

| Namn på valv | Valv region | Objekttyp (hemligt, nyckel eller certifikat) | Operation(er)* | Nyckeltyp | Tangentlängd eller -kurva | HSM-nyckel?| Steady state RPS behövs | Peak RPS behövs |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Nyckel | Tecken | EC | P-256 | Inga | 200 | 1000 |

\*En fullständig lista över möjliga värden finns i [Azure Key Vault-åtgärder](/rest/api/keyvault/key-operations).

Om ytterligare kapacitet godkänns, observera följande till följd av kapacitetsökningarna:
1. Datakonsekvensmodellen ändras. När ett valv tillåter listad med ytterligare dataflödeskapacitet ändras garantigarantin för Key Vault-tjänstdatakonsekvens (nödvändigt för att uppfylla RPS med högre volym eftersom den underliggande Azure Storage-tjänsten inte kan hålla jämna steg).  I ett nötskal:
  1. **Utan tillåt notering:** Key Vault-tjänsten kommer att återspegla resultatet av en skrivåtgärd (t.ex. SecretSet, CreateKey) omedelbart i efterföljande samtal (t.ex. SecretGet, KeySign).
  1. **Med tillåt notering:** Key Vault-tjänsten kommer att återspegla resultatet av en skrivåtgärd (t.ex. SecretSet, CreateKey) inom 60 sekunder i efterföljande samtal (t.ex. SecretGet, KeySign).
1. Klientkoden måste uppfylla back-off-principen för 429 försök. Klientkoden som anropar Tjänsten Key Vault får inte omedelbart försöka sig på Key Vault-begäranden när den tar emot en 429-svarskod.  Azure Key Vault begränsning vägledning publiceras här rekommenderar att tillämpa exponentiell backoff när du får en 429 Http-svarskod.

Om du har ett giltigt affärsart för högre gasreglagegränser, vänligen kontakta oss.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Så här begränsar du appen som svar på tjänstgränser

Följande är **metodtips** som du bör implementera när tjänsten begränsas:
- Minska antalet åtgärder per begäran.
- Minska antalet begäranden.
- Undvik omedelbara återförsök. 
    - Alla begäranden ackumuleras mot dina användningsgränser.

När du implementerar appens felhantering använder du HTTP-felkoden 429 för att identifiera behovet av begränsning på klientsidan. Om begäran misslyckas igen med en HTTP 429-felkod stöter du fortfarande på en Azure-tjänstgräns. Fortsätt att använda den rekommenderade begränsningsmetoden på klientsidan och försöker igen begäran tills den lyckas.

Kod som implementerar exponentiell backoff visas nedan. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Det är enkelt att använda den här koden i ett C#-program för klienten. 

### <a name="recommended-client-side-throttling-method"></a>Rekommenderad begränsningsmetod på klientsidan

På HTTP-felkod 429 börjar du strypa klienten med en exponentiell backoff-metod:

1. Vänta 1 sekund, försök igen begäran
2. Om du fortfarande begränsar vänta 2 sekunder försöker du begäran om nytt
3. Om du fortfarande begränsar vänta 4 sekunder försöker du begära igen
4. Om du fortfarande begränsar vänta 8 sekunder försöker du begäran om återförsök
5. Om du fortfarande begränsar vänta 16 sekunder försöker du begäran om nytt

Nu bör du inte få HTTP 429 svarskoder.

## <a name="see-also"></a>Se även

En djupare orientering av begränsningen i Microsoft Cloud finns i [Begränsningsmönster](https://docs.microsoft.com/azure/architecture/patterns/throttling).

