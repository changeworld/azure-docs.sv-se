---
title: 'Självstudiekurs: Azure Active Directory-integrering med Recognize | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Recognize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943345"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Självstudiekurs: Azure Active Directory-integrering med Recognize

I den här självstudien får du lära dig hur du integrerar Recognize med Azure Active Directory (Azure AD).
Genom att integrera Recognize med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Recognize.
* Du kan aktivera dina användare så att de automatiskt loggas in på Identifiera (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Recognize behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Identifiera en prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Identifiera stöder **SP** initierade SSO

## <a name="adding-recognize-from-the-gallery"></a>Lägga till Igen från galleriet

Om du vill konfigurera integreringen av Recognize i Azure AD måste du lägga till Recognize från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Recognize från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Identifiera**i sökrutan och **klicka** sedan på Lägg till knappen **Lägg** till för att lägga till programmet.

     ![Identifiera i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD-enkel inloggning med Recognize baserat på en testanvändare som heter **Britta Simon**.
För enkel inloggning för att fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Recognize upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Recognize måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Identifiera enkel inloggning](#configure-recognize-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa identifiera testanvändare](#create-recognize-test-user)** - att ha en motsvarighet till Britta Simon i Recognize som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Recognize:

1. Välj Enkel inloggning på sidan **Identifiera programintegrering** i [Azure-portalen](https://portal.azure.com/). **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du får **metadatafilen för Tjänsteleverantören** från avsnittet **Konfigurera identifiera enkel inloggning i** självstudien.

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har överförts fylls **identifierare-värdet** i automatiskt i avsnittet Grundläggande SAML-konfiguration.

    ![Identifiera domän- och webbadresser med enkel inloggning](common/sp-identifier.png)

     Skriv en URL med följande mönster i textrutan **Logga in på WEBBADRESS:**`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Om **identifieringsvärdet** inte fylls i automatiskt får du värdet Identifierare genom att öppna url:en för tjänsteleverantörens metadata från avsnittet **SSO-inställningar** som förklaras senare i avsnittet Konfigurera identifiera enkel inloggning i självstudien. Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Identifiera klientsupportteam](mailto:support@recognizeapp.com) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. Kopiera lämpliga webbadresser enligt dina krav i avsnittet **Konfigurera igen.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-recognize-single-sign-on"></a>Konfigurera identifiera en inloggning

1. I ett annat webbläsarfönster loggar du in på din Identifiera klientorganisation som administratör.

2. Klicka på **Meny**i det övre högra hörnet . Gå till **Företagsadministratör**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_000.png)

3. I det vänstra navigeringsfönstret klickar du på **Inställningar**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Utför följande steg i avsnittet **SSO-inställningar.**
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Välj **PÅ** **som Aktivera SSO**.

    b. Klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen i **IDP-entitets-ID-textrutan.**
    
    c. Klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **Sso-måladress.**
    
    d. Klistra in värdet **för url-URL-url som** du har kopierat från Azure-portalen i **textrutan Slo-måladress.** 
    
    e. Öppna den nedladdade **certifikatfilen (Base64)** i anteckningar, kopiera innehållet i den i Urklipp och klistra sedan in den i **textrutan Certifikat.**
    
    f. Klicka på knappen **Spara inställningar.** 

5. Bredvid **avsnittet SSO-inställningar** kopierar du webbadressen under **Url för tjänsteleverantörermetadata**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Öppna **url-länken metadata** under en tom webbläsare för att hämta metadatadokumentet. Kopiera sedan entitydescriptor-värdet(entityID) från filen och klistra in det i **textrutan Identifierare** i **Grundläggande SAML-konfiguration** på Azure-portalen.
    
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Recognize.

1. I Azure-portalen väljer du **Enterprise Applications**, väljer **Alla program**och väljer sedan **Identifiera**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Identifiera**i programlistan .

    ![Länken Identifiera i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-recognize-test-user"></a>Skapa identifiera testanvändare

För att Azure AD-användare ska kunna logga in på Recognize måste de etableras i Recognize. När det gäller Erkänna är etablering en manuell aktivitet.

Den här appen stöder inte SCIM-etablering men har en alternativ användarsynkronisering som avser användare. 

**Gör följande för att etablera ett användarkonto:**

1. Logga in på webbplatsen Recognize som administratör.

2. Klicka på **Meny**i det övre högra hörnet . Gå till **Företagsadministratör**.

3. I det vänstra navigeringsfönstret klickar du på **Inställningar**.

4. Utför följande steg i avsnittet **Användarsynkronisering.**
   
    ![Ny användare](./media/recognize-tutorial/tutorial_recognize_005.png "Ny användare")
   
    a. När **synkronisering aktiverad**väljer du **PÅ**.
   
    b. Välj **Microsoft/Office 365**som **välj synkroniseringsleverantör**.
   
    c. Klicka på **Kör användarsynkronisering**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Identifiera på åtkomstpanelen bör du automatiskt loggas in på den identifiera som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

