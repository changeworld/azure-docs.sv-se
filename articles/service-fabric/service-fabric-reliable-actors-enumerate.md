---
title: Räkna upp aktörer på Azure Service Fabric
description: Lär dig mer om uppräkning av tillförlitliga aktörer och deras metadata i ett Azure Service Fabric-program med hjälp av exempel.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 1516c9005a7c4dd0adcb279e9954e5f882c575c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645607"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Räkna upp driftsinfrastruktur för serviceinfrastruktur
Tjänsten Reliable Actors gör det möjligt för en klient att räkna upp metadata om de aktörer som tjänsten är värd för. Eftersom aktörstjänsten är en partitionerad tillståndskänslig tjänst utförs uppräkning per partition. Eftersom varje partition kan innehålla många aktörer returneras uppräkningen som en uppsättning växlingsresultat. Sidorna loopas över tills alla sidor läss. I följande exempel visas hur du skapar en lista över alla aktiva aktörer i en partition i en aktörstjänst:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Nästa steg
* [Hantering av aktörstillstånd](service-fabric-reliable-actors-state-management.md)
* [Skådespelarens livscykel och skräpinsamling](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentation för ACTORS API-referens](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [EXEMPELkod för .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
