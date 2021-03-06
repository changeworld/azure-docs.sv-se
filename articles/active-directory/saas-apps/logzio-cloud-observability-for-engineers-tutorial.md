---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Logz.io – Azure AD-integrering | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Logz.io - Azure AD-integrering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0984471-d12f-4f58-896e-194ea45b01fd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e192a8e97e82ba26ffa7527cb12219aeaf2a80cd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259467"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-logzio---azure-ad-integration"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Logz.io – Azure AD-integrering

I den här självstudien får du lära dig hur du integrerar Logz.io - Azure AD-integrering med Azure Active Directory (Azure AD). När du integrerar Logz.io – Azure AD-integrering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Logz.io - Azure AD-integrering.
* Gör att användarna automatiskt loggas in på Logz.io - Azure AD-integrering med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Logz.io - Azure AD Integration enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Logz.io - Azure AD-integrering stöder **IDP-initierad** SSO
* När du har konfigurerat Logz.io – Azure AD-integrering kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-logzio---azure-ad-integration-from-the-gallery"></a>Lägga till Logz.io - Azure AD-integrering från galleriet

För att konfigurera integreringen av Logz.io - Azure AD-integrering i Azure AD måste du lägga till Logz.io - Azure AD-integrering från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Logz.io - Azure AD-integrering** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Logz.io - Azure AD-integrering** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-logzio---azure-ad-integration"></a>Konfigurera och testa en azure AD-inloggning för Logz.io - Azure AD-integrering

Konfigurera och testa Azure AD SSO med Logz.io - Azure AD-integrering med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Logz.io - Azure AD-integrering.

Så här konfigurerar och testar du Azure AD SSO med Logz.io – Azure AD-integrering:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Logz.io - Azure AD Integration SSO](#configure-logzio-azure-ad-integration-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa Logz.io - Azure AD Integration testanvändare](#create-logzio-azure-ad-integration-test-user)** - att ha en motsvarighet till B.Simon i Logz.io - Azure AD-integrering som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på **sidan Logz.io - Azure AD-integreringsprogram** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`urn:auth0:logzio:CONNECTION-NAME`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://logzio.auth0.com/login/callback?connection=CONNECTION-NAME`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Logz.io - Azure AD Integration Client support team](mailto:help@logz.io) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Logz.io - Azure AD Integration-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom ovanstående Logz.io - Azure AD Integration-programmet förväntar sig att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.
    
    | Namn |  Källattribut|
    | ---------------| --------- |
    | session-förfallodatum | user.session-förfallodatum |
    | e-post | user.mail |
    | Grupp | användare.grupper |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera Logz.io - Azure AD-integrering.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Logz.io - Azure AD-integrering.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj Logz.io - **Azure AD-integrering**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-logzio-azure-ad-integration-sso"></a>Konfigurera SSO för Azure AD-integrering Logz.io Azure AD-integrering

Om du vill konfigurera enkel inloggning på **Logz.io - Azure AD-integreringssidan** måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade url:er från Azure-portalen till [Logz.io - Azure AD-integreringssupportteam](mailto:help@logz.io). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-logzio-azure-ad-integration-test-user"></a>Skapa Logz.io Azure AD-integreringstestanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Logz.io - Azure AD Integration. Arbeta med [Logz.io - Azure AD Integration supportteam](mailto:help@logz.io) för att lägga till användare i Logz.io - Azure AD Integration-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Logz.io - Azure AD-integrering på åtkomstpanelen bör du automatiskt loggas in på den Logz.io - Azure AD-integrering som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Logz.io – Azure AD-integrering med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Logz.io – Azure AD-integrering med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

