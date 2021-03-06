---
title: Skapa en klientmodell (förhandsgranskning) - Taltjänst
titleSuffix: Azure Cognitive Services
description: Generera automatiskt en säker, kompatibel klientmodell (anpassat tal med Office 365-data) som använder dina Office 365-data för att ge optimal taligenkänning för organisationsspecifika termer.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: a83ed5c9cec994c1bc4cadd5cf6208c159823658
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469025"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Självstudiekurs: Skapa en klientmodell (förhandsgranskning)

Klientmodell (anpassat tal med Office 365-data) är en opt-in-tjänst för Office 365-företagskunder som automatiskt genererar en anpassad taligenkänningsmodell från organisationens Office 365-data. Modellen är optimerad för tekniska termer, jargong och människors namn, allt på ett säkert och kompatibelt sätt.

> [!IMPORTANT]
> Om din organisation registrerar sig med hjälp av klientmodelltjänsten kan Taltjänsten komma åt organisationens språkmodell. Modellen genereras från e-postmeddelanden och dokument för offentliga office 365-grupper, som kan ses av alla i organisationen. Organisationens Office 365-administratör kan aktivera eller inaktivera användningen av den organisationsomfattande språkmodellen från administrationsportalen för Office 365.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Registrera dig i klientmodellen med hjälp av administrationscentret för Microsoft 365
> * Skaffa en talprenumerationsnyckel
> * Skapa en klientmodell
> * Distribuera en klientmodell
> * Använda din klientmodell med Tal-SDK

## <a name="enroll-in-the-tenant-model-service"></a>Registrera dig i tjänsten Klientmodell

Innan du kan distribuera din klientmodell måste du vara registrerad i klientmodelltjänsten. Registreringen slutförs i Microsoft 365-administrationscentret och kan endast utföras av Microsoft 365-administratören.

1. Logga in på [Administrationscenter för Microsoft 365](https://admin.microsoft.com).

1. I den vänstra rutan väljer du **Inställningar**och väljer sedan **Inställningar** på den kapslade menyn och väljer sedan **Azure Speech Services** i huvudfönstret.

   ![Fönstret "Tjänster & tillägg"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Markera kryssrutan **Tillåt den organisationsomfattande språkmodellen** och välj sedan **Spara ändringar**.

   ![Fönstret Azure Speech Services](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Så här inaktiverar du klientmodellinstansen:
1. Upprepa föregående steg 1 och 2.
1. Avmarkera kryssrutan **Tillåt den organisationsomfattande språkmodellen** och välj sedan **Spara ändringar**.

## <a name="get-a-speech-subscription-key"></a>Skaffa en talprenumerationsnyckel

Om du vill använda klientmodellen med Tal-SDK behöver du en talresurs och dess associerade prenumerationsnyckel.

1. Logga in på [Azure-portalen](https://aka.ms/azureportal).
1. Välj **Skapa en resurs**.
1. Skriv **Tal**i rutan **Sök** .
1. Välj **Tal**i resultatlistan och välj sedan **Skapa**.
1. Följ instruktionerna på skärmen för att skapa din resurs. Kontrollera att:
   * **Platsen** är inställd på antingen **eastus** eller **westus**.
   * **Prisnivån** är inställd på **S0**.
1. Välj **Skapa**.

   Efter några minuter skapas resursen. Prenumerationsnyckeln är tillgänglig i avsnittet **Översikt** för din resurs.

## <a name="create-a-language-model"></a>Skapa en språkmodell

När administratören har aktiverat klientmodell för din organisation kan du skapa en språkmodell som baseras på dina Office 365-data.

1. Logga in på [Talstudion](https://speech.microsoft.com/).
1. Längst upp till höger väljer du **Inställningar** (kugghjulsikon) och väljer sedan **Inställningar för klientmodell**.

   ![Länken "Klientmodellinställningar"](media/tenant-language-model/tenant-language-settings.png)

   Speech Studio visar ett meddelande som låter dig veta om du är kvalificerad att skapa en klientmodell.

   > [!NOTE]
   > Office 365 företagskunder i Nordamerika kan skapa en klientmodell (engelska). Om du är kundlåsbox, kundnyckel eller Office 365-kund är den här funktionen inte tillgänglig. Information om du är kundlåstör eller kundnyckelkund finns i:
   > * [Customer Lockbox](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Kundnyckel](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365-regeringen](https://www.microsoft.com/microsoft-365/government)

1. Välj **Anmäl dig**.

   När din klientmodell är klar får du ett e-postmeddelande med en bekräftelse med ytterligare instruktioner.

## <a name="deploy-your-tenant-model"></a>Distribuera din klientmodell

När klientmodellinstansen är klar distribuerar du den genom att göra följande:

1. Välj knappen Visa modell i e-postmeddelandet **visa.** Eller logga in [på Speech Studio](https://speech.microsoft.com/).
1. Längst upp till höger väljer du **Inställningar** (kugghjulsikon) och väljer sedan **Inställningar för klientmodell**.

   ![Länken "Klientmodellinställningar"](media/tenant-language-model/tenant-language-settings.png)

1. Välj **Distribuera**.

   När din modell har distribuerats ändras statusen till *Distribuerad*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Använda din klientmodell med Tal-SDK

Nu när du har distribuerat din modell kan du använda den med Tal-SDK. I det här avsnittet använder du exempelkod för att anropa Taltjänst med hjälp av Azure Active Directory (Azure AD) autentisering.

Låt oss titta på koden du använder för att ringa Tal SDK i C#. I det här exemplet utför du taligenkänning med hjälp av din klientmodell. Den här guiden förutsätter att din plattform redan har konfigurerats. Om du behöver installationshjälp läser du [Snabbstart: Identifiera tal, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Kopiera den här koden till projektet:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Därefter måste du återskapa och köra projektet från kommandoraden. Innan du kör kommandot uppdaterar du några parametrar genom att göra följande:

1. Ersätt `<Username>` `<Password>` och med värdena för en giltig klientanvändare.
1. Ersätt `<Subscription-Key>` med prenumerationsnyckeln för din Talresurs. Det här värdet är tillgängligt i avsnittet **Översikt** för din Talresurs i [Azure-portalen](https://aka.ms/azureportal).
1. Ersätt `<Endpoint-Uri>` med följande slutpunkt. Se till att `{your region}` du ersätter med den region där talresursen skapades. Dessa regioner `westus`stöds: `westus2`, `eastus`och . Din regioninformation är tillgänglig i avsnittet **Översikt för** din Talresurs i [Azure-portalen](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Kör följande kommando:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

I den här självstudien har du lärt dig hur du använder Office 365-data för att skapa en anpassad taligenkänningsmodell, distribuera den och använda den med Tal-SDK.

## <a name="next-steps"></a>Nästa steg

* [Talstudio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)
