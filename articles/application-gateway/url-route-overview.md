---
title: Översikt över dirigering av URL-baserat innehåll med Azure Application Gateway
description: Den här artikeln innehåller en översikt över URL-baserad innehållsroutning för Azure Application Gateway, UrlPathMap-konfiguration och PathBasedRouting-regeln.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e20acb131b1a091fef858dab34705f4a8d3b4c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251846"
---
# <a name="url-path-based-routing-overview"></a>Översikt över URL-sökvägsbaserad routning

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. 

Ett av scenarierna är att dirigerar begäranden för olika innehållstyper till olika serverdels-serverpooler.

I följande exempel servar Application Gateway trafik åt contoso.com från tre serverdels-serverpooler, till exempel: VideoServerPool, ImageServerPool och DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Begäranden\:om http //contoso.com/video/* dirigeras till VideoServerPool\:och http //contoso.com/images/* dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.

> [!IMPORTANT]
> För v1 SKU bearbetas regler i den ordning de visas i portalen. Om en grundläggande lyssnare visas först och matchar en inkommande begäran kommer den att bearbetas av den lyssnaren. För v2 SKU har exakta matchningar högre prioritet. Det rekommenderas dock starkt att konfigurera lyssnare på flera webbplatser först innan du konfigurerar en grundläggande lyssnare. Detta säkerställer att trafik dirigeras till rätt serverdel.

## <a name="urlpathmap-configuration-element"></a>UrlPathMap-konfigurationselementet

UrlPathMap-elementet används för att ange sökvägsmönster till mappningar för serverdelen och serverpoolen. Följande kodexempel är utdrag av urlPathMap-element från mallfilen.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>PathPattern

PathPattern är en lista över banmönster som ska matchas. Vart och ett måste börja med / och ett * får bara förekomma på slutet följt av ett /. Strängen som matas till sökvägen matchar innehåller inte någon text efter den första ? eller #, och dessa tecken är inte tillåtna här. Annars tillåts alla tecken som tillåts i en URL i PathPattern.

De mönster som stöds beror på om du distribuerar Application Gateway v1 eller v2:

#### <a name="v1"></a>v1 (på)

Sökvägsregler är skiftlägeskänsliga.

|v1 bana mönster  |Stöds?  |
|---------|---------|
|`/images/*`     |ja|
|`/images*`     |nej|
|`/images/*.jpg`     |nej|
|`/*.jpg`     |nej|
|`/Repos/*/Comments/*`     |nej|
|`/CurrentUser/Comments/*`     |ja|

#### <a name="v2"></a>v2

Sökvägsregler är skiftlägeskänsliga.

|v2-banmönster  |Stöds?  |
|---------|---------|
|`/images/*`     |ja|
|`/images*`     |ja|
|`/images/*.jpg`     |nej|
|`/*.jpg`     |nej|
|`/Repos/*/Comments/*`     |nej|
|`/CurrentUser/Comments/*`     |ja|

Du kan kolla en [Resource Manager-mall med URL-baserad routning](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) för mer information.

## <a name="pathbasedrouting-rule"></a>PathBasedRouting-regeln

RequestRoutingRule av typen PathBasedRouting används för att binda en lyssnare till en urlPathMap. Alla begäranden som tas emot för den här lyssnaren dirigeras baserat på principen som anges i urlPathMap.
Utdrag från PathBasedRouting-regeln:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>Nästa steg

När du läst om URL-baserad innehållsroutning, kan du gå till [skapa en Application Gateway med URL-baserad routing](create-url-route-portal.md) för att skapa en Application Gateway med regler för URL-routning.
