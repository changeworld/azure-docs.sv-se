---
title: Konfigurera ditt konto för offlinestreaming av Widevine-skyddat innehåll - Azure
description: Det här avsnittet visar hur du konfigurerar ditt Azure Media Services-konto för offlinestreaming av Widevine-skyddat innehåll.
services: media-services
keywords: DASH, DRM, Widevine offlineläge, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: f3bd7bc78eeb62cc33a01ed31bb04d94078cae4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294338"
---
# <a name="offline-widevine-streaming-for-android"></a>Widevine-direktuppspelning offline för Android  

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 3](../latest/offline-widevine-for-android.md)
> * [Version 2](offline-widevine-for-android.md)

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Förutom att skydda innehåll för onlinestreaming erbjuder prenumerationer på medieinnehåll och uthyrningstjänster nedladdningsbart innehåll som fungerar när du inte är ansluten till internet. Du kan behöva hämta innehåll till telefonen eller surfplattan för uppspelning i flygplansläge när du flyger frånkopplad från nätverket. Ytterligare scenarier där du kanske vill hämta innehåll:

- Vissa innehållsleverantörer kan inte tillåta leverans av DRM-licenser utanför ett lands/regions gräns. Om en användare vill titta på innehåll när du reser utomlands behövs nedladdning offline.
- I vissa länder/regioner är Internettillgängligheten och/eller bandbredden begränsad. Användare kan välja att ladda ner innehåll för att kunna titta på det i tillräckligt hög upplösning för tillfredsställande tittarupplevelse.

I den här artikeln beskrivs hur du implementerar uppspelning av offlineläge för DASH-innehåll som skyddas av Widevine på Android-enheter. Offline DRM kan du tillhandahålla prenumerations-, uthyrnings- och inköpsmodeller för ditt innehåll, vilket gör det möjligt för kunder i dina tjänster att enkelt ta med sig innehåll när de kopplas bort från internet.

För att bygga Android-spelarens appar beskriver vi tre alternativ:

> [!div class="checklist"]
> * Skapa en spelare med Hjälp av Java API för ExoPlayer SDK
> * Bygg en spelare med Xamarin-bindning av ExoPlayer SDK
> * Skapa en spelare med EME (Encrypted Media Extension) och MSE (Media Source Extension) i webbläsaren Chrome mobile v62 eller senare

Artikeln besvarar också några vanliga frågor relaterade till offline streaming av Widevine skyddat innehåll.

## <a name="requirements"></a>Krav 

Innan du implementerar offline DRM för Widevine på Android-enheter bör du först:

