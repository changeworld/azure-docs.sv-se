---
title: Självstudiekurs - Konfigurera trafikroutning i undernät med Azure Traffic Manager
description: I den här självstudien beskrivs hur du konfigurerar Traffic Manager för att dirigera trafik från användarundernät till specifika slutpunkter.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: rohink
ms.openlocfilehash: 49e0bce6eea8fac32f49bb905c225e898e709af0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77136291"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Självstudiekurs: Dirigera trafik till specifika slutpunkter baserat på användarens undernät med Traffic Manager

Den här artikeln beskriver hur du konfigurerar trafikroutningsmetoden för undernät. Med trafikroutningsmetoden för **undernät** kan du mappa en uppsättning IP-adressintervall till specifika slutpunkter. När en begäran tas emot av Traffic Manager undersöks käll-IP-adressen för begäran och slutpunkten som är kopplad till den returneras.

I de här självstudierna används undernätsroutning, vilket innebär att trafik dirigeras antingen till en intern webbplats eller en produktionswebbplats beroende på IP-adressen för användarens fråga.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer som kör en grundläggande webbplats i IIS
> * Skapa två virtuella testdatorer för att visa hur Traffic Manager fungerar i praktiken
> * Konfigurera DNS-namn för de virtuella datorer som kör IIS
> * Skapa en Traffic Manager-profil för trafikroutning som baseras på användarens undernät
> * Lägg till VM-slutpunkter i Traffic Manager-profilen
> * Se hur Traffic Manager fungerar i praktiken

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill se hur Traffic Manager fungerar i praktiken behöver du använda följande i den här självstudien:

- två grundläggande webbplatser som körs i olika Azure-regioner – **USA, östra** (fungerar som intern webbplats) och **Europa, västra** (fungerar som produktionswebbplats).
- två virtuella testdatorer för att testa Traffic Manager – en virtuell dator i **USA, östra** och den andra virtuella datorn i **Europa, västra**.

De virtuella testdatorerna används för att illustrera hur Traffic Manager dirigerar trafik till den interna webbplatsen eller produktionswebbplatsen baserat på undernätet som användarfrågan kommer från.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två tjänstslutpunkter för Traffic Manager-profilen i två Azure-regioner. Att skapa två webbplatser omfattar följande steg:

1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i **USA, östra** och den andra i **Europa, västra**.
2. Installera IIS-servern på de båda virtuella datorerna och uppdatera standardwebbsidan som beskriver namnet på den virtuella datorn som en användare är ansluten när webbplatsen besöks.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser

I det här avsnittet skapar du två virtuella datorer *myIISVMEastUS* och *myIISVMWestEurope* i **azure-regionerna östra USA** och **Västeuropa.**

1. I det övre vänstra hörnet av Azure-portalen väljer du Skapa ett > **resursberäkningscenter** > **för Windows Server 2019**. **Create a resource**
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Subscription** > **Prenumerationsresursgrupp:** Välj **Skapa ny** och skriv sedan **myResourceGroupTM1**.
   - **Instansinformation** > **Virtuell datornamn:** Skriv *myIISVMEastUS*.
   - **Instance Details** > Region för**instansinformation**: Välj **östra USA**.
   - **Användarnamn för administratörskonto:** > **Username**Ange ett användarnamn som du väljer.
   - **Lösenord för administratörskonto:** > **Password**Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inkommande portregler** > **Offentliga inkommande portar**: Välj Tillåt valda **portar**.
   - **Regler för inkommande port** > **Välj inkommande portar**: Välj **RDP** och **HTTP** i rulllådan.

3. Välj fliken **Hantering** eller välj **Nästa: Diskar**och sedan **Nästa: Nätverk**, sedan **Nästa: Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Granska inställningarna och klicka sedan på **Skapa**.  
6. Följ stegen för att skapa en andra virtuell dator med namnet *myIISVMWestEurope*, med namnet **på resursgruppnamnet** *myResourceGroupTM2*, en **plats** i *Västeuropa*och alla andra inställningar på samma sätt som *myIISVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installera IIS och anpassa standardwebbsidan

I det här avsnittet installerar du IIS-servern på de två virtuella datorerna - *myIISVMEastUS* & *myIISVMWestEurope*och uppdaterar sedan standardwebbplatssidan. Den anpassade webbsidan visar namnet på den virtuella datorn som du ansluter till när du besöker webbplatsen från en webbläsare.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan klickar du sedan på *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. På serverskrivbordet navigerar du till **Windows Administrative Tools**>**Server Manager**.
7. Starta Windows PowerShell på VM *myIISVMEastUS*och använd följande kommandon för att installera IIS-servern och uppdatera standardfilen htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Stäng RDP-anslutningen med *myIISVMEastUS* VM.
9. Upprepa steg 1-6 med genom att skapa en RDP-anslutning med VM *myIISVMWestEurope* inom resursgruppen *myResourceGroupTM2* för att installera IIS och anpassa standardwebbsidan.
10. Starta Windows PowerShell på *myIISVMWestEurope* VM och använd följande kommandon för att installera IIS-servern och uppdatera standardfilen htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurera DNS-namnen för de virtuella datorer som kör IIS

Traffic Manager dirigerar användartrafik baserat på tjänstslutpunkternas DNS-namn. I det här avsnittet konfigurerar du DNS-namnen för IIS-servrarna - *myIISVMEastUS* och *myIISVMWestEurope*.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan väljer du sedan *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** under **DNS-namn** väljer du **Konfigurera**.
3. På sidan **Konfiguration**, under DNS-namnetiketten, lägger du till ett unikt namn och sedan väljer du **Spara**.
4. Upprepa steg 1-3 för den virtuella datorn som heter *myIISVMWestEurope* som finns i resursgruppen *myResourceGroupTM2.*

### <a name="create-test-vms"></a>Skapa virtuella testdatorer

I det här avsnittet skapar du en virtuell dator *(myVMEastUS* och *myVMWestEurope)* i varje Azure-region **(Östra USA** och **Västeuropa).** Du kommer att använda dessa virtuella datorer för att testa hur Traffic Manager dirigerar användartrafik baserat på undernätet i användarens fråga.

1. I det övre vänstra hörnet av Azure-portalen väljer du Skapa ett > **resursberäkningscenter** > **för Windows Server 2019**. **Create a resource**
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Resursgrupp** > **för**prenumeration: Välj **myResourceGroupTM1**.
   - **Instansinformation** > **Virtuell datornamn:** Skriv *myVMEastUS*.
   - **Instance Details** > Region för**instansinformation**: Välj **östra USA**.
   - **Användarnamn för administratörskonto:** > **Username**Ange ett användarnamn som du väljer.
   - **Lösenord för administratörskonto:** > **Password**Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inkommande portregler** > **Offentliga inkommande portar**: Välj Tillåt valda **portar**.
   - **Inkommande portregler** > **Välj inkommande portar:** Välj **RDP** i rulllådan.

3. Välj fliken **Hantering** eller välj **Nästa: Diskar**och sedan **Nästa: Nätverk**, sedan **Nästa: Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Granska inställningarna och klicka sedan på **Skapa**.  
6. Följ stegen för att skapa en andra virtuell dator med namnet *myVMWestEurope*, med ett **resursgruppnamn** för *myResourceGroupTM2*, en **plats** i *Västeuropa*och alla andra inställningar på samma sätt som *myVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil som gör det möjligt att returnera specifika slutpunkter baserat på käll-IP-adressen för begäran.

