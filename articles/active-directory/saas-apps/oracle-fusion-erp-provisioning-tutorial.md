---
title: 'Självstudiekurs: Konfigurera Oracle Fusion ERP för automatisk användaretablering med Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetableringa användarkonton till Oracle Fusion ERP.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 08a82be5a11ba7b81cd1939fc5b386c161c43480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061207"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Självstudiekurs: Konfigurera Oracle Fusion ERP för automatisk användaretablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Oracle Fusion ERP och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetableras användare och/eller grupper till Oracle Fusion ERP.

> [!NOTE]
>  Den här självstudien beskriver en anslutningsapp som skapats ovanpå Azure AD-tjänsten för användaretablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande i förhandsversion. Mer information om de allmänna användningsvillkoren för förhandsversionen av Microsoft Azure finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klient
* En [Oracle Fusion ERP hyresgäst](https://www.oracle.com/applications/erp/).
* Ett användarkonto i Oracle Fusion ERP med administratörsbehörighet.

## <a name="assign-users-to-oracle-fusion-erp"></a>Tilldela användare till Oracle Fusion ERP 
Azure Active Directory använder ett koncept som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användaretablering synkroniseras endast användare och/eller grupper som har tilldelats ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användaretablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Oracle Fusion ERP. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Oracle Fusion ERP genom att följa instruktionerna här:
 
* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Viktiga tips för att tilldela användare till Oracle Fusion ERP 

 * Vi rekommenderar att en enda Azure AD-användare tilldelas Oracle Fusion ERP för att testa konfigurationen för automatisk användaretablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Oracle Fusion ERP måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelningsdialogrutan. Användare med rollen Standardåtkomst är undantagna från etablering.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Konfigurera Oracle Fusion ERP för etablering

Innan du konfigurerar Oracle Fusion ERP för automatisk användaretablering med Azure AD måste du aktivera SCIM-etablering på Oracle Fusion ERP.

1. Logga in på [din Administrationskonsol för Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Klicka på Navigator högst upp till vänster. Under **Verktyg**väljer du **Säkerhetskonsol**.

    ![Oracle Fusion ERP Lägg TILL SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Navigera till **Användare**.
    
    ![Oracle Fusion ERP Lägg TILL SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Spara användarnamn och lösenord för administratörsanvändarkontot som du använder för att logga in på Administrationskonsolen Oracle Fusion ERP. Dessa värden måste anges i fälten **Administratörsanvändarnamn** och **lösenord** på fliken Etablering i ditt Oracle Fusion ERP-program i Azure-portalen.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Lägg till Oracle Fusion ERP från galleriet

Om du vill konfigurera Oracle Fusion ERP för automatisk användaretablering med Azure AD måste du lägga till Oracle Fusion ERP från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Så här lägger du till Oracle Fusion ERP från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i **[Azure-portalen](https://portal.azure.com)** i den vänstra navigeringspanelen .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Enterprise-program**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Oracle Fusion ERP**, väljer **Oracle Fusion ERP** i resultatpanelen.

    ![Oracle Fusion ERP i resultatlistan](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Konfigurera automatisk användaretablering till Oracle Fusion ERP 

I det här avsnittet får du hjälp med stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Oracle Fusion ERP baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Oracle Fusion ERP genom att följa instruktionerna i [Oracle Fusion ERP Single sign-on tutorial](oracle-fusion-erp-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användaretablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Mer information om Oracle Fusion ERP:s SCIM-slutpunkt finns [i REST API för vanliga funktioner i Oracle Applications Cloud](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Så här konfigurerar du automatisk användaretablering för Fuze i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram**och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Oracle Fusion ERP**.

    ![Oracle Fusion ERP-länk i listan med program](common/all-applications.png)

3. Välj fliken **Etablering.**

    ![Fliken Etablering](common/provisioning.png)

4. Ställ in **etableringsläget** på **Automatiskt**.

    ![Fliken Etablering](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du i `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` **klient-URL.** Ange administratörens användarnamn och lösenord som hämtats tidigare i fälten **Administratörsanvändarnamn** och **lösenord.** Klicka på **Testa anslutning** mellan Azure AD och Oracle Fusion ERP. 

    ![Oracle Fusion ERP Lägg TILL SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. I fältet **E-post för meddelanden** anger du e-postadressen till en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan - **Skicka ett e-postmeddelande när ett fel inträffar**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-användare till Oracle Fusion ERP**.

    ![Oracle Fusion ERP Lägg TILL SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Granska användarattributen som synkroniseras från Azure AD till Oracle Fusion ERP i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha användarkontona i Oracle Fusion ERP för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Oracle Fusion ERP Lägg TILL SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory-grupper med Oracle Fusion ERP**.

    ![Oracle Fusion ERP-gruppkartor](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Oracle Fusion ERP i avsnittet **Attributmappning.** De attribut som valts som **matchande** egenskaper används för att matcha grupperna i Oracle Fusion ERP för uppdateringsåtgärder. Välj knappen **Spara** om du vill utföra eventuella ändringar.

    ![Oracle Fusion ERP-gruppattribut](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudiefilatkursen För att visa omfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten för Oracle Fusion ERP ändrar **du etableringsstatusen** till **På** i avsnittet **Inställningar.**

    ![Etableringsstatus växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Oracle Fusion ERP genom att välja önskade värden i **Scope** i avsnittet **Inställningar.**

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats** i Scope i avsnittet **Inställningar.** Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda avsnittet **Synkroniseringsinformation** för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Oracle Fusion ERP.

    Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Begränsningar för anslutning

* Oracle Fusion ERP stöder endast grundläggande autentisering för sin SCIM-slutpunkt.
* Oracle Fusion ERP stöder inte gruppetablering.
* Roller i Oracle Fusion ERP mappas till grupper i Azure AD. Om du vill tilldela roller till användare i Oracle Fusion ERP från Azure AD måste du tilldela användare till önskade Azure AD-grupper som är uppkallade efter roller i Oracle Fusion ERP.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera etablering av användarkonton för Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
