---
title: Skillnader i Azure Service Fabric mellan Linux och Windows
description: Skillnader mellan Azure Service Fabric i Linux och Azure Service Fabric i Windows.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: sfrev
ms.openlocfilehash: c064291491110b17c172a561afbd1e65d6c08d06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748526"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Skillnader mellan Service Fabric i Linux och Windows

Det finns vissa funktioner som inte finns i Linux ännu, utan bara i Windows. Till slut kommer alla funktioner att finnas på båda plattformarna. Skillnaderna minskar med varje ny version. Följande skillnader finns mellan de senaste tillgängliga versionerna.

* Envoy (Reverse Proxy) är i förhandsversion på Linux
* Fristående installationsprogram är inte tillgängligt ännu i Linux
* Omdirigering av konsol (stöds inte i Linux- eller Windows-produktionskluster)
* FAS (Fault Analysis Service) i Linux
* DNS-tjänst för Service Fabric-tjänster (DNS-tjänst stöds för containrar i Linux)
* Motsvarande CLI-kommandon för vissa Powershell-kommandon (se lista nedan, de flesta gäller endast fristående kluster)
* [Skillnader i loggimplementering som kan påverka skalbarheten](service-fabric-concepts-scalability.md#choosing-a-platform)

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>PowerShell-cmdletar som inte fungerar mot ett Linux Service Fabric-kluster

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy

## <a name="next-steps"></a>Nästa steg

* [Förbereda utvecklingsmiljön i Linux](service-fabric-get-started-linux.md)
* [Förbereda utvecklingsmiljön i OSX](service-fabric-get-started-mac.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ditt första CSharp-program i Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Använd Service Fabric CLI för att hantera dina program](service-fabric-application-lifecycle-sfctl.md)
