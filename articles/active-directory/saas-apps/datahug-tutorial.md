---
title: 'Självstudiekurs: Azure Active Directory-integrering med Datahug | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Datahug.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9271d8b5657769ce70c46b5e428d995ddc642608
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158440"
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Självstudiekurs: Azure Active Directory-integrering med Datahug

I den här självstudien får du lära dig hur du integrerar Datahug med Azure Active Directory (AD Azure).
Integreringen mellan Datahug och Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Datahug från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i Datahug (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Datahug behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En Datahug-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Datahug stöder **SP**- och **IDP**-initierad enkel inloggning

## <a name="adding-datahug-from-the-gallery"></a>Lägga till Datahug från galleriet

För att konfigurera integrering av Datahug i Azure AD måste du lägga till Datahug från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Datahug från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Datahug**, väljer **Datahug** i resultatrutan och klickar på **Lägg till** för att lägga till programmet.

     ![Datahug i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Datahug baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Datahug upprättas.

För att konfigurera och testa enkel inloggning mellan Azure AD och Datahug måste du göra följande:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Datahug](#configure-datahug-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en testanvändare för Datahug](#create-datahug-test-user)** – för att skapa en motsvarighet till Britta Simon i Datahug som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning mellan Azure AD och Datahug:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Datahug** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Information om domäner och URL:er för enkel inloggning med Datahug](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://apps.datahug.com/identity/<uniqueID>`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://apps.datahug.com/identity/<uniqueID>/acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om domäner och URL:er för enkel inloggning med Datahug](common/metadata-upload-additional-signon.png)

    Skriv en URL i textrutan **Sign-on-URL:**`https://apps.datahug.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Datahug-klientsupporten](https://datahug.com/about/contact-us/) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **XML-federationsmetadata** från de angivna alternativen enligt dina behov och sparar dem på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **SAML-signeringscertifikat** klickar du på **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat** och utför följande steg.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

    a. Välj **Signera SAML-försäkran** från **Signeringsalternativ**.

    b. Välj **SHA-1** från **Signeringsalgoritm**.
    
    c. Klicka på **Spara**

    ![Communifire-signeringsalternativ](./media/datahug-tutorial/tutorial_datahug_signingoption.png)

8. I avsnittet **Konfigurera Datahug** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-datahug-single-sign-on"></a>Konfigurera enkel inloggning för Datahug

För att konfigurera enkel inloggning på **Datahug**-sidan måste du skicka den nedladdade **XML:en med federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [Datahug-supportteamet](https://datahug.com/about/contact-us/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. Ange **BrittaSimon**i fältet **Namn** .
  
    b. Skriv **brittasimon\@ditt företaganydomain.extension** i fältet **Användarnamn**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Datahug.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Datahug**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Datahug**.

    ![Datahug-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-datahug-test-user"></a>Skapa en Datahug-testanvändare

För att göra det möjligt för Azure AD-användare att logga in i Datahug måste de etableras i Datahug.  
Med Datahug är etableringen en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Datahug-företagswebbplats som administratör.

2. Hovra över **kugghjulet** i det övre högra hörnet och klicka på **Inställningar**
   
    ![Lägga till medarbetare](./media/datahug-tutorial/1.png)

3. Välj **Personer** och klicka på fliken **Lägg till användare**

    ![Lägga till medarbetare](./media/datahug-tutorial/2.png)

4. Skriv e-postadressen för den person som du vill skapa ett konto för och klicka på **Lägg till**.

    ![Lägga till medarbetare](./media/datahug-tutorial/3.png)

    > [!NOTE] 
    > Du kan skicka ett registreringsmeddelande till användare via e-post genom att markera kryssrutan **Skicka välkomstmeddelande via e-post**.  
    > Skapa inte välkomstmeddelandet om du skapar ett konto för Salesforce.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Datahug-panelen på åtkomstpanelen bör du loggas in automatiskt i Datahug som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

