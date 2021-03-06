---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med BenSelect | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BenSelect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffa17478-3ea1-4356-a289-545b5b9a4494
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a027a4038d806b7c19dc45ea5b6da74acb802b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74893135"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-benselect"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med BenSelect

I den här självstudien får du lära dig hur du integrerar BenSelect med Azure Active Directory (Azure AD). När du integrerar BenSelect med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till BenSelect.
* Gör att användarna automatiskt loggas in på BenSelect med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* BenSelect enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.


* BenSelect stöder **IDP** initierad SSO

> [!NOTE]
> Identifieraren för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klient.

## <a name="adding-benselect-from-the-gallery"></a>Lägga till BenSelect från galleriet

Om du vill konfigurera integreringen av BenSelect i Azure AD måste du lägga till BenSelect från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv BenSelect i sökrutan i avsnittet **Lägg till från galleriet.** **BenSelect**
1. Välj **BenSelect** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-benselect"></a>Konfigurera och testa en azure AD-inloggning för BenSelect

Konfigurera och testa Azure AD SSO med BenSelect med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i BenSelect.

Så här konfigurerar och testar du Azure AD SSO med BenSelect:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera BenSelect SSO](#configure-benselect-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa BenSelect-testanvändare](#create-benselect-test-user)** – om du vill ha en motsvarighet till B.Simon i BenSelect som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan BenSelect-programintegrering i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**. **BenSelect**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    Skriv en URL med följande mönster i textrutan **Svara URL:**`https://www.benselect.com/enroll/login.aspx?Path=<tenant name>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta Supportteamet för [BenSelect-klienten](mailto:support@selerix.com) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. BenSelect-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Klicka på ikonen **Redigera** om du vill redigera **namnidentifieringsvärdet**.

    ![image](media/benselect-tutorial/mail-prefix1.png)

1. Gör följande i avsnittet **Hantera användarens anspråk:**

    ![image](media/benselect-tutorial/mail-prefix2.png)

    a. Välj **Omvandling** som **källa**.

    b. Välj **ExtractMailPrefix()** i listrutan **Omvandling.**

    c. Välj **user.userprincipalname**i listrutan **Parameter 1** .

    d. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML,** i avsnittet **SAML-signeringscertifikat,** hittar **du Certifikat (Raw)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera BenSelect** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till BenSelect.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **BenSelect**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-benselect-sso"></a>Konfigurera BenSelect SSO

Om du vill konfigurera enkel inloggning på **BenSelect-sidan** måste du skicka det hämtade **certifikatet (Raw)** och lämpliga kopierade url:er från Azure-portalen till [Supportteamet för BenSelect](mailto:support@selerix.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!NOTE]
> Du måste nämna att denna integration kräver SHA256 algoritm (SHA1 stöds inte) för att ställa in SSO på lämplig server som app2101 etc.

### <a name="create-benselect-test-user"></a>Skapa BenSelect-testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i BenSelect. Arbeta med Supportteamet för [BenSelect](mailto:support@selerix.com) för att lägga till användarna i BenSelect-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen BenSelect på åtkomstpanelen ska du automatiskt loggas in på den BenSelect som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova BenSelect med Azure AD](https://aad.portal.azure.com/)

