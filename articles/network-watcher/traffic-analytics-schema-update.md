---
title: Schemauppdatering för Azure Traffic Analytics – mars 2020 | Microsoft-dokument
description: Exempel på frågor med nya fält i Traffic Analytics-schemat.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 4fe981576e3f6e58b0886d9c0d2eb2915d8b7720
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396610"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Exempel på frågor med nya fält i Traffic Analytics-schemat (schemauppdatering för augusti 2019)

[Loggschemat för Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) innehåller följande nya fält: **SrcPublicIPs_s**, **DestPublicIPs_s**, **NSGRule_s**. De nya fälten innehåller information om käll- och mål-IP-adresser och de förenklar frågor.

Under de närmaste månaderna kommer följande äldre fält att inaktuell: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.

Följande tre exempel visar hur du ersätter de gamla fälten med de nya.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>Exempel 1: fälten VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s och PublicIPs_s

Vi behöver inte dra slutsatsen att käll- och målfall från **fältet FlowDirection_s** för AzurePublic- och ExternalPublic-flöden. Det kan också vara olämpligt att använda **fältet FlowDirection_s** för en virtuell nätverksinstallation.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>Exempel 2: NSGRules_s fält

Det gamla fältet använde formatet:

<Indexvärde 0)>|<NSG_>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

Vi sammanställer inte längre data över en nätverkssäkerhetsgrupp (NSG). I det uppdaterade schemat innehåller **NSGList_s** bara en NSG. Även **NSGRules** innehåller endast en regel. Vi tog bort den komplicerade formateringen här och i andra fält som visas i exemplet.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>Exempel 3: FlowCount_d fältet

Eftersom vi inte klubbdata över NSG, **är FlowCount_d** helt enkelt:

**AllowedInFlows_d****DeniedInFlows_d** + **AllowedOutFlows_d** + **DeniedOutFlows_d**  + 

Endast ett av de fyra fälten kommer att vara nonzero. De andra tre fälten blir noll. Fälten fylls i för att ange status och antal i nätverkskortet där flödet fångades.

För att illustrera dessa villkor:

- Om flödet tilläts fylls ett av de tillåtna fälten i.
- Om flödet nekades fylls ett av de "nekade" prefixerade fälten i.
- Om flödet var inkommande fylls ett av suffixfälten för InFlows_d.
- Om flödet var utgående fylls ett av suffixfälten för OutFlows_d.

Beroende på förhållandena vet vi vilket av de fyra fälten som kommer att befolkas.

## <a name="next-steps"></a>Nästa steg

- Mer om du vill få svar på vanliga frågor och svar finns i [Vanliga frågor och svar om Traffic Analytics](traffic-analytics-faq.md).
- Information om funktioner finns i [Dokumentationen](traffic-analytics.md)till Traffic Analytics .
