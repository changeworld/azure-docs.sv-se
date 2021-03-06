---
title: Azurblå ytterdörr | Microsoft-dokument
description: Den här artikeln innehåller en översikt för Azure Front Door. Ta reda på om det är rätt val för belastningsbalanserad användartrafik för ditt program.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: sharadag
ms.openlocfilehash: b2ee41324cfaefa4d5aec3aa02b2d0d8c75da78f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879130"
---
# <a name="what-is-azure-front-door"></a>Vad är Azure Front Door?
Med Azure Front Door kan du definiera, hantera och övervaka den globala routningen för din webbtrafik genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet. Med Ytterdörren kan du omvandla dina globala konsument- och företagsprogram (flera regioner) till robusta, högpresterande anpassade moderna program, API:er och innehåll som når en global publik med Azure.

Front Door fungerar i Layer 7- eller HTTP/HTTPS-lagret och använder anycast-protokoll med delad TCP och Microsofts globala nätverk för att förbättra globala anslutningar. Så om du väljer routningsmetod i konfigurationen kan du vara säker på att Front Door dirigerar dina klientbegäranden till den snabbaste och mest tillgängliga programserverdelen. En programserverdel är en Internetansluten tjänst i eller utanför Azure. Front Door innehåller en uppsättning [trafikroutningsmetoder](front-door-routing-methods.md) och [alternativ för hälsoövervakning av serverdelen](front-door-health-probes.md) som passar olika programbehov och modeller för automatisk redundans. Precis som [Traffic Manager](../traffic-manager/traffic-manager-overview.md) är Front Door motståndskraftigt mot fel, inklusive fel som påverkar en hel Azure-region.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. Om du är intresserad av en DNS-baserad global routning och **inte** har några krav på avslutning av TLS-protokoll (Transport Layer Security) (”SSL-avlastning”) eller bearbetning på programnivå för enskilda HTTP/HTTPS-begäranden, kan [Traffic Manager](../traffic-manager/traffic-manager-overview.md) kanske passa dig. Om du vill ha belastningsutjämning mellan dina servrar i en region för programnivån kan du använda [Application Gateway,](../application-gateway/application-gateway-introduction.md) och vid belastningsutjämning av nätverkslagret kan [Azure Load Balancer](../load-balancer/load-balancer-overview.md) vara användbart. Du kan med fördel kombinera dessa lösningar efter behov för dina slutpunkts-till-slutpunkts-scenarier.
>
> En jämförelse med azure-belastningsutjämningsalternativ finns i [Översikt över belastningsutjämningsalternativ i Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Följande funktioner ingår i Front Door:

## <a name="accelerate-application-performance"></a>Påskynda programprestandan
Om du använder ett delat TCP-baserat anycast-protokoll, säkerställer Front Door att dina slutanvändare snabbt kan ansluta till närmaste POP-plats (Point of Presence) för Front Door. Med hjälp av Microsofts globala nätverk för att ansluta till dina programserverdelar från Front Doors POP, får du högre tillgänglighet och tillförlitlighet samtidigt som prestandan bibehålls. Den här anslutningen till din serverdel baseras också på så låg nätverksfördröjning som möjligt. Läs mer om Front Doors routningstekniker, som t.ex. [Delad TCP](front-door-routing-architecture.md#splittcp) och [Anycast-protokoll](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Öka programmets tillgänglighet med smarta hälsokontroller

Front Door ger hög tillgänglighet för dina viktiga program med hjälp av dess smarta hälsokontroller, där dina serverdelar övervakas för både svarstid och tillgänglighet och du får omedelbar automatisk redundans om en serverdel kraschar. Det innebär att du kan köra planerat underhåll för dina program utan någon stilleståndstid. Front Door dirigerar trafiken till alternativa serverdelar när underhållet pågår.

## <a name="url-based-routing"></a>URL-baserad routning
Med URL-sökvägsbaserad routning kan du dirigera trafik till serverdelspooler som baseras på URL-sökvägen till begärandet. Ett av scenarierna är att dirigera begäranden för olika innehållstyper till olika serverdelspooler.

Till exempel dirigeras begäranden för `http://www.contoso.com/users/*` till UserProfilePool och `http://www.contoso.com/products/*` dirigeras till ProductInventoryPool.  Med Front Door får du ännu mer komplexa vägmatchningsscenarier där den bästa matchningsalgoritmen används. Om inget sökvägsmönster matchar kommer standardhanteringsregeln för `http://www.contoso.com/*` att väljas och trafiken dirigeras till en standardhanteringsregel som fångar in alla. Läs mer i [Vägmatchning](front-door-route-matching.md).

## <a name="multiple-site-hosting"></a>Värd för flera platser
Om du har flera webbplatser kan du konfigurera fler än en webbplats inom samma Front Door-konfiguration. Med den här funktionen kan du konfigurera en mer effektiv topologi för dina distributioner genom att lägga till olika webbplatser i en enda Front Door-konfiguration. Baserat på programmets arkitektur kan du konfigurera Azure Front Door så att du antingen dirigerar varje webbplats till sin egen serverdelspool eller har olika webbplatser riktade till samma serverdelspool. Front Door kan exempelvis hantera trafik för `images.contoso.com` och `videos.contoso.com` från två serverdelspooler som heter ImagePool och VideoPool. Du kan också konfigurera att båda klientdelsvärdarna dirigerar trafik till en enda serverdelspool med namnet MediaPool.

På liknande sätt kan du ha två olika domäner, `www.contoso.com` och `www.fabrikam.com`, som har konfigurerats för samma Front Door.

## <a name="session-affinity"></a>Sessionstillhörighet
Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession i samma programserverdel. Med hjälp av Front Doors hanterade cookies, dirigeras efterföljande trafik från en användarsession till samma programserverdel för bearbetning. Den här funktionen är viktig i de fall där sessionstillstånd har sparats lokalt på serverdelen för en användarsession.

## <a name="tls-termination"></a>TLS-avslutning
Ytterdörren stöder TLS-avslutning vid kanten som är, enskilda användare kan ställa in en TLS-anslutning med ytterdörr miljöer istället för att upprätta den över långväga anslutningar med programmet backend. Dessutom har Front Door stöd för både HTTP- och HTTPS-anslutning mellan Front Door-miljöer och serverdelar. Så du kan också ställa in end-to-end TLS-kryptering. Om Front Door för din programarbetsbelastning tar emot över 5 000 begäranden per minut, kommer den vid återanvändning av anslutningen till aktiva tjänster endast upprätta 500 anslutningar till din programserverdel, vilket avsevärt minskar belastningen från serverdelen.

## <a name="custom-domains-and-certificate-management"></a>Anpassade domäner och certifikatshantering
När du använder Front Door för att leverera innehåll behövs en anpassad domän om du vill att ditt eget domännamn ska synas i din Front Door-URL. Att ha ett synligt domännamn kan vara praktiskt för dina kunder och användbart i profileringssyfte.
Front Door har också stöd för HTTPS för anpassade domännamn. Använd den här funktionen genom att antingen välja Front Door-hanterade certifikat för din trafik eller ladda upp ditt eget anpassade TLS/SSL-certifikat.

## <a name="application-layer-security"></a>Säkerhet för programskikt
Med Azure Front Door kan du skapa WAF-regler (Custom Web Application Firewall) för åtkomstkontroll för att skydda din HTTP/HTTPS-arbetsbelastning från utnyttjande baserat på klient-IP-adresser, landskod och http-parametrar. Dessutom kan du med Front Door också skapa begränsningsregler för att bekämpa skadlig robottrafik. Mer information om brandvägg för webbprogram finns i [Brandvägg för Azure-webbprogram?](../web-application-firewall/overview.md)

Själva Front Door-plattformen är skyddad av [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) Basic. Om du behöver mer skydd kan Azure DDoS Protection Standard aktiveras på dina virtuella nätverk och skydda resurser från nätverkslagerattacker (TCP/UDP) med hjälp av automatiska justeringar och åtgärder. Front Door är en omvänd proxy för Layer 7 där webbtrafik kan passera serverdelarna och andra typer av trafik blockeras som standard.

## <a name="url-redirection"></a>URL-omdirigering
Med den starka bransch push på att stödja endast säker kommunikation, webbprogram förväntas automatiskt omdirigera all HTTP-trafik till HTTPS. Detta säkerställer att all kommunikation mellan användarna och programmet sker över en krypterad sökväg. 

Traditionellt har programägare hanterat detta krav genom att skapa en dedikerad tjänst, vars enda syfte var att omdirigera begäranden som den tar emot på HTTP till HTTPS. Azure Front Door stöder möjligheten att omdirigera trafik från HTTP till HTTPS. Detta förenklar programkonfigurationen, optimerar resursanvändningen och stöder nya omdirigeringsscenarier, inklusive global och sökvägsbaserade omdirigering. URL-omdirigering från Azure Front Door är inte begränsad till HTTP till HTTPS-omdirigering ensam, utan också för att omdirigera till ett annat värdnamn, omdirigera till en annan sökväg eller till och med omdirigera till en ny frågesträng i URL:en.

Mer information finns i [omdirigera trafik](front-door-url-redirect.md) med Azure Front Door.

## <a name="url-rewrite"></a>URL-omskrivning
Front Door har stöd för [URL-omskrivning](front-door-url-rewrite.md), vilket innebär att du kan konfigurera en valfri anpassad sökväg för vidarebefordran. Den används när en begäran skapas som ska vidarebefordras till serverdelen. Med Front Door kan du dessutom konfigurera det värdhuvud som ska skickas när du vidarebefordrar begärandet till din serverdel.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Protokollstöd – IPv6- och HTTP/2-trafik
Azure Front Door har inbyggt stöd för IPv6-anslutningar för slutpunkt till slutpunkt och även för HTTP/2-protokoll. 

HTTP/2-protokollet ger full duplex-kommunikation mellan programserverdelar och en klient över en långvarig TCP-anslutning. HTTP/2 ger en mer interaktiv kommunikation mellan serverdelen och klienten, som kan vara dubbelriktad utan att behöva den avsökning som krävs i HTTP-baserade implementeringar. HTTP/2-protokoll har låga omkostnader, till skillnad från HTTP, och kan återanvända samma TCP-anslutning för flera begäranden eller svar, vilket resulterar i ett mer effektivt utnyttjande av resurser. Läs mer om [HTTP/2-stöd i Azure Front Door](front-door-http2.md).

## <a name="pricing"></a>Prissättning

Information om priser finns i [Prissättning för Front Door](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
