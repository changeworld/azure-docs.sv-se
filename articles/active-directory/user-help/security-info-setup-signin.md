---
title: Konfigurera din säkerhetsinformation (förhandsversion) från en inloggningsfråga – Azure AD
description: Så här konfigurerar du säkerhetsinformation (förhandsversion) för ditt arbets- eller skolkonto när du har fått information från organisationens inloggningssida.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 08/05/2019
ms.author: curtand
ms.openlocfilehash: c216dbfef99422fc49fde774dc57d5cbcc9f879a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77063993"
---
# <a name="set-up-your-security-info-preview-from-a-sign-in-prompt"></a>Konfigurera säkerhetsinformation (förhandsgranskning) från en inloggningsfråga

Du kan följa de här stegen om du uppmanas att konfigurera säkerhetsinformation omedelbart efter att du har loggat in på ditt arbets- eller skolkonto.

Det här meddelandet visas endast om du inte har konfigurerat den säkerhetsinformation som krävs av din organisation. Om du tidigare har konfigurerat din säkerhetsinformation men vill göra ändringar kan du följa stegen i de olika metodbaserade instruktionsartiklarna. Mer information finns i [översikten om att lägga till eller uppdatera säkerhetsinformation](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Säkerhetsverifiering kontra autentisering om återställning av lösenord

Metoder för säkerhetsinformation används för både tvåfaktorssäkerhetsverifiering och för återställning av lösenord. Dock kan inte alla metoder användas för båda.

| Metod | Används för |
| ------ | -------- |
| Autentiseringsapp | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| SMS | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Telefonsamtal | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Säkerhetsnyckel | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| E-postkonto | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |
| Säkerhetsfrågor | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |

## <a name="sign-in-to-your-work-or-school-account"></a>Logga in på ditt arbets- eller skolkonto

När du har loggat in på ditt arbets- eller skolkonto konto visas en uppmaning om att du behöver ange mer information innan du tillåts komma åt ditt konto.

![Uppmaning som ber om mer information](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Konfigurera säkerhetsinformation med hjälp av guiden

Följ de här stegen för att konfigurera säkerhetsinformation för ditt arbets-eller skolkonto från uppmaningen.

>[!Important]
>Det här är bara ett exempel på processen. Beroende på organisationens krav kan administratören ha konfigurerat olika verifieringsmetoder som du behöver konfigurera under den här processen. I det här exemplet kräver vi två metoder, Microsoft Authenticator-appen och ett mobiltelefonnummer för verifieringssamtal eller SMS.

1. När du har valt **Nästa** från uppmaningen öppnas **guiden om att hålla ditt konto säkert**, som visar den första metod som din administratör eller organisation kräver att du konfigurerar. I det här exemplet är det Microsoft Authenticator-appen.

   > [!Note]
   > Om du vill använda en annan autentiseringsapp än Microsoft Authenticator-appen väljer du länken **Jag vill använda en annan autentiseringsapp**.
   >
   > Om din organisation låter dig välja en annan metod än autentiseringsappen kan du välja länken **Jag vill konfigurera en annan metod**.

    ![Guiden för att hålla ditt konto säkert, som visar sidan för nedladdning av autentiseringsappen](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Välj **Ladda ned nu** för att ladda ned och installera Microsoft Authenticator-appen på din mobila enhet, och välj sedan **Nästa**. Mer information om hur du laddar ned och installerar appen finns i [Ladda ned och installera Microsoft Authenticator-appen](user-help-auth-app-download-install.md).

    ![Guiden för att hålla ditt konto säkert, som visar autentiserarsidan för att konfigurera kontot](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Ha sidan **Konfigurera ditt konto** öppen medan du konfigurerar Microsoft Authenticator-appen på din mobila enhet.

4. Öppna Microsoft Authenticator-appen, välj att tillåta meddelanden (om du uppmanas), välj **Lägg till konto** från ikonen **Customize and control** (Anpassa och styr) uppe till höger och välj sedan **Arbets- eller skolkonto**.

    >[!Note]
    >Om det är första gången du konfigurerar Microsoft Authenticator-appen kan du få en uppmaning om du vill tillåta appen att komma åt kameran (iOS) eller låta appen ta bilder och spela in video (Android). Du måste välja **Tillåt** så att autentiseringsappen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera autentiseringsappen, men du måste lägga till kodinformationen manuellt. Information om hur du lägger till koden manuellt finns i [Lägg till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

5. Gå tillbaka till sidan **Konfigurera ditt konto** på datorn och välj sedan **Nästa**.

    Sidan **Skanna QR-koden** visas.

    ![Skanna QR-koden med hjälp av Authenticator-appen](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Skanna den angivna koden med Microsoft Authenticator-appens QR-kodläsare, som infördes på din mobila enhet efter att du skapade arbets- eller skolkontot i steg 5.

    Authenticator-appen bör lägga till ditt arbets- eller skolkonto utan att någon ytterligare information krävs från dig. Om QR-kodläsaren inte kan läsa koden kan du välja **den kan inte skanna QR-avbildningen** och manuellt ange koden och webbadressen i Microsoft Authenticator-appen. Mer information om att lägga till en kod manuellt finns i [Lägga till ett konto i appen manuellt](user-help-auth-app-add-account-manual.md).

7. Välj **Nästa** på sidan **Skanna QR-koden** på datorn.

    Ett meddelande skickas till Microsoft Authenticator-appen på din mobila enhet för att testa ditt konto.

    ![Testa ditt konto med Authenticator-appen](media/security-info/securityinfo-prompt-test-app.png)

8. Godkänn meddelandet i Microsoft Authenticator-appen och välj sedan **Nästa**.

    ![Meddelande om att det lyckades; appen och ditt konto ansluts](media/security-info/securityinfo-prompt-auth-app-success.png)

    Din säkerhetsinformation uppdateras till att använda Microsoft Authenticator-appen som standard för att verifiera din identitet vid användning av tvåstegsverifiering eller återställning av lösenord.

9. På sidan för **telefonkonfiguration** väljer du om du vill få ett SMS eller ett telefonsamtal och väljer sedan **Nästa**. För det här exemplet använder vi SMS, så du måste använda ett telefonnummer för en enhet som kan ta emot SMS.

    ![Börja konfigurera telefonnumret för SMS](media/security-info/securityinfo-prompt-text-msg.png)

    Ett SMS skickas till ditt telefonnummer. Om föredrar att få ett telefonsamtal är processen densamma. Men då får du ett telefonsamtal med instruktioner i stället för ett SMS.

10. Ange den kod som visas i det SMS som skickades till din mobila enhet och välj sedan **Nästa**.

    ![Testa ditt konto med SMS:et](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Granska meddelandet om att det lyckades och välj sedan **Klar**.

    ![Meddelande om att det lyckades](media/security-info/securityinfo-prompt-call-answered-success.png)

    Din säkerhetsinformation uppdateras till att använda SMS som reservmetod för att verifiera din identitet vid användning av tvåstegsverifiering eller återställning av lösenord.

12. Granska sidan **Success** (Lyckades) för att kontrollera att du har konfigurerat både Microsoft Authenticator-appen och en telefonmetod (SMS eller telefonsamtal) för din säkerhetsinformation, och välj sedan **Klar**.

    ![Sidan om att guiden har slutförts](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Om din organisation kräver att du använder applösenord kan du se ytterligare ett avsnitt i den här guiden, där du kan konfigurera det. Om du ser ett tredje avsnitt, som kallas **Applösenord,** måste du fylla i det innan du kan slutföra guiden. Steg om hur du lägger till ett applösenord finns i avsnittet [Hantera applösenord](#manage-your-app-passwords) i den här artikeln.

### <a name="manage-your-app-passwords"></a>Hantera dina applösenord

Vissa appar, till exempel Outlook 2010, stöder inte tvåstegsverifiering. Den här bristen på stöd innebär att om du använder tvåstegsverifiering i din organisation fungerar inte appen. För att komma runt det här problemet kan du skapa ett automatiskt uppringda lösenord som ska användas med varje app som inte är en webbläsare, separat från ditt vanliga lösenord.

>[!Note]
>Om du inte ser det här alternativet i guiden betyder det att administratören inte har konfigurerat det. Om detta inte är konfigurerat, men du vet att du behöver använda applösenord, kan du följa stegen i [sidan Konfigurera applösenord från sidan Säkerhetsinformation (förhandsversion).](security-info-app-passwords.md)

När du använder applösenord är det viktigt att komma ihåg:

- Applösenord skapas automatiskt och anges bara en gång per app.

- Det finns en gräns på 40 lösenord per användare. Om du försöker skapa ett efter den gränsen uppmanas du att ta bort ett befintligt lösenord innan du får skapa det nya.

- Använd ett applösenord per enhet, inte per app. Skapa till exempel ett enda lösenord för alla appar på din bärbara dator och sedan ett annat enda lösenord för alla appar på skrivbordet.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Så här lägger du till applösenord i inloggningsguiden

1. När du har avslutat föregående avsnitt i guiden väljer du **Nästa** och fyller i avsnittet **Applösenord.**

2. Skriv namnet på appen som behöver lösenordet, till exempel `Outlook 2010`, och välj sedan **Nästa**.

    ![Lägga till appens lösenordsnamn i guiden](media/security-info/app-password-app-password.png)

3. Kopiera lösenordskoden från **skärmen Applösenord** och klistra in den i området **Lösenord** i appen (i det här exemplet Outlook 2010).

    ![Sidan Applösenord, med lösenord för kopiering](media/security-info/app-password-copy-password.png)

4. När du har kopierat lösenordet och klistrat in det i appen går du tillbaka till den här guiden för att kontrollera att all information om inloggningsmetoden är korrekt och väljer sedan **Klar**.

    ![Sidan Applösenord, med meddelande om slutförande](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Nästa steg

- Om du vill ändra, ta bort, eller uppdatera standardmetoder för säkerhetsinformation kan du läsa:

    - [Konfigurera säkerhetsinformation för en autentiseringsapp](security-info-setup-auth-app.md).

    - [Konfigurera säkerhetsinformation för SMS](security-info-setup-text-msg.md).

    - [Konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

    - [Konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).

    - [Ställ in säkerhetsinformation för att använda fördefinierade säkerhetsfrågor](security-info-setup-questions.md).

- Information om hur du loggar in med din angivna metod finns i [Så loggar du in](user-help-sign-in.md).

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
