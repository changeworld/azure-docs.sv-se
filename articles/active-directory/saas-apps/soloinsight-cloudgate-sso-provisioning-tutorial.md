---
title: 'Självstudiekurs: Konfigurera Soloinsight-CloudGate SSO för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Soloinsight-CloudGate SSO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063203"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Soloinsight-CloudGate SSO för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Soloinsight-CloudGate SSO och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Soloinsight-CloudGate SSO.

> [!NOTE]
> Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i offentlig förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* [En Soloinsight-CloudGate SSO-klientorganisation](https://www.soloinsight.com/)
* Ett användarkonto i Soloinsight-CloudGate SSO med administratörsbehörighet.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Tilldela användare till Soloinsight-CloudGate SSO

Azure Active Directory använder ett koncept som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Soloinsight-CloudGate SSO. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Soloinsight-CloudGate SSO genom att följa instruktionerna här:
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Viktiga tips för att tilldela användare till Soloinsight-CloudGate SSO

* Vi rekommenderar att en enda Azure AD-användare tilldelas Soloinsight-CloudGate SSO för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Soloinsight-CloudGate SSO måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen **Standardåtkomst** är undantagna från etablering.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Konfigurera Soloinsight-CloudGate SSO för etablering

1. Logga in på [din Soloinsight-CloudGate SSO-administratörskonsol](https://soloinsight.sigateway.com/login). Navigera till **Administration > Systeminställningar**.

    ![Soloinsight-CloudGate SSO-administratörskonsol](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Navigera till **Allmänt**.

    ![Soloinsight-CloudGate SSO Lägg till SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Bläddra ned till slutet av sidan för att hämta **klientadressen** och **den hemliga token**. Kopiera den **hemliga token**. Det här värdet anges i fältet Hemlig token på fliken Etablering i ditt Soloinsight-CloudGate SSO-program i Azure-portalen.

    ![Soloinsight-CloudGate SSO Skapa token](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Lägg till Soloinsight-CloudGate SSO från galleriet

Innan du konfigurerar Soloinsight-CloudGate SSO för automatisk användaretablering med Azure AD måste du lägga till Soloinsight-CloudGate SSO från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Soloinsight-CloudGate SSO från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Soloinsight-CloudGate SSO**, väljer **Soloinsight-CloudGate SSO** på resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Soloinsight-CloudGate SSO i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Konfigurera automatisk användaretablering till Soloinsight-CloudGate SSO 

Det här avsnittet guidar dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Soloinsight-CloudGate SSO baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Soloinsight-CloudGate SSO , följ instruktionerna i [Soloinsight-CloudGate SSO Enkel inloggningskurs](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Soloinsight-CloudGate SSO i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Soloinsight-CloudGate SSO** i programlistan.

    ![Soloinsight-CloudGate SSO-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://sigateway.com/scim/v2/sync/serviceproviderconfig` **klient-URL.** Ange värdet **för SCIM-autentiseringstoken** som hämtats tidigare i **hemlig token**. Klicka på **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Soloinsight-CloudGate SSO. Om anslutningen misslyckas kontrollerar du att ditt Soloinsight-CloudGate SSO-konto har administratörsbehörighet och försök igen.

    ![Url till klient + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Soloinsight-CloudGate SSO**.

    ![Användarmappningar för Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Granska användarattributen som synkroniseras från Azure AD till Soloinsight-CloudGate SSO i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Soloinsight-CloudGate SSO för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Användarattribut för Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper med Soloinsight-CloudGate SSO**.

    ![Soloinsight-CloudGate SSO-gruppmappningar](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Soloinsight-CloudGate SSO i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Soloinsight-CloudGate SSO för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Soloinsight-CloudGate SSO-gruppattribut](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Soloinsight-CloudGate SSO ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Soloinsight-CloudGate SSO genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Soloinsight-CloudGate SSO.

Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

