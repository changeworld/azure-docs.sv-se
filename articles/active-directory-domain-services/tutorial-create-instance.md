---
title: Självstudiekurs - Skapa en Azure Active Directory Domain Services-instans | Microsoft-dokument
description: I den här självstudien får du lära dig hur du skapar och konfigurerar en Azure Active Directory Domain Services-instans med Azure-portalen.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 1bd5248e0a6a6c7c569c85e8c1af3e30f8b7f9e4
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474208"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Självstudiekurs: Skapa och konfigurera en Azure Active Directory Domain Services-instans

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domäntjänster som domänanslutning, grupprincip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibel med Active Directory i Windows Server. Du använder dessa domäntjänster utan att distribuera, hantera och korrigera domänkontrollanter själv. Azure AD DS integreras med din befintliga Azure AD-klientorganisation. Med den här integreringen kan användare logga in med sina företagsautentiseringsuppgifter och du kan använda befintliga grupper och användarkonton för att skydda åtkomsten till resurser.

Du kan skapa en hanterad domän med standardkonfigurationsalternativ för nätverk och synkronisering, eller [manuellt definiera dessa inställningar][tutorial-create-instance-advanced]. Den här självstudien visar hur du använder standardalternativ för att skapa och konfigurera en Azure AD DS-instans med Azure-portalen.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Förstå DNS-krav för en hanterad domän
> * Skapa en Azure AD DS-instans
> * Aktivera hashsynkronisering för lösenord

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* Du behöver globala administratörsbehörighet i din Azure AD-klient för att aktivera Azure AD DS. *global administrator*
* Du *Contributor* behöver deltagarbehörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.

Även om det inte krävs för Azure AD DS, rekommenderas att [konfigurera självbetjäningslösenordsåterställning (SSPR)][configure-sspr] för Azure AD-klienten. Användare kan ändra sitt lösenord utan SSPR, men SSPR hjälper om de glömmer sitt lösenord och behöver återställa det.

> [!IMPORTANT]
> När du har skapat en Azure AD DS-hanterad domän kan du inte flytta instansen till en annan resursgrupp, virtuellt nätverk, prenumeration osv. Var noga med att välja den lämpligaste prenumerationen, resursgruppen, regionen och det virtuella nätverket när du distribuerar Azure AD DS-instansen.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar och konfigurerar du Azure AD DS-instansen med Azure-portalen. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-instance"></a>Skapa en instans

Så här startar du guiden **Aktivera Azure AD Domain Services:**

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
1. Ange *Domain Services* i sökfältet och välj sedan Azure AD Domain *Services* i sökförslagen.
1. På sidan Azure AD Domain Services väljer du **Skapa**. Guiden **Aktivera Azure AD Domain Services** startas.
1. Välj den **Azure-prenumeration** där du vill skapa den hanterade domänen.
1. Välj den **resursgrupp** som den hanterade domänen ska tillhöra. Välj att **skapa nya** eller välja en befintlig resursgrupp.

När du skapar en Azure AD DS-instans anger du ett DNS-namn. Det finns några funderingar när du väljer det här DNS-namnet:

* **Inbyggt domännamn:** Som standard används katalogens inbyggda domännamn (ett *.onmicrosoft.com* suffix). Om du vill aktivera säker LDAP-åtkomst till den hanterade domänen via internet kan du inte skapa ett digitalt certifikat för att skydda anslutningen till den här standarddomänen. Microsoft äger *domänen .onmicrosoft.com,* så att en certifikatutfärdarcertifikatutfärdaren inte utfärdar ett certifikat.
* **Anpassade domännamn:** Den vanligaste metoden är att ange ett anpassat domännamn, vanligtvis ett som du redan äger och är dirigerbart. När du använder en dirigerbar, anpassad domän kan trafiken flöda korrekt efter behov för att stödja dina program.
* **Icke-dirigerbara domänsuffix:** Vi rekommenderar vanligen att du undviker ett suffix som inte är dirigerbart domännamn, till exempel *contoso.local*. Suffixet *.local* är inte dirigerbart och kan orsaka problem med DNS-upplösning.

