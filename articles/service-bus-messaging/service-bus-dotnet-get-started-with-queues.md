---
title: Komma igång med Azure Service Bus-köer | Microsoft Docs
description: I den här självstudien skapar du .NET Core-konsolprogram för att skicka meddelanden till och ta emot meddelanden från en servicebusskö.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 5718106aee0e60d111398efdb839945c2c7a8a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471745"
---
# <a name="get-started-with-service-bus-queues"></a>Komma igång med Service Bus-köer
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
I den här självstudien skapar du .NET Core-konsolprogram för att skicka meddelanden till och ta emot meddelanden från en servicebusskö.

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Core SDK](https://www.microsoft.com/net/download/windows), version 2.0 eller senare.
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [msdn-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö att arbeta med följer du stegen i [Azure-portalen för att skapa en](service-bus-quickstart-portal.md) köartikel för Service Bus för att skapa en kö.

  - Läs den snabba översikten över servicebussköer.
  - Skapa ett servicebussnamnområde.
  - Hämta anslutningssträngen.
  - Skapa en servicebusskö.

## <a name="send-messages-to-the-queue"></a>Skicka meddelanden till kön

Skriv ett C#-konsolprogram med Visual Studio för att skicka meddelanden till kön.

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio och skapa ett nytt **Console App-projekt (.NET Core)** för C#. I det här exemplet namnges appen *CoreSenderApp*.

### <a name="add-the-service-bus-nuget-package"></a>Lägga till Service Bus-NuGet-paketet

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
1. Välj **Bläddra**. Sök efter och välj **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**.
1. Välj **Installera** för att slutföra installationen och stäng sedan NuGet Package Manager.

    ![Välj ett NuGet-paket][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Skriva kod för att skicka meddelanden till kön

1. I *Program.cs*lägger du `using` till följande satser högst upp i namnområdesdefinitionen före klassdeklarationen:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Deklarera `Program` följande variabler i klassen:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Ange anslutningssträngen för namnområdet som `ServiceBusConnectionString` variabel. Ange ditt könamn.

1. Ersätt `Main()` metoden med följande **asynkronmetod.** `Main` Den anropar den `SendMessagesAsync()` metod som du ska lägga till i nästa steg för att skicka meddelanden till kön. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. Direkt efter `MainAsync()` metoden lägger `SendMessagesAsync()` du till följande metod som gör arbetet `numberOfMessagesToSend` med att skicka antalet meddelanden som anges av (för närvarande inställd på 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

Så här ska din *Program.cs* fil se ut.

```csharp
namespace CoreSenderApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
                }
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
            }
        }
    }
}
```

Kör programmet och kontrollera Azure-portalen.

Välj namnet på kön i fönstret **översiktsfönster** för namnområde om du vill visa kön **Essentials**.

![Mottagna meddelanden med antal och storlek][queue-message]

Värdet **för antal aktiva meddelanden** för kön är nu **10**. Varje gång du kör den här avsänarappan utan att hämta meddelandena ökar det här värdet med 10.

Den aktuella storleken på kön ökar värdet **AKTUELL** i **Essentials** varje gång appen lägger till meddelanden i kön.

I nästa avsnitt beskrivs hur du hämtar dessa meddelanden.

## <a name="receive-messages-from-the-queue"></a>Ta emot meddelanden från kön

Om du vill ta emot de meddelanden du skickade skapar du ett annat **Console App-program (.NET Core).** Installera **paketet Microsoft.Azure.ServiceBus** NuGet, som du gjorde för avsänarprogrammet.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Skriva kod för att ta emot meddelanden från kön

1. I *Program.cs*lägger du `using` till följande satser högst upp i namnområdesdefinitionen före klassdeklarationen:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Deklarera `Program` följande variabler i klassen:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Ange anslutningssträngen för namnområdet som `ServiceBusConnectionString` variabel. Ange ditt könamn.

1. Ersätt metoden `Main()` med följande kod:

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. Direkt efter `MainAsync()` metoden lägger du till följande metod, som registrerar meddelandehanteraren och tar emot de meddelanden som skickas av avsändningsprogrammet:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. Direkt efter den föregående metoden lägger du till följande `ProcessMessagesAsync()`-metod för att bearbeta de mottagna meddelandena:

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. Slutligen lägger du till följande metod för att hantera alla undantag som kan uppstå:

    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }
    ```

Så här ska *din Program.cs* fil se ut:

```csharp
namespace CoreReceiverApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
            // to avoid unnecessary exceptions.
        }

        static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
        {
            Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
            var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
            Console.WriteLine("Exception context for troubleshooting:");
            Console.WriteLine($"- Endpoint: {context.Endpoint}");
            Console.WriteLine($"- Entity Path: {context.EntityPath}");
            Console.WriteLine($"- Executing Action: {context.Action}");
            return Task.CompletedTask;
        }
    }
}
```

Kör programmet och kontrollera portalen igen. Antalet **aktiva meddelanden** och **AKTUELLA** värden är nu **0**.

![Kö efter att meddelanden har tagits emot][queue-message-receive]

Grattis! Du har nu skapat en kö, skickat en uppsättning meddelanden till den kön och tagit emot dessa meddelanden från samma kö.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Med Service Bus Explorer kan användare enkelt ansluta till ett servicebussnamnområde och administrera meddelandeentiteter. Verktyget innehåller avancerade funktioner som import-/exportfunktioner eller möjligheten att testa ämnen, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar.

## <a name="next-steps"></a>Nästa steg

Kolla in våra [GitHub-databaser med exempel](https://github.com/Azure/azure-service-bus/tree/master/samples) som visar några av de mer avancerade funktionerna i meddelandetjänsten i Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive-in-essentials.png