- Bekanta dig med de koncept som introduceras för innehållsskydd online med Widevine DRM. Detta behandlas i detalj i följande dokument/prover:
    - [Använda Azure Media Services för att leverera DRM-licenser eller AES-nycklar](media-services-deliver-keys-and-licenses.md)
    - [CENC med Multi-DRM och åtkomstkontroll: En referensdesign och implementering i Azure och Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [Använda PlayReady och/eller Widevine Dynamic Common Encryption med .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Använd Azure Media Services för att leverera PlayReady- och/eller Widevine-licenser med .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Bekanta dig med Google ExoPlayer SDK för Android, en videospelare med öppen källkod SDK som kan stödja offline Widevine DRM-uppspelning. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer Developer Guide](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Utvecklare Blogg](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Konfiguration av innehållsskydd i Azure Media Services

När du konfigurerar Widevine-skydd för en tillgång i Media Services måste du skapa ContentKeyAuthorizationPolicyOption som anger följande tre saker:

1. DRM-system (Widevine)
2. ContentKeyAuthorizationPolicyÅterbrott som anger hur leverans av innehållsnycklar är auktoriserad i licensleveranstjänsten (Öppen eller tokenauktorisering)
3. LICENSMALL FÖR DRM (Widevine)

Om du vill aktivera **offlineläge** för Widevine-licenser måste du konfigurera [Widevine-licensmallen](media-services-widevine-license-template-overview.md). I **policy_overrides** objektet anger du **egenskapen can_persist** till **true** (standard är falskt). 

I följande kodexempel används .NET för att aktivera **offlineläge** för Widevine-licenser. Koden baseras på [exemplet Använda PlayReady och/eller Widevine Dynamic Common Encryption med .NET-exempel.](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>Konfigurera Android-spelaren för offlineuppspelning

Det enklaste sättet att utveckla en inbyggd spelarapp för Android-enheter är att använda [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), en videospelare med öppen källkod SDK. ExoPlayer stöder funktioner som för närvarande inte stöds av Androids inbyggda MediaPlayer-API, inklusive MPEG-DASH och Microsoft Smooth Streaming-leveransprotokoll.

ExoPlayer version 2.6 och högre innehåller många klasser som stöder offline Widevine DRM uppspelning. I synnerhet offlinelicenseHelper-klassen tillhandahåller verktygsfunktioner för att underlätta användningen av DefaultDrmSessionManager för nedladdning, förnyelse och publicering av offlinelicenser. Klasserna i SDK-mappen "library/core/src/main/java/com/google/android/exoplayer2/offline/" stöder nedladdning av videoinnehåll offline.

Följande lista över klasser underlättar offlineläge i ExoPlayer SDK för Android:

- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- bibliotek/kärna/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- bibliotek/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Utvecklare bör referera till [ExoPlayer Developer Guide](https://google.github.io/ExoPlayer/guide.html) och motsvarande [Developer Blog](https://medium.com/google-exoplayer) under utvecklingen av ett program. Google har inte släppt en fullt dokumenterad referens genomförande eller exempelkod för ExoPlayer app stödja Widevine offline just nu, så informationen är begränsad till utvecklarna guide och blogg. 

### <a name="working-with-older-android-devices"></a>Arbeta med äldre Android-enheter

För vissa äldre Android-enheter måste du ange värden för följande **policy_overrides** egenskaper (definierad i [Widevine-licensmallen:](media-services-widevine-license-template-overview.md) **rental_duration_seconds,** **playback_duration_seconds**och **license_duration_seconds**. Alternativt kan du ställa in dem till noll, vilket innebär oändlig / obegränsad varaktighet.  

Värdena måste ställas in för att undvika ett heltalsspillfel. Mer förklaring om problemet https://github.com/google/ExoPlayer/issues/3150 finns https://github.com/google/ExoPlayer/issues/3112i och . <br/>Om du inte uttryckligen anger värdena tilldelas mycket stora värden för **PlaybackDurationRemaining** och **LicenseDurationRemaining** (till exempel 9223372036854775807, vilket är det högsta positiva värdet för ett 64-bitars heltal). Som ett resultat verkar Widevine-licensen ha upphört att gälla och därför kommer dekrypteringen inte att ske. 

Det här problemet uppstår inte på Android 5.0 Lollipop eller senare eftersom Android 5.0 är den första Android-versionen, som har utformats för att fullt ut stödja ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) och 64-bitars plattformar, medan Android 4.4 KitKat ursprungligen utformades för att stödja ARMv7 och 32-bitars plattformar som med andra äldre Android-versioner.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Använda Xamarin för att skapa en Android-uppspelningsapp

Du kan hitta Xamarin bindningar för ExoPlayer med hjälp av följande länkar:

- [Xamarin binder bibliotek för Google ExoPlayer-biblioteket](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin bindningar för ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Se även följande tråd: [Xamarin bindning](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome Player-appar för Android

Från och med lanseringen av [Chrome för Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)stöds beständig licens i EME. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) stöds nu även i Chrome för Android. På så sätt kan du skapa offlineuppspelningsprogram i Chrome om slutanvändarna har den här (eller högre) versionen av Chrome. 

Dessutom har Google producerat en Progressiv Web App (PWA) exempel och öppen källkod det: 

- [Källkod](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google värd version](https://biograf-155113.appspot.com/ttt/episode-2/) (fungerar bara i Chrome v 62 och högre på Android-enheter)

Om du uppgraderar webbläsaren Chrome till v62 (eller högre) på en Android-telefon och testar exempelappen ovan värd ser du att både onlinestreaming och offlineuppspelning fungerar.

Pwa-appen ovan med öppen källkod skapas i Node.js. Om du vill vara värd för din egen version på en Ubuntu-server bör du tänka på följande vanliga problem som kan förhindra uppspelning:

1. CORS-problemet: Exempelvideon i exempelappen https://storage.googleapis.com/biograf-video-files/videos/finns i . Google har satt upp CORS för alla sina testprover som finns i Google Cloud Storage bucket. De serveras med CORS-huvuden och anger uttryckligen `https://biograf-155113.appspot.com` CORS-posten: (den domän där google är värd för deras exempel) som förhindrar åtkomst av andra webbplatser. Om du försöker visas följande HTTP-fel:`Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Certifikatproblem: Från Chrome v 58 kräver EME för Widevine HTTPS. Därför måste du vara värd för exempelappen via HTTPS med ett X509-certifikat. Ett vanligt testcertifikat fungerar inte på grund av följande krav: Du måste skaffa ett certifikat som uppfyller följande minimikrav:
    - Chrome och Firefox kräver att inställningen för alternativt namn för SAN-ämne finns i certifikatet
    - Certifikatet måste ha betrodd certifikatutfärdare och ett självsignerat utvecklingscertifikat fungerar inte
    - Certifikatet måste ha ett KN som matchar DNS-namnet på webbservern eller gatewayen

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="question"></a>Fråga

Hur kan jag leverera beständiga licenser (offlineaktiverade) för vissa klienter/användare och icke-beständiga licenser (offline inaktiverade) för andra? Måste jag duplicera innehållet och använda separat innehållsnyckel?

### <a name="answer"></a>Svar
Du behöver inte duplicera innehållet. Du kan helt enkelt använda en enda kopia av innehållet och en enda ContentKeyAuthorizationPolicy, men två separata ContentKeyAuthorizationPolicyOptions:

1. IContentKeyAuthorizationPolicyOption 1: använder beständig licens och ContentKeyAuthorizationPolicyRestriction 1 som innehåller ett anspråk som license_type = "Persistent"
2. IContentKeyAuthorizationPolicyOption 2: använder icke-beständig licens och ContentKeyAuthorizationPolicyRestriction 2 som innehåller ett anspråk som license_type = "Nonpersistent"

På så sätt, när en licensbegäran kommer in från klientappen, från licensbegäran finns det ingen skillnad. För olika slutanvändare/enheter bör STS dock ha affärslogiken för att utfärda olika JWT-token som innehåller olika anspråk (en av ovanstående två license_type). Anspråksvärdet i JWT-token används för att bestämma efter licenstjänst för att utfärda vilken typ av licens: beständig eller icke-beständig.

Det innebär att STS (Secure Token Service) måste ha affärslogik och klient-/enhetsinformation för att lägga till motsvarande anspråksvärde i en token.

### <a name="question"></a>Fråga

För Widevine-säkerhetsnivåer definierar den tre olika säkerhetsnivåer i Googles dokumentdokument dokumentation [för Widevine DRM Architecture Overview.](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) I [Azure Media Services-dokumentationen på Widevine-licensmallen](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)beskrivs dock fem olika säkerhetsnivåer. Vad är relationen eller mappningen mellan de två olika uppsättningarna säkerhetsnivåer?

### <a name="answer"></a>Svar

I Googles [Widevine DRM Architecture Overview](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)definierar den följande tre säkerhetsnivåer:

1.  Säkerhetsnivå 1: All innehållsbearbetning, kryptografi och kontroll utförs i TEE (Trusted Execution Environment). I vissa implementeringsmodeller kan säkerhetsbearbetning utföras i olika marker.
2.  Säkerhetsnivå 2: Utför kryptografi (men inte videobearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetas via separat videomaskinvara eller programvara. På nivå 2 bearbetas dock kryptografisk information fortfarande endast inom TEE.
3.  Säkerhetsnivå 3 Har ingen TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografisk information och dekrypterat innehåll i värdoperativsystemet. En nivå 3-implementering kan också innehålla en kryptografisk maskinvarumotor, men som bara förbättrar prestanda, inte säkerhet.

I Azure Media [Services-dokumentationen på Widevine-licensmallen](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)kan security_level-egenskapen för content_key_specs ha följande fem olika värden (klients robusthetskrav för uppspelning):

1.  Programvarubaserad whiteboxkrypt krävs.
2.  Programvarukrypt och en fördunklad dekoder krävs.
3.  Nyckelmaterialet och kryptoåtgärderna måste utföras inom en maskinvarubaserad TEE.
4.  Krypto och avkodning av innehåll måste utföras inom en maskinvarubaserad TEE.
5.  Krypto, avkodning och all hantering av mediet (komprimerad och okomprimerad) måste hanteras inom en maskinvarubaserad TEE.

Båda säkerhetsnivåerna definieras av Google Widevine. Skillnaden är i dess användningsnivå: arkitekturnivå eller API-nivå. De fem säkerhetsnivåerna används i Widevine API. Det content_key_specs objektet, som innehåller security_level är deserialiserat och skickas till Widevine globala leveranstjänst av Azure Media Services Widevine-licenstjänsten. Tabellen nedan visar mappningen mellan de två uppsättningarna säkerhetsnivåer.

| **Säkerhetsnivåer definierade i Widevine-arkitektur** |**Säkerhetsnivåer som används i Widevine API**|
|---|---| 
| **Säkerhetsnivå 1:** All innehållsbearbetning, kryptografi och kontroll utförs i TEE (Trusted Execution Environment). I vissa implementeringsmodeller kan säkerhetsbearbetning utföras i olika marker.|**security_level=5**: Krypto, avkodning och all hantering av mediet (komprimerad och okomprimerad) måste hanteras inom en maskinvarubackad TEE.<br/><br/>**security_level=4**: Krypto och avkodning av innehåll måste utföras i en maskinvarubackad TEE.|
**Säkerhetsnivå 2:** Utför kryptografi (men inte videobearbetning) inom TEE: dekrypterade buffertar returneras till programdomänen och bearbetas via separat videomaskinvara eller programvara. På nivå 2 bearbetas dock kryptografisk information fortfarande endast inom TEE.| **security_level=3**: Nyckelmaterialet och kryptoåtgärderna måste utföras inom en maskinvarubaserad TEE. |
| **Säkerhetsnivå 3:** Har ingen TEE på enheten. Lämpliga åtgärder kan vidtas för att skydda kryptografisk information och dekrypterat innehåll i värdoperativsystemet. En nivå 3-implementering kan också innehålla en kryptografisk maskinvarumotor, men som bara förbättrar prestanda, inte säkerhet. | **security_level=2**: Programvarukrypt och en fördunklad dekoder krävs.<br/><br/>**security_level=1**: Programvarubaserad whiteboxkrypt krävs.|

### <a name="question"></a>Fråga

Varför tar nedladdningen av innehåll så lång tid?

### <a name="answer"></a>Svar

Det finns två sätt att förbättra nedladdningshastigheten:

1.  Aktivera CDN så att slutanvändarna är mer benägna att träffa CDN istället för ursprung / streaming slutpunkt för nedladdning av innehåll. Om användaren träffar slutpunkten för direktuppspelning paketeras och krypteras varje HLS-segment eller DASH-fragment dynamiskt. Även om den här svarstiden är i millisekundskala för varje segment/fragment, kan den ackumulerade svarstiden vara stor och orsaka längre hämtning.
2.  Ge slutanvändare möjlighet att selektivt ladda ner videokvalitet lager och ljudspår i stället för allt innehåll. För offline-läge är det ingen idé att hämta alla kvalitetslager. Det finns två sätt att uppnå detta:
    1.  Klientkontrollerad: antingen väljer spelarens app automatiskt eller så väljer användaren videokvalitetslager och ljudspår att ladda ned.
    2.  Tjänststyrd: man kan använda funktionen Dynamiskt manifest i Azure Media Services för att skapa ett (globalt) filter som begränsar HLS-spellista eller DASH MPD till ett enda videokvalitetslager och valda ljudspår. Då nedladdnings-URL presenteras för slutanvändare kommer att innehålla detta filter.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="summary"></a>Sammanfattning

I den här artikeln diskuterades hur du implementerar uppspelning av offlineläge för DASH-innehåll som skyddas av Widevine på Android-enheter.  Det besvarade också några vanliga frågor om offline streaming av Widevine skyddat innehåll.