> [!TIP]
> Om du skapar ett eget domännamn bör du ta hand om befintliga DNS-namnområden. Vi rekommenderar att du använder ett domännamn som är skilt från ett befintligt Azure- eller lokalt DNS-namnutrymme.
>
> Om du till exempel har ett befintligt DNS-namnutrymme *contoso.com*skapar du en Azure AD DS-hanterad domän med det anpassade domännamnet *aaddscontoso.com*. Om du behöver använda säker LDAP måste du registrera dig och äga det här anpassade domännamnet för att generera de certifikat som krävs.
>
> Du kan behöva skapa ytterligare DNS-poster för andra tjänster i din miljö eller villkorliga DNS-vidarebefordrare mellan befintliga DNS-namnutrymmen i din miljö. Om du till exempel kör en webbserver som är värd för en webbplats med rot-DNS-namnet kan det finnas namnkonflikter som kräver ytterligare DNS-poster.
>
> I dessa självstudier och instruktionsartiklar används den anpassade domänen *för aaddscontoso.com* som ett kort exempel. Ange ditt eget domännamn i alla kommandon.

Följande DNS-namnbegränsningar gäller också:

* **Begränsningar för domänprefix:** Du kan inte skapa en hanterad domän med ett prefix som är längre än 15 tecken. Prefixet för det angivna domännamnet (till exempel *aaddscontoso* i *aaddscontoso.com* domännamnet) måste innehålla 15 eller färre tecken.
* **Konflikter i nätverksnamn:** DNS-domännamnet för den hanterade domänen bör inte redan finnas i det virtuella nätverket. Kontrollera särskilt följande scenarier som skulle leda till en namnkonflikt:
    * Om du redan har en Active Directory-domän med samma DNS-domännamn i det virtuella Azure-nätverket.
    * Om det virtuella nätverket där du planerar att aktivera den hanterade domänen har en VPN-anslutning till ditt lokala nätverk. I det här fallet se till att du inte har en domän med samma DNS-domännamn i ditt lokala nätverk.
    * Om du har en befintlig Azure-molntjänst med det namnet i det virtuella Azure-nätverket.

Fyll i fälten i *fönstret Grunderna* i Azure-portalen för att skapa en Azure AD DS-instans:

1. Ange ett **DNS-domännamn** för den hanterade domänen, med beaktande av föregående punkter.
1. Välj den **Azure-plats** där den hanterade domänen ska skapas. Om du väljer en region som stöder tillgänglighetszoner distribueras Azure AD DS-resurserna mellan zoner för ytterligare redundans.

    Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner.

    Det finns inget för dig att konfigurera för Azure AD DS som ska distribueras över zoner. Azure-plattformen hanterar automatiskt zonfördelningen av resurser. Mer information och om du vill se regionens tillgänglighet finns [i Vad är tillgänglighetszoner i Azure?][availability-zones]

1. **SKU** avgör prestanda, säkerhetskopieringsfrekvens och maximalt antal skogsförtroende som du kan skapa. Du kan ändra SKU:n när den hanterade domänen har skapats om ditt företag kräver eller krav ändras. Mer information finns i [Azure AD DS SKU-begrepp][concepts-sku].

    För den här *Standard* självstudien väljer du Standard-SKU.
1. En *skog* är en logisk konstruktion som används av Active Directory Domain Services för att gruppera en eller flera domäner. Som standard skapas en Azure AD DS-hanterad domän som en *användarskog.* Den här typen av skog synkroniserar alla objekt från Azure AD, inklusive alla användarkonton som skapats i en lokal AD DS-miljö. En *resursskog* synkroniserar bara användare och grupper som skapats direkt i Azure AD. Resursskogar är för närvarande i förhandsgranskning. Mer information *Resource* om resursskogar, inklusive varför du kan använda en och hur du skapar skogsförtroende med lokala AD DS-domäner, finns i [översikt över Azure AD DS-resursskogar][resource-forests].

    För den här självstudien väljer du att skapa en *användarskog.*

    ![Konfigurera grundläggande inställningar för en Azure AD Domain Services-instans](./media/tutorial-create-instance/basics-window.png)

Om du snabbt vill skapa en Azure AD DS-hanterad domän kan du välja **Granska + skapa** för att acceptera ytterligare standardkonfigurationsalternativ. Följande standardvärden konfigureras när du väljer det här alternativet för att skapa:

