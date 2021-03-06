---
title: Registrera en webbapp som anropar webb-API:er – Microsoft identity platform | Azure
description: Läs om hur du registrerar en webbapp som anropar webb-API:er
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881884"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>En webbapp som anropar webb-API:er: Appregistrering

En webbapp som anropar webb-API:er har samma registrering som en webbapp som loggar in användare. Så, följ instruktionerna i [en webbapp som loggar in användare: Appregistrering](scenario-web-app-sign-user-app-registration.md).

Men eftersom webbappen nu också anropar webb-API:er blir det ett konfidentiellt klientprogram. Det är därför en del extra registrering krävs. Appen måste dela klientuppgifter eller *hemligheter*med Microsofts identitetsplattform.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Webbappar anropar API:er för den inloggade användarens räkning. För att göra det måste de begära *delegerade behörigheter*. Mer information finns i [Lägga till behörigheter för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [En webbapp som anropar webb-API:er: Kodkonfiguration](scenario-web-app-call-api-app-configuration.md)
