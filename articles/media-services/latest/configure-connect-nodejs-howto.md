---
title: Ansluta till Azure Media Services v3 API - Node.js
description: Den här artikeln visar hur du ansluter till Media Services v3 API med Node.js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896107"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ansluta till Media Services v3 API - Node.js

Den här artikeln visar hur du ansluter till Azure Media Services v3 node.js SDK med hjälp av tjänstens huvudloggmetod.

## <a name="prerequisites"></a>Krav

- Installera [Node.js](https://nodejs.org/en/download/).
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Kom ihåg resursgruppsnamnet och mediatjänstkontonamnet.

> [!IMPORTANT]
> Granska [namngivningskonventioner](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Skapa package.json

1. Skapa en package.json-fil med din favoritredigerare.
1. Öppna filen och klistra in följande kod:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Följande paket bör anges:

|Paket|Beskrivning|
|---|---|
|`azure-arm-mediaservices`|Sdk för Azure Media Services. <br/>Kontrollera att du använder det senaste Azure Media [Services-paketet npm installera azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Lagring SDK. Används när filer laddas upp till tillgångar.|
|`ms-rest-azure`| Används för att logga in.|

Du kan köra följande kommando för att kontrollera att du använder det senaste paketet:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Ansluta till nod.js-klient

1. Skapa en .js-fil med din favoritredigerare.
1. Öppna filen och klistra in följande kod.
1. Ange värdena i avsnittet "slutpunktskonfiguration" på värden som du har fått från [åtkomst-API:er](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Köra appen

Öppna en kommandotolk. Bläddra till exempelkatalogen och kör följande kommandon:

```
npm install 
node index.js
```

## <a name="see-also"></a>Se även

- [Koncept för Media Services](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Nästa steg

Utforska Media Services [Node.js-referensdokumentationen](/javascript/api/overview/azure/mediaservices/management) och titta på de [exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials) som visar hur du använder Media Services API med node.js.

