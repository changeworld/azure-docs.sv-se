---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med Euromonitor Passport | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Euromonitor Passport.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7740905c-04c4-4d8c-ad90-523a6cd1e206
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1748c6945dc50554c84fa8c178cece02baf9d840
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74075250"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-euromonitor-passport"></a>Självstudiekurs: Integrering av Enkel inloggning i Azure Active Directory (SSO) med Euromonitor Passport

I den här självstudien får du lära dig hur du integrerar Euromonitor Passport med Azure Active Directory (Azure AD). När du integrerar Euromonitor Passport med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Euromonitor Passport.
* Gör det möjligt för användarna att automatiskt loggas in på Euromonitor Passport med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Euromonitor Passport enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Euromonitor Passport stöder **SP- och IDP**-initierad enkel inloggning

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-euromonitor-passport-from-the-gallery"></a>Lägga till Euromonitor Passport från galleriet

För att konfigurera integrering av Euromonitor Passport med Azure AD behöver du lägga till Euromonitor Passport från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Euromonitor Passport** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Euromonitor Passport** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-euromonitor-passport"></a>Konfigurera och testa en enda Azure AD-inloggning för Euromonitor Passport

Konfigurera och testa Azure AD SSO med Euromonitor Passport med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Euromonitor Passport.

Så här konfigurerar och testar du Azure AD SSO med Euromonitor Passport:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Euromonitor Passport SSO](#configure-euromonitor-passport-sso)** - för att konfigurera de enskilda inloggningsinställningarna på applikationssidan.
    * **[Skapa Euromonitor Passport-testanvändare](#create-euromonitor-passport-test-user)** – om du vill ha en motsvarighet till B.Simon i Euromonitor Passport som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. På [Azure portal](https://portal.azure.com/)sidan Integrering av Euromonitor **Passport-programmet**på sidan **Programintegrering** i **Euromonitor Passport** hittar du avsnittet Hantera och väljer enkel inloggning .
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Om du vill konfigurera programmet i **SP**-initierat läge måste du hämta inloggnings-URL:en från [Euromonitor Passport-supportteamet](mailto:passport.support@euromonitor.com). När du får inloggnings-URL:en av Euromonitor Passport -supportteamet klickar du på **Ange ytterligare URL:er** och utföra följande steg:

    Klistra in det erhållna inloggnings-URL-värdet från Euromonitor Passport-supportteamet i textrutan Inloggnings-URL.

1. Klicka på **Spara**.

1. Euromonitor Passport-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig Euromonitor Passport ansökan få fler attribut som skall skickas tillbaka i SAML svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------- |
    | Namnidentifierarvärde | user.userprincipalname |

    > [!NOTE]
    > Klientadministratörer kan lägga till/ändra attribut enligt sina behov.

1. Klicka på knappen Kopiera i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** för att kopiera url till App **Federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Euromonitor Passport.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **Euromonitor Passport**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-euromonitor-passport-sso"></a>Konfigurera Euromonitor Passport SSO

För att konfigurera enkel inloggning på **Euromonitor Passport**-sidan behöver du skicka **URL för appens federationsmetadata** till [supportteamet för Euromonitor Passport](mailto:passport.support@euromonitor.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-euromonitor-passport-test-user"></a>Skapa Euromonitor Passport-testanvändare

I det här avsnittet skapar du en användare som heter B.Simon i Euromonitor Passport. Ta hjälp från [supportteamet för Euromonitor Passport](mailto:passport.support@euromonitor.com) och lägg till användarna på Euromonitor Passport-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Euromonitor Passport-panelen i åtkomstpanelen bör du automatiskt loggas in på Euromonitor Passport som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Euromonitor Passport med Azure AD](https://aad.portal.azure.com/)