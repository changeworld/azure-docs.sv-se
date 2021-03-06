---
title: Konfigurera textmeddelanden som din verifieringsmetod - Azure AD
description: Så här konfigurerar du sidan Säkerhetsinformation (förhandsgranskning) för att verifiera din identitet med textmeddelanden som verifieringsmetod.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 0525ee6d0ac5df61113e5a97c9812394323ffb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062361"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Konfigurera textmeddelanden som verifieringsmetod

Du kan följa dessa steg för att lägga till dina metoder för tvåfaktorsverifiering och återställning av lösenord. När du har konfigurerat detta första gången kan du gå tillbaka till sidan **Säkerhetsinformation** för att lägga till, uppdatera eller ta bort säkerhetsinformationen.

Om du uppmanas att konfigurera detta direkt efter att du har loggat in på ditt arbets- eller skolkonto läser du de detaljerade stegen i dialogrutan Konfigurera din säkerhetsinformation från snabb artikeln [för inloggningssidan.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Om du inte ser något telefonalternativ är det möjligt att din organisation inte tillåter att du använder det här alternativet för verifiering. I det här fallet måste du välja en annan metod eller kontakta organisationens supportavdelning för mer hjälp.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Konfigurera textmeddelanden från sidan Säkerhetsinformation

Beroende på organisationens inställningar kanske du kan använda textmeddelanden som en av dina metoder för säkerhetsinformation. Textmeddelandealternativet är en del av telefonalternativet, så du ställer in allt på samma sätt som för ditt telefonnummer, men i stället för att låta Microsoft ringa dig väljer du att använda ett textmeddelande.

>[!Note]
>Om du vill ta emot ett telefonsamtal i stället för ett sms följer du stegen i artikeln [Konfigurera säkerhetsinformation för att använda telefonsamtal.](security-info-setup-phone-number.md)

### <a name="to-set-up-text-messages"></a>Så här konfigurerar du textmeddelanden

1. Logga in på ditt arbets- eller https://myprofile.microsoft.com/ skolkonto och gå sedan till din sida.

    ![Sidan Min profil, med markerade länkar för säkerhetsinformation](media/security-info/securityinfo-myprofile-page.png)

2. Välj **Säkerhetsinformation** i det vänstra navigeringsfönstret eller från länken i blocket **Säkerhetsinformation** och välj sedan **Lägg till metod** på sidan **Säkerhetsinformation.**

    ![Sidan Säkerhetsinformation med markerat alternativ för Lägg till metod](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **Telefon** i listrutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod med telefonen markerad](media/security-info/securityinfo-myprofile-addphonetext.png)

4. På sidan **Telefon** skriver du telefonnumret till din mobila enhet, väljer **Text mig en kod**och väljer sedan **Nästa**.

    ![Lägga till telefonnummer och välj textmeddelanden](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Skriv koden som skickas till dig via sms till din mobila enhet och välj sedan **Nästa**.

    ![Lägga till telefonnummer och välj textmeddelanden](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Sidan ändras för att visa din framgång.

    ![Meddelande om lyckade svar, anslutning av telefonnumret, valet att ta emot textmeddelanden och ditt konto](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Säkerhetsinformationen uppdateras och du kan använda textmeddelanden för att verifiera din identitet när du använder tvåstegsverifiering eller återställning av lösenord. Om du vill göra textmeddelanden till standardmetoden läser du avsnittet [Ändra standardinformationsmetoden för säkerhetsinformation i](#change-your-default-security-info-method) den här artikeln.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Ta bort textmeddelanden från dina metoder för säkerhetsinformation

Om du inte längre vill använda textmeddelanden som en säkerhetsinformationsmetod kan du ta bort det från sidan **Säkerhetsinformation.**

>[!Important]
>Om du tar bort textmeddelanden av misstag, det finns inget sätt att ångra det. Du måste lägga till metoden igen i avsnittet [Konfigurera textmeddelanden](#set-up-text-messages-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-text-messaging"></a>Så här tar du bort textmeddelanden

1. På sidan **Säkerhetsinformation** väljer du länken **Ta bort** bredvid alternativet **Telefon.**

    ![Länk för att ta bort telefon- och sms-metoden från säkerhetsinformation](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Välj **Ja** i bekräftelserutan om du vill ta bort **telefonnumret.** När ditt telefonnummer har tagits bort tas det bort från säkerhetsinformationen och försvinner från sidan **Säkerhetsinformation.** Om **Telefon** är standardmetoden ändras standardinställningen till en annan tillgänglig metod.

## <a name="change-your-default-security-info-method"></a>Ändra standardmetoden för säkerhetsinformation

Om du vill att textmeddelanden ska vara den standardmetod som används när du loggar in på ditt arbets- eller skolkonto med tvåfaktorsverifiering eller för begäranden om återställning av lösenord kan du ange det från sidan **Säkerhetsinformation.**

### <a name="to-change-your-default-security-info-method"></a>Så här ändrar du standardmetoden för säkerhetsinformation

1. På sidan **Säkerhetsinformation** väljer du länken **Ändra** bredvid standardinformationen för **inloggningsmetod.**

    ![Ändra länk för standard inloggningsmetod](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Välj **Telefon - text (*_your_phone_number_*)** i listrutan med tillgängliga metoder och välj sedan **Bekräfta**.

    ![Välj metod för standard inloggning](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Standardmetoden som används för inloggningsändringar i **Telefon - text (*_your_phone_number_*).**

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhetsinformation

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad du försöker göra. Följande alternativ är tillgängliga:

- **Appen Autentiserare.** Ladda ned och använd en autentiseringsapp för att få antingen ett godkännandemeddelande eller en slumpmässigt genererad godkännandekod för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Mobil enhet eller arbetssamtal.** Ange ditt mobilenhetsnummer och få ett telefonsamtal för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **Säkerhetsnyckel.** Registrera din Microsoft-kompatibla säkerhetsnyckel och använd den tillsammans med en PIN-kod för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med en säkerhetsnyckel finns i [Konfigurera säkerhetsinformation för att använda en säkerhetsnyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress för arbete eller skola för att få ett e-postmeddelande om återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).

- **Säkerhetsfrågor.** Svara på några säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgängligt för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i artikeln [Konfigurera säkerhetsinformation för att använda säkerhetsfrågor.](security-info-setup-questions.md)

    >[!Note]
    >Om några av dessa alternativ saknas beror det troligen på att organisationen inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
