---
title: 'Självstudiekurs: Azure Active Directory-integrering med Qlik Sense Enterprise | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec9349d8ed330a00a64922a44f99910f9eeeb0df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136456"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Självstudiekurs: Integrera Qlik Sense Enterprise med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Qlik Sense Enterprise med Azure Active Directory (Azure AD). När du integrerar Qlik Sense Enterprise med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Qlik Sense Enterprise.
* Gör att användarna automatiskt loggas in på Qlik Sense Enterprise med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri provperiod [här.](https://azure.microsoft.com/pricing/free-trial/)
* Qlik Sense Enterprise enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. 
* Qlik Sense Enterprise stöder **SP** initierade SSO.
* Qlik Sense Enterprise stöder **just-in-time-etablering**

* När du har konfigurerat Qlik Sense Enterprise kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Lägga till Qlik Sense Enterprise från galleriet

För att konfigurera integreringen av Qlik Sense Enterprise med Azure AD behöver du lägga till Qlik Sense Enterprise från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Qlik Sense Enterprise** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Qlik Sense Enterprise** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Qlik Sense Enterprise med en testanvändare som heter **Britta Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Qlik Sense Enterprise.

Så här konfigurerar och testar du Azure AD SSO med Qlik Sense Enterprise:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Qlik Sense Enterprise SSO](#configure-qlik-sense-enterprise-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    * **[Skapa Qlik Sense Enterprise-testanvändare](#create-qlik-sense-enterprise-test-user)** – för att ha en motsvarighet till Britta Simon i Qlik Sense Enterprise som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet Hantera på sidan Hantera på sidan [Azure-portalen](https://portal.azure.com/)och välj Enkel inloggning på sidan **Manage** **Qlik Sense** **Enterprise-programintegration**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. Skriv en URL i textrutan **Sign-on URL** med följande mönster:`https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. Skriv en URL i textrutan **Identifierare** med något av följande mönster:

    | |
    |--|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com`|
    | `https://<Fully Qualified Domain Name>.qliksense.com`|
    | |

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: 

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med de faktisk inloggnings-URL, identifierare och svars-URL, som beskrivs senare i den här självstudien, eller kontakta [supportteamet för Qlik Sense Enterprise-klienten](https://www.qlik.com/us/services/support) för att hämta värdena. Standardporten för webbadresserna är 443 men du kan anpassa den efter organisationens behov.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** från de angivna alternativen enligt dina krav och sparar den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter Britta Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Qlik Sense Enterprise.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Qlik Sense Enterprise**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-qlik-sense-enterprise-sso"></a>Konfigurera Qlik Sense Enterprise SSO

1. Förbered Federation Metadata-XML-filen så att du kan ladda upp den till Qlik Sense-servern.

    > [!NOTE]
    > Innan du laddar upp IdP-metadata till Qlik Sense-servern måste filen redigeras med informationsborttagning så att funktionen mellan Azure AD och Qlik Sense-servern fungerar korrekt.

    ![QlikSense][qs24]

    a. Öppna den FederationMetaData.xml-fil som du har laddat ned från Azure-portalen i ett redigeringsprogram.

    b. Sök efter värdet **RoleDescriptor**.  Det finns fyra poster (två par inledande och avslutande elementtaggar).

    c. Ta bort RoleDescriptor-taggarna och all information mellan dem från filen.

    d. Spara filen och ha den till hands för användning senare i det här dokumentet.

2. Gå till Qlik Sense Qlik Management Console (QMC) som en användare som kan skapa konfigurationer för virtuell proxy.

3. I QMC klickar du på menykommandot **Virtual Proxies** (Virtuella proxyservrar).

    ![QlikSense][qs6]

4. Längst ned på skärmen klickar du på knappen **Skapa ny**.

    ![QlikSense][qs7]

5. Redigeringsskärmen för virtuell proxy visas.  På skärmens högra sida finns en meny där du kan göra konfigurationsalternativ synliga.

    ![QlikSense][qs9]

6. Med menyalternativet Identification (Identifiering) markerat anger du den identifierande informationen för konfigurationen av virtuell Azure-proxy.

    ![QlikSense][qs8]  

    a. Fältet **Beskrivning** är ett eget namn för konfigurationen av virtuell proxy.  Ange ett värde för en beskrivning.

    b. Fältet **Prefix** identifierar den slutpunkten för virtuell proxy för anslutning till Qlik Sense via enkel inloggning med Azure AD.  Ange ett för unikt prefixnamn för den här virtuella proxyn.

    c. **Session inactivity timeout (minutes)** (Tidsgräns för sessionsinaktivitet (minuter) är tidsgränsen för anslutningar via den här virtuella proxyn.

    d. **Session cookie header name** (Namn på huvud för sessionscookie) är det cookienamn som lagrar sessionsidentifieraren för den Qlik Sense-session som en användare får efter en lyckad autentisering.  Det här namnet måste vara unikt.

7. Klicka på menyalternativet Autentisering så att det visas.  Skärmen Autentisering visas.

    ![QlikSense][qs10]

    a. Listrutan **Anonymous access mode** (Läge för anonym åtkomst) avgör huruvida anonyma användare kan komma åt Qlik Sense genom den virtuella proxyn.  Standardalternativet är No anonymous user (Ingen anonym användare).

    b. Listan **Autentiseringsmetod** bestämmer vilket autentiseringsschema som används för den virtuella proxyn.  Välj SAML i listrutan.  Då visas fler alternativ.

    c. I **URI-fältet för SAML-värd** anger du det värdnamn som användare anger för att komma åt Qlik Sense via den här virtuella SAML-proxyn.  Värdnamnet är URI:n för Qlik Sense-servern.

    d. I **SAML-entitets-ID** anger du samma värde som angavs för URI-fältet för SAML-värd.

    e. **IdP-metadata för SAML** är den fil som redigerades tidigare i avsnittet om att **redigera Federation Metadata från Azure AD-konfiguration**.  **Innan du laddar upp IdP-metadata måste filen redigeras** med informationsborttagning så att funktionen mellan Azure AD och Qlik Sense-servern fungerar korrekt.  **Se anvisningarna ovan om filen inte har redigerats ännu.**  Om filen har redigerats klickar du på knappen Bläddra och väljer den redigereade metadatafilen för att ladda upp den till konfigurationen för virtuell proxy.

    f. Ange attributnamn eller schemareferens för det SAML-attribut som representerar det **Användar-ID** som Azure AD skickar till Qlik Sense-servern.  Information om schemareferens finns i på Azure-appens skärmar efter konfiguration.  För att använda namnattributet anger du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Ange värdet för den **användarkatalog** som kommer att kopplas till användare när de autentiserar till Qlik Sense-servern via Azure AD.  Hårdkodade värden måste omges av **hakparenteser []**.  För att använda ett attribut som skickas i Azure AD SAML-försäkran anger du namnet på attributet i den här textrutan **utan** hakparenteser.

    h. **SAML-signeringsalgoritmen** anger certifikatsignering för serviceprovidern (i det här fallet Qlik Sense-servern) för konfigurationen av virtuell proxy.  Om Qlik Sense-servern använder ett betrott certifikat som genererats med hjälp av Microsoft Enhanced RSA and AES Cryptographic Provider ändrar du SAML-signeringsalgoritmen till **SHA-256**.

    i. Avsnittet för SAML-attributmappning möjliggör ytterligare attribut såsom grupper som ska skickas till Qlik Sense för användning i säkerhetsregler.

8. Klicka på menyalternativet **LOAD BALANCING** (Belastningsutjämning) så att det visas.  Skärmen för belastningsutjämning visas.

    ![QlikSense][qs11]

9. Klicka på knappen **Add new server node** (Lägg till ny servernod), välj motornod eller noder som Qlik Sense skickar sessioner till i belastningsutjämningssyfte och klicka på knappen **Lägg till**.

    ![QlikSense][qs12]

10. Klicka på menyalternativet Avancerat så att det visas. Skärmen Avancerat visas.

    ![QlikSense][qs13]

    Listan Värd tillåt identifierar värdnamn som accepteras när du ansluter till Qlik Sense-servern.  **Ange det värdnamn som användare anger när de ansluter till Qlik Sense-servern.** Värdnamnet är samma värde som SAML-värd-URI utan https://.

11. Klicka på knappen **Använd**.

    ![QlikSense][qs14]

12. Klicka på OK för att acceptera det varningsmeddelande där det står att proxyservrar som länkas till den virtuella proxyn kommer att startas om.

    ![QlikSense][qs15]

13. Menyn för associerade objekt visas till höger på skärmen.  Klicka på menyalternativ **Proxyservrar**.

    ![QlikSense][qs16]

14. Proxy-skärmen visas.  Klicka på knappen **Länka** längst ned för att länka en proxy till den virtuella proxyn.

    ![QlikSense][qs17]

15. Välj den proxynod som kommer att stödja den här virtuella proxyanslutningen och klicka på knappen **Länka**.  När du har länkat visas proxyn under associerade proxyservrar.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Efter cirka fem till tio sekunder visas meddelandet Refresh QMC (Uppdatera QMC).  Klicka på knappen **Refresh QMC** (Uppdatera QMC).

    ![QlikSense][qs20]

17. När QMC uppdateras klickar du på menykommandot **Virtual proxies** (Virtuella proxyservrar). Den nya posten för virtuell SAML-proxy visas i tabellen på skärmen.  Klicka en gång på posten för virtuell proxy.

    ![QlikSense][qs51]

18. Längst ned på skärmen aktiveras knappen Download SP metadata (Ladda ned SP-metadata).  Klicka på knappen **Download SP metadata** (Ladda ned SP-metadata) för att spara metadata till en fil.

    ![QlikSense][qs52]

19. Öppna sp-metadata-filen.  Notera posten **entityID** och posten **AssertionConsumerService**.  De här värdena är likvärdiga med **identifieraren**, **inloggnings-URL** och **svars-URL** i Azure AD-programkonfigurationen. Klistra in dessa värden i avsnittet **Qlik Sense Enterprise Domain and URLs** (domäner och URL:er för Qlik Sense Enterprise) i Azure AD-programkonfigurationen. Om de inte matchar bör du ersätta dem i guiden för konfiguration av Azure AD-app.

    ![QlikSense][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Skapa Qlik Sense Enterprise-testanvändare

Qlik Sense Enterprise stöder **just-in-time-etablering**, Användare läggs automatiskt till i "ANVÄNDARE" i Qlik Sense Enterprise när de använder SSO-funktionen. Dessutom kan klienter använda QMC och skapa en UDC (User Directory Connector) för förifyllda användare i Qlik Sense Enterprise från deras LDAP val som Active Directory och andra.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Qlik Sense Enterprise på åtkomstpanelen bör du automatiskt loggas in på den Qlik Sense Enterprise som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