* Skapar ett virtuellt nätverk med namnet *aadds-vnet* som använder IP-adressintervallet *10.0.2.0/24*.
* Skapar ett undernät med namnet *aadds-undernät* med IP-adressintervallet *10.0.2.0/24*.
* Synkroniserar *alla* användare från Azure AD till den Hanterade Azure AD DS-domänen.

Välj **Granska + skapa** om du vill acceptera dessa standardkonfigurationsalternativ.

## <a name="deploy-the-managed-domain"></a>Distribuera den hanterade domänen

På sidan **Sammanfattning** i guiden granskar du konfigurationsinställningarna för den hanterade domänen. Du kan gå tillbaka till valfritt steg i guiden för att göra ändringar. Om du vill distribuera om en Azure AD DS-hanterad domän till en annan Azure AD-klient på ett konsekvent sätt med hjälp av dessa konfigurationsalternativ kan du också **hämta en mall för automatisering**.

1. Om du vill skapa den hanterade domänen väljer du **Skapa**. En anteckning visas att vissa konfigurationsalternativ som DNS-namn eller virtuellt nätverk inte kan ändras när Azure AD DS-hanterad har skapats. Om du vill fortsätta väljer du **OK**.
1. Processen att etablera din hanterade domän kan ta upp till en timme. Ett meddelande visas i portalen som visar förloppet för din Azure AD DS-distribution. Välj meddelandet om du vill se detaljerade framsteg för distributionen.

    ![Meddelande i Azure-portalen för den pågående distributionen](./media/tutorial-create-instance/deployment-in-progress.png)

1. Sidan läses in med uppdateringar om distributionsprocessen, inklusive skapandet av nya resurser i katalogen.
1. Välj din resursgrupp, till exempel *myResourceGroup,* och välj sedan din Azure AD DS-instans i listan över Azure-resurser, till exempel *aaddscontoso.com*. Fliken **Översikt** visar att den hanterade domänen för närvarande *distribuerar*. Du kan inte konfigurera den hanterade domänen förrän den är helt etablerad.

    ![Domain Services-status under etableringstillståndet](./media/tutorial-create-instance/provisioning-in-progress.png)

1. När den hanterade domänen är helt etablerad visar fliken **Översikt** domänstatus som *Kör*.

    ![Domain Services-status när den har etablerats](./media/tutorial-create-instance/successfully-provisioned.png)

Den hanterade domänen är associerad med din Azure AD-klientorganisation. Under etableringsprocessen skapar Azure AD DS två företagsprogram med namnet *Domain Controller Services* och *AzureActiveDirectoryDomainControllerServices* i Azure AD-klienten. Dessa företagsprogram behövs för att serva din hanterade domän. Ta inte bort dessa program.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Uppdatera DNS-inställningarna för det virtuella Azure-nätverket

Med Azure AD DS har distribuerats konfigurerar du nu det virtuella nätverket så att andra anslutna virtuella datorer och program kan använda den hanterade domänen. Om du vill tillhandahålla den här anslutningen uppdaterar du DNS-serverinställningarna för det virtuella nätverket så att de pekar på de två IP-adresser där Azure AD DS distribueras.

1. Fliken **Översikt** för den hanterade domänen visar några **obligatoriska konfigurationssteg**. Det första konfigurationssteget är att uppdatera DNS-serverinställningarna för det virtuella nätverket. När DNS-inställningarna är korrekt konfigurerade visas inte längre det här steget.

    Adresserna i listan är domänkontrollanter som ska användas i det virtuella nätverket. I det här exemplet är dessa adresser *10.0.2.4* och *10.0.2.5*. Du kan senare hitta dessa IP-adresser på fliken **Egenskaper.**

    ![Konfigurera DNS-inställningar för ditt virtuella nätverk med IP-adresserna för Azure AD Domain Services](./media/tutorial-create-instance/configure-dns.png)

1. Om du vill uppdatera DNS-serverinställningarna för det virtuella nätverket väljer du knappen **Konfigurera.** DNS-inställningarna konfigureras automatiskt för det virtuella nätverket.

> [!TIP]
> Om du har valt ett befintligt virtuellt nätverk i föregående steg får alla virtuella datorer som är anslutna till nätverket bara de nya DNS-inställningarna efter en omstart. Du kan starta om virtuella datorer med Azure-portalen, Azure PowerShell eller Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Aktivera användarkonton för Azure AD DS

