---
title: Använda Azure AD i Azure Kubernetes-tjänsten
description: Lär dig hur du använder Azure AD i Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: b121830192a2b88185bbbbc9a92934e51b32a61c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114653"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integrera Azure AD i Azure Kubernetes-tjänsten (förhandsversion)

> [!Note]
> Befintliga AKS v1-kluster med AD-integrering påverkas inte av den nya AKS v2-upplevelsen.

Azure AD-integrering med AKS v2 är utformad för att förenkla Azure AD-integreringen med AKS v1-upplevelse, där användarna var tvungna att skapa en klientapp, en serverapp och krävde att Azure AD-klienten beviljar katalogläsningsbehörigheter. I den nya versionen hanterar AKS-resursleverantören klient- och serverapparna åt dig.

## <a name="limitations"></a>Begränsningar

* Du kan för närvarande inte uppgradera ett befintligt Azure AD-aktiverat AKS v1-kluster till v2-upplevelsen.

> [!IMPORTANT]
> AKS-förhandsgranskningsfunktioner är tillgängliga på opt-in-basis med självbetjäning. Förhandsvisningar tillhandahålls "i nu och "som tillgängligt" och är undantagna från servicenivåavtalen och den begränsade garantin. AKS-förhandsvisningar omfattas delvis av kundsupport på bästa sätt. Därför är dessa funktioner inte avsedda för produktionsanvändning. Mer information finns i följande supportartiklar:
>
> - [Aks-supportpolicyer](support-policies.md)
> - [Vanliga frågor och svar om Azure-support](faq.md)

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurser installerade:

- Azure CLI, version 2.2.0 eller senare
- Aks-förhandsversionen 0.4.38-tillägget
- Kubectl med en minimiversion av [1,18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Om du vill installera/uppdatera tillägget aks-preview eller senare använder du följande Azure CLI-kommandon:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Så här installerar du kubectl:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Följ [dessa instruktioner](https://kubernetes.io/docs/tasks/tools/install-kubectl/) för andra operativsystem.

> [!CAUTION]
> När du har registrerat en funktion på en prenumeration kan du för närvarande inte avregistrera den funktionen. När du aktiverar vissa förhandsversionsfunktioner kan standardvärden användas för alla AKS-kluster som skapas efteråt i prenumerationen. Aktivera inte förhandsgranskningsfunktioner på produktionsprenumerationer. Använd i stället en separat prenumeration för att testa förhandsgranskningsfunktioner och samla in feedback.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Det kan ta flera minuter innan statusen visas som **registrerad**. Du kan kontrollera registreringsstatusen med kommandot [az-funktionslista:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

När statusen visas som registrerad uppdaterar `Microsoft.ContainerService` du registreringen av resursprovidern med kommandot [az provider register:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Skapa ett AKS-kluster med Azure AD aktiverat

Du kan nu skapa ett AKS-kluster med hjälp av följande CLI-kommandon.

Skapa först en Azure-resursgrupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Skapa sedan ett AKS-kluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Kommandot ovan skapar ett AKS-kluster med tre noder, men användaren, som skapade klustret, som standard, är inte medlem i en grupp som har åtkomst till det här klustret. Den här användaren måste skapa en Azure AD-grupp, lägga till sig som medlem i gruppen och sedan uppdatera klustret enligt nedan. Följ instruktionerna [här](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

När du har skapat en grupp och lagt till dig själv (och andra) som medlem kan du uppdatera klustret med Azure AD-gruppen med följande kommando

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Alternativt, om du först skapar en grupp och lägger till medlemmar, kan du aktivera Azure AD-gruppen vid skapande tid med hjälp av följande kommando,

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

En lyckad skapande av ett Azure AD v2-kluster har följande avsnitt i svarstexten
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Klustret skapas inom några minuter.

## <a name="access-an-azure-ad-enabled-cluster"></a>Få tillgång till ett Azure AD-aktiverat kluster
Så här hämtar du administratörsautentiseringsuppgifterna för att komma åt klustret:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Använd nu kommandot kubectl get noder för att visa noder i klustret:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Så här hämtar du användarautentiseringsuppgifter för att komma åt klustret:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Följ instruktionerna för att logga in.

Du får: **Du måste vara inloggad på servern (Obehörig)**

Användaren ovan får ett felmeddelande eftersom användaren inte är en del av en grupp som har åtkomst till klustret.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure AD-rollbaserad åtkomstkontroll][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