1. Välj **Skapa en resurs** > **Traffic** > **Manager-profil** > **Skapa**på den övre vänstra sidan av skärmen .
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och generera DNS-namnet, trafficmanager.net, som används för att öppna din Traffic Manager-profil.                                   |
    | Routningsmetod          | Välj routningsmetoden för **undernät**.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj **Befintlig** och ange *myResourceGroupTM1*. |
    | |                              |
    |

    ![Skapa en Traffic Manager-profil](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till de två virtuella datorer som kör IIS-servrarna - *myIISVMEastUS* & *myIISVMWestEurope* för att dirigera användartrafik baserat på undernätet i användarens fråga.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange, eller välj, följande information, acceptera standardinställningarna för de återstående inställningarna och välj sedan **OK:**

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Azure-slutpunkt                                   |
    | Namn           | myInternalWebSiteEndpoint                                        |
    | Målresurstyp           | Offentlig IP-adress                          |
    | Målresurs          | **Välj en offentlig IP-adress** för att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resurs** väljer du den offentliga IP-adressen med namnet *myIISVMEastUS-ip*. Det här är den offentliga IP-adressen för virtuella datorer med IIS i USA, östra.|
    |  Inställningar för undernätsroutning    |   Lägg till IP-adressen *för myVMEastUS* test VM. Alla användarfrågor som kommer från den här virtuella datorn dirigeras till *myInternalWebSiteEndpoint*.    |

4. Upprepa steg 2 och 3 för att lägga till en annan slutpunkt med namnet *myProdWebsiteEndpoint* för den offentliga IP-adressen *myIISVMWestEurope-ip* som är associerad med IIS-servern VM som heter *myIISVMWestEurope*. För **inställningar för undernätsdirigering**lägger du till IP-adressen för test-VM - *myVMWestEurope*. Alla användarfrågor från den här virtuella testdatorn dirigeras till slutpunkten – *myProdWebsiteEndpoint*.
5. När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet testar du hur Traffic Manager dirigerar användartrafik från ett visst undernät till en specifik slutpunkt. Om du vill se hur Traffic Manager fungerar i praktiken gör du följande:

1. Fastställ DNS-namnet för din Traffic Manager-profil.
2. Se hur Traffic Manager fungerar i praktiken:
    - Från test-VM *(myVMEastUS*) som finns i regionen **östra USA** bläddrar du i en webbläsare till DNS-namnet på din Traffic Manager-profil.
    - Från test-VM *(myVMWestEurope*) som finns i regionen **Västeuropa,** i en webbläsare, bläddra till DNS-namnet på din Traffic Manager-profil.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Fastställ DNS-namnet på Traffic Manager-profilen

I den här självstudien använder du för enkelhetens skull DNS-namnet för Traffic Manager-profilen för att besöka webbplatserna.

Så här kan du fastställa DNS-namnet i Traffic Manager-profilen:

1. I portalens sökfält söker du efter det **Traffic Manager-profil**namn som du skapade i föregående avsnitt. I det resultat som visas klickar du på Traffic Manager-profilen.
2. Klicka på **Översikt**.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. I Produktionsdistributioner konfigurerar du ett anpassat domännamn så att den pekar till Traffic Manager-domännamnet, med hjälp av en DNS CNAME-post.

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

I det här avsnittet får du se Traffic Manager i arbete.

1. Välj **Alla resurser** på menyn till vänster och klicka i listan över resurser på *myVMEastUS* som vinns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. I en webbläsare på den virtuella datorn *myVMEastUS* anger du DNS-namnet i Traffic Manager-profilen för at visa din webbplats. Eftersom DEN VM *myVMEastUS* IP-adress är associerad med slutpunkten *myInternalWebsiteEndpoint,* webbläsaren lanserar Test webbplats server - *myIISVMEastUS*.

7. Anslut sedan till VM *myVMWestEurope* som finns i **Västeuropa** med steg 1-5 och bläddra till Traffic Manager-profilens domännamn från den här virtuella datorn. Eftersom VM *myVMWestEurope* IP-adress är associerad med slutpunkten *myProductionWebsiteEndpoint,* webbläsaren lanserar Test webbplats server - *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Ta bort Traffic Manager-profilen

När resursgrupperna inte längre behövs kan du ta bort dem (**ResourceGroupTM1** och **ResourceGroupTM2**). Om du vill göra det markerar du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioritetsroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