För att autentisera användare på den hanterade domänen behöver Azure AD DS lösenordshashar i ett format som är lämpligt för NT LAN Manager (NTLM) och Kerberos-autentisering. Azure AD genererar inte eller lagrar lösenordsh hashar i det format som krävs för NTLM- eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhetsskäl lagrar Azure AD inte heller några lösenordsuppgifter i klartextform. Därför kan Azure AD inte automatiskt generera dessa NTLM- eller Kerberos-lösenordshashar baserat på användarnas befintliga autentiseringsuppgifter.

> [!NOTE]
> När det är korrekt konfigurerat lagras de användbara lösenordsharen i azure AD DS-hanterade domänen. Om du tar bort den hanterade Azure AD DS-domänen tas alla lösenordshar som lagras vid den tidpunkten också bort. Synkroniserad autentiseringsuppgifter i Azure AD kan inte återanvändas om du senare skapar en Azure AD DS-hanterad domän - du måste konfigurera om synkroniseringen av lösenordsh hash-synkronisering för att lagra lösenords hasharen igen. Tidigare domänanslutna virtuella datorer eller användare kan inte autentiseras omedelbart – Azure AD måste generera och lagra lösenordshÃrna i den nya Azure AD DS-hanterade domänen. Mer information finns i [Synkroniseringsprocessen för lösenord hash för Azure AD DS och Azure AD Connect][password-hash-sync-process].

Stegen för att generera och lagra dessa lösenordshashar är olika för molnbaserade användarkonton som skapats i Azure AD kontra användarkonton som synkroniseras från din lokala katalog med Azure AD Connect. Ett endast molnbaserat användarkonto är ett konto som skapats i Azure AD-katalogen med antingen Azure Portal eller Azure AD PowerShell-cmdletar. Dessa användarkonton synkroniseras inte från en lokal katalog. I den här självstudien ska vi arbeta med ett grundläggande användarkonto endast för molnet. Mer information om de ytterligare steg som krävs för att använda Azure AD Connect finns i [Synkronisera lösenordshÃ¥ringar för användarkonton som synkroniserats frÃ¥r lokala AD till din hanterade domän][on-prem-sync].

> [!TIP]
> Om din Azure AD-klient har en kombination av molnanvändare och användare från din lokala AD måste du slutföra båda stegen.

För användarkonton med enbart molnet måste användarna ändra sina lösenord innan de kan använda Azure AD DS. Den här lösenordsändringsprocessen gör att lösenordsh hashar för Kerberos- och NTLM-autentisering genereras och lagras i Azure AD. Du kan antingen upphöra att gälla lösenorden för alla användare i klienten som behöver använda Azure AD DS, vilket tvingar en lösenordsändring vid nästa inloggning eller instruera dem att manuellt ändra sina lösenord. För den här självstudien ska vi manuellt ändra ett användarlösenord.

Innan en användare kan återställa sitt lösenord måste Azure [AD-klienten konfigureras för återställning av lösenord med självbetjäning][configure-sspr].

Om du vill ändra lösenordet för en användare som endast är molnet måste användaren utföra följande steg:

1. Gå till sidan Azure AD [https://myapps.microsoft.com](https://myapps.microsoft.com)Access Panel på .
1. I det övre högra hörnet väljer du ditt namn och väljer sedan **Profil** på rullgardinsmenyn.

    ![Välj profil](./media/tutorial-create-instance/select-profile.png)

1. Välj **Ändra lösenord**på sidan **Profil** .
1. På sidan **Ändra lösenord** anger du ditt befintliga (gamla) lösenord och anger och bekräftar sedan ett nytt lösenord.
1. Välj **Skicka**.

Det tar några minuter efter att du har ändrat ditt lösenord för att det nya lösenordet ska kunna användas i Azure AD DS och logga in på datorer som är anslutna till den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Förstå DNS-krav för en hanterad domän
> * Skapa en Azure AD DS-instans
> * Lägga till administrativa användare i domänhantering
> * Aktivera användarkonton för Azure AD DS och generera lösenordshÃ¤ringar

Innan du domänanslutning till virtuella datorer och distribuera program som använder den Hanterade Azure AD DS-domänen konfigurerar du ett virtuellt Azure-nätverk för programarbetsbelastningar.

> [!div class="nextstepaction"]
> [Konfigurera virtuella Azure-nätverk för programarbetsbelastningar för att använda din hanterade domän](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
