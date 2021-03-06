---
title: Konfigurera inloggning med ett Twitter-konto med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera inloggning med ett Twitter-konto med hjälp av anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5804ded875ef03d7ade4414eb8f08885634748dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051600"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning med ett Twitter-konto med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare av ett Twitter-konto med hjälp av [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).
- Om du inte redan har ett Twitter-konto, skapa en på [Twitter sign-up sida](https://twitter.com/signup).

## <a name="create-an-application"></a>Skapa ett program

Om du vill använda Twitter som identitetsleverantör i Azure AD B2C måste du skapa ett Twitter-program.

1. Logga in på [Twitter Developers](https://developer.twitter.com/en/apps) webbplats med dina Twitter-kontouppgifter.
2. Välj **Skapa en app**.
3. Ange ett **appnamn** och en **programbeskrivning**.
4. I **Webbadress**anger `https://your-tenant.b2clogin.com`du . Ersätt `your-tenant` med namnet på din klient. Till exempel `https://contosob2c.b2clogin.com`.
5. För **motringningsadressen**anger du `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`. Ersätt `your-tenant` med namnet på ditt `your-policy-Id` klientnamn och med identifieraren för din princip. Till exempel `b2c_1A_signup_signin_twitter`. Du måste använda alla gemener när du anger ditt klientnamn även om klienten definieras med versaler i Azure AD B2C.
6. Längst ned på sidan läser och accepterar du villkoren och väljer sedan **Skapa**.
7. På sidan **Appinformation** väljer du **Redigera > Redigera information,** markerar kryssrutan Aktivera **inloggning med Twitter**och väljer sedan **Spara**.
8. Välj **Nycklar och token** och registrera **konsument-API-nyckeln** och **de hemliga nyckelnvärden för konsument-API** som ska användas senare.

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du måste lagra den hemliga nyckeln som du tidigare spelat in i din Azure AD B2C-klientorganisation.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **Identity Experience Framework**.
5. Välj **Principnycklar** och välj sedan **Lägg till**.
6. För **Alternativ** `Manual`väljer du .
7. Ange ett **namn** för principnyckeln. Till exempel `TwitterSecret`. Prefixet `B2C_1A_` läggs automatiskt till i namnet på nyckeln.
8. I **Secret**anger du din klienthemlighet som du tidigare spelat in.
9. För **nyckelanvändning** `Encryption`väljer du .
10. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägga till en anspråksleverantör

Om du vill att användare ska logga in med ett Twitter-konto måste du definiera kontot som en anspråksleverantör som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Du kan definiera ett Twitter-konto som en anspråksleverantör genom att lägga till det i elementet **ClaimsProviders i förlängningsfilen** för din policy.

1. Öppna *TrustFrameworkExtensions.xml*.
2. Leta reda på elementet **ClaimsProviders.** Om den inte finns lägger du till den under rotelementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ersätt värdet **för client_id** med konsumentnyckeln som du tidigare har registrerat.
5. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Vid det här laget har du konfigurerat din princip så att Azure AD B2C vet hur du kommunicerar med ditt Twitter-konto. Prova att ladda upp förlängningsfilen för din policy bara för att bekräfta att den inte har några problem hittills.

1. På sidan **Anpassade principer** i din Azure AD B2C-klient väljer du **Överför princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions.xml.*
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksleverantören

Nu har identitetsprovidern konfigurerats, men den är inte tillgänglig på någon av inloggnings- eller inloggningsskärmarna. Om du vill göra den tillgänglig skapar du en dubblett av en befintlig mallanvändarresa och ändrar den så att den också har Twitter-identitetsprovidern.

1. Öppna filen *TrustFrameworkBase.xml* från startpaketet.
2. Leta reda på och kopiera hela innehållet `Id="SignUpOrSignIn"`i **UserJourney-elementet** som innehåller .
3. Öppna *TrustFrameworkExtensions.xml* och leta reda på **Elementet UserJourneys.** Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney-elementet** som du kopierade som underordnade **elementet UserJourneys.**
5. Byt namn på ID för användarens färd. Till exempel `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Visa knappen

Elementet **ClaimsProviderSelection** är analogt med en identitetsleverantörsknapp på en inloggnings- eller inloggningsskärm. Om du lägger till ett **ClaimsProviderSelection-element** för ett Twitter-konto visas en ny knapp när en användare hamnar på sidan.

1. Leta reda på **orchestrationStep-elementet** som ingår `Order="1"` i användarfärden som du skapade.
2. Lägg till följande element under **ClaimsProviderSelects.** Ange värdet för **TargetClaimsExchangeId** till ett `TwitterExchange`lämpligt värde, till exempel:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C att kommunicera med ett Twitter-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som `Order="2"` ingår i användarfärden.
2. Lägg till följande **ClaimsExchange-element** och se till att du använder samma värde för ID:et som du använde för **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till ID för den tekniska profil som du skapade tidigare. Till exempel `Twitter-OAUTH1`.

3. Spara *filen TrustFrameworkExtensions.xml* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i din B2C-klientorganisation. I det här avsnittet visas valfria steg som du kan slutföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande partfilen

Uppdatera den förlitande partens (RP) fil som initierar användarens färd som du skapade.

1. Gör en kopia av *SignUpOrSignIn.xml* i arbetskatalogen och byt namn på den. Byt till exempel namn på den till *SignUpSignInTwitter.xml*.
2. Öppna den nya filen och uppdatera värdet för **Attributet PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInTwitter`.
3. Uppdatera värdet för **PublicPolicyUri** med URI för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Uppdatera värdet för **Attributet ReferenceId** i **DefaultUserJourney** så att det matchar ID:t för den nya användarresan som du skapade (SignUpSignTwitter).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrollera att Azure AD B2C-program som du har skapat har valts i fältet **Välj program** och testa det sedan genom att klicka på **Kör nu**.
