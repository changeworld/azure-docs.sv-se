---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SurveyMonkey Enterprise | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SurveyMonkey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a2d9b57-27b5-4d9f-b52c-09b692872cf7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4238033f94fe8bfdc677c9eb623a2eab3cdf371c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72532929"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-surveymonkey-enterprise"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SurveyMonkey Enterprise

I den här självstudien får du lära dig hur du integrerar SurveyMonkey Enterprise med Azure Active Directory (Azure AD). När du integrerar SurveyMonkey Enterprise med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SurveyMonkey Enterprise.
* Gör att användarna automatiskt loggas in på SurveyMonkey Enterprise med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SurveyMonkey Enterprise enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SurveyMonkey Enterprise stöder **IDP** initierad SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-surveymonkey-enterprise-from-the-gallery"></a>Lägga till SurveyMonkey Enterprise från galleriet

Om du vill konfigurera integreringen av SurveyMonkey Enterprise i Azure AD måste du lägga till SurveyMonkey Enterprise från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SurveyMonkey Enterprise** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SurveyMonkey Enterprise** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-surveymonkey-enterprise"></a>Konfigurera och testa en enda Azure AD-inloggning för SurveyMonkey Enterprise

Konfigurera och testa Azure AD SSO med SurveyMonkey Enterprise med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SurveyMonkey Enterprise.

Så här konfigurerar och testar du Azure AD SSO med SurveyMonkey Enterprise:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SurveyMonkey Enterprise SSO](#configure-surveymonkey-enterprise-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa SurveyMonkey Enterprise-testanvändare](#create-surveymonkey-enterprise-test-user)** – om du vill ha en motsvarighet till B.Simon i SurveyMonkey Enterprise som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan **SurveyMonkey** Enterprise-programintegrering hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** är programmet förkonfigurerat och nödvändiga url:er är redan förifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på **knappen Spara.**

1. SurveyMonkey Enterprise-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

6. Utöver ovanstående förväntar SurveyMonkey Enterprise ansökan några fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | E-post | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera SurveyMonkey Enterprise** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SurveyMonkey Enterprise.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **SurveyMonkey Enterprise**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-surveymonkey-enterprise-sso"></a>Konfigurera SurveyMonkey Enterprise SSO

Om du vill konfigurera enkel inloggning på **SurveyMonkey Enterprise-sidan** måste du skicka den nedladdade **XML-koden för federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [SurveyMonkey Enterprise-supportteamet](mailto:support@selerix.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-surveymonkey-enterprise-test-user"></a>Skapa SurveyMonkey Enterprise-testanvändare

Det är inte nödvändigt att skapa en testanvändare i SurveyMonkey Enterprise. Användarkonton etableras om användaren väljer att skapa ett nytt konto, baserat på SAML- påståendet. Din SurveyMonkey Enterprise Customer Success Manager kommer att ge steg för att slutföra den här processen när dina Azure-metadata har lagts till i SurveyMonkey Enterprise-konfigurationen och den är redo att valideras.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SurveyMonkey Enterprise på åtkomstpanelen bör du automatiskt loggas in på det SurveyMonkey Enterprise som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SurveyMonkey Enterprise med Azure AD](https://aad.portal.azure.com/)