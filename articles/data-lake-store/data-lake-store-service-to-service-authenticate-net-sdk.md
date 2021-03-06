---
title: 'Autentisering från tjänst: .NET SDK med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du uppnår autentisering mellan tjänst och tjänst med Azure Data Lake Storage Gen1 med Azure Active Directory med .NET SDK
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 96c496ef67e26a3079577bf52e9d019d963467b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265537"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med .NET SDK
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

I den här artikeln får du lära dig mer om hur du använder .NET SDK för att göra tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1. För slutanvändareautentisering med DataSjölagring Gen1 med .NET SDK finns i [Slutanvändarens autentisering med DataSjölagringGenm1 med .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Krav
* **Visual Studio 2013 eller högre**. Instruktionerna nedan använder Visual Studio 2019.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "Web"-program**. Du måste ha slutfört stegen i [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Skapa ett .NET-program
1. Välj Arkiv-menyn, **File** **Ny**och sedan **Project**.
2. Välj **Console App (.NET Framework)** och välj sedan **Nästa**.
3. I **Projektnamn** `CreateADLApplication`anger du och väljer sedan **Skapa**.

4. Lägg till NuGet-paketen i projektet.

   1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
   2. Kontrollera att **paketkällan** är inställd **på nuget.org** på fliken **NuGet Package Manager** och att kryssrutan Inkludera **förhandsversion** är markerad.
   3. Sök efter och installera följande NuGet-paket:

      * `Microsoft.Azure.Management.DataLake.Store` – I den här självstudiekursen används v2.1.3-förhandsversionen.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` –I den här självstudiekursen används v2.2.12.

        ![Lägga till en NuGet-källa](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Skapa ett nytt Azure Data Lake-konto")
   4. Stäng **NuGet Package Manager**.

5. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Autentisering mellan tjänst och tjänst med klienthemlighet
Lägg till det här kodavsnittet i .NET-klientprogrammet. Ersätt platshållarvärdena med de värden som hämtas från ett Azure AD-webbprogram (listat som en förutsättning). Med det här kodavsnittet kan du autentisera ditt program **icke-interaktivt** med Data Lake Storage Gen1 med hjälp av klienthemligheten/nyckeln för Azure AD-webbprogrammet.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

I föregående utdrag används en hjälpfunktion `GetCreds_SPI_SecretKey`. Koden för den här hjälpfunktionen finns [här på GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Autentisering mellan tjänst och certifikat

Lägg till det här kodavsnittet i .NET-klientprogrammet. Ersätt platshållarvärdena med de värden som hämtas från ett Azure AD-webbprogram (listat som en förutsättning). Med det här kodavsnittet kan du autentisera ditt program **icke-interaktivt** med Data Lake Storage Gen1 med hjälp av certifikatet för ett Azure AD-webbprogram. Instruktioner om hur du skapar ett Azure AD-program finns i [Skapa tjänstens huvudnamn med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

I föregående utdrag används en hjälpfunktion `GetCreds_SPI_Cert`. Koden för den här hjälpfunktionen finns [här på GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Nästa steg
I den här artikeln fick du lära dig hur du använder tjänst-till-tjänst-autentisering för att autentisera med Data Lake Storage Gen1 med .NET SDK. Du kan nu titta på följande artiklar som talar om hur du använder .NET SDK för att arbeta med Data Lake Storage Gen1.

* [Kontohanteringsåtgärder på DataSjölagring Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dataåtgärder på Datasjölagring Gen1 med .NET SDK](data-lake-store-data-operations-net-sdk.md)
