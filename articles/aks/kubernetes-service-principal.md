---
title: Tjänstens huvudnamn för Azure Kubernetes Service (AKS)
description: Skapa och hantera ett tjänstobjekt för Azure Active Directory för ett kluster i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 523f08ddbf22e175af5b0604b04d4a2460ffd634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259427"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Tjänstens huvudnamn med Azure Kubernetes Service (AKS)

Om ett AKS-kluster ska kunna interagera med Azure-API:er måste det ha ett [Azure Active Directory-huvudnamn för tjänsten][aad-service-principal]. Tjänstens huvudnamn krävs för att dynamiskt skapa och hantera andra Azure-resurser som en Azure-lastbalanserare eller containerregister (ACR).

Den här artikeln visar hur du skapar och använder ett tjänstens huvudnamn för AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

För att skapa ett Azure AD-huvudnamn för tjänsten måste du ha behörighet att registrera ett program med din Azure AD-klientorganisation, samt behörighet att tilldela programmet till en roll i din prenumeration. Om du inte har de behörigheter som du behöver kan du be din Azure AD- eller prenumerationsadministratör att tilldela de nödvändiga behörigheterna eller att skapa ett tjänstens huvudnamn att använda med AKS-klustret.

Om du använder ett tjänsthuvudnamn från en annan Azure AD-klient finns det ytterligare överväganden kring de behörigheter som är tillgängliga när du distribuerar klustret. Du kanske inte har rätt behörighet att läsa och skriva kataloginformation. Mer information finns [i Vilka är standardanvändarbehörigheterna i Azure Active Directory?][azure-ad-permissions]

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Skapa och använda ett tjänstens huvudnamn automatiskt

När du skapar ett AKS-kluster i Azure Portal eller använder kommandot [az aks create][az-aks-create] så kan Azure automatiskt generera ett tjänstens huvudnamn.

Ett tjänstens huvudnamn har inte angetts i följande Azure CLI-exempel. I det här scenariot skapar Azure CLI ett tjänstens huvudnamn för AKS-klustret. Om ditt Azure-konto ska kunna slutföra åtgärden måste det ha rätt behörigheter för att skapa ett tjänstens huvudnamn.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Skapa ett tjänstens huvudnamn manuellt

Använd kommandot [az ad sp create-for-rbac][az-ad-sp-create] om du vill skapa ett tjänstens huvudnamn med Azure CLI manuellt. I följande exempel visas förhindrar parametern `--skip-assignment` eventuella ytterligare tilldelningar från att göras:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

De utdata som genereras påminner om de i följande exempel. Anteckna dina egna `appId` och `password`. De här värdena används när du skapar ett AKS-kluster i nästa avsnitt.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Ange ett tjänstens huvudnamn för ett AKS-kluster

Om du vill använda ett befintligt huvudnamn för tjänsten när du skapar ett AKS-kluster med kommandot [az aks create][az-aks-create] så använd parametrarna `--service-principal` och `--client-secret` för att ange `appId` och `password` från utdata från kommandot [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Om du använder ett befintligt tjänsthuvudnamn med anpassad hemlighet kontrollerar du att hemligheten inte är längre än 190 byte.

Om du distribuerar ett AKS-kluster med hjälp av Azure Portal, så välj **Konfigurera tjänstens huvudnamn** i dialogrutan **Skapa Kubernetes-kluster** på sidan *Autentisering*. Välj **Använd befintlig** och ange följande värden:

- **Klient-ID för tjänstens huvudnamn** är *appId*
- **Klienthemligheten för tjänstens huvudnamn** är värdet *lösenord*

![Bild som illustrerar hur du navigerar till Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegera åtkomst till andra Azure-resurser

Tjänstens huvudnamn för AKS-klustret kan användas för att komma åt andra resurser. Om du till exempel vill distribuera AKS-klustret till ett befintligt Azure-virtuellt nätverksundernät eller ansluta till Azure Container Registry (ACR), måste du delegera åtkomst till dessa resurser till tjänstens huvudnamn.

Om du vill delegera behörigheter skapar du en rolltilldelning med kommandot [az role assignment create.][az-role-assignment-create] Tilldela `appId` till ett visst scope, till exempel en resursgrupp eller virtuell nätverksresurs. En roll definierar därefter vilka behörigheter som tjänstens huvudnamn har på resursen, som visas i följande exempel:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

En resurs `--scope` måste vara ett fullständigt resurs-ID, som */subscriptions/\<guid\>/resourceGroups/myResourceGroup* eller */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

Följande avsnitt beskriver vanliga delegeringar som du kan behöva göra.

### <a name="azure-container-registry"></a>Azure Container Registry

Om du använder Azure Container Registry (ACR) som behållaravbildningsarkiv måste du bevilja behörigheter till tjänstens huvudnamn för aks-klustret för att läsa och hämta avbildningar. För närvarande är den rekommenderade konfigurationen att använda az [aks skapa][az-aks-create] eller [az aks uppdatera][az-aks-update] kommandot för att integrera med ett register och tilldela lämplig roll för tjänstens huvudnamn. Detaljerade steg finns [i Autentisera med Azure Container Registry från Azure Kubernetes Service][aks-to-acr].

### <a name="networking"></a>Nätverk

Du kan använda avancerade nätverk där det virtuella nätverket och undernätet eller offentliga IP-adresser finns i en annan resursgrupp. Tilldela behörigheter till någon av följande uppsättningar:

- Skapa en [anpassad roll][rbac-custom-role] och definiera följande rollbehörigheter:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/undernät/skrivning*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- Eller tilldela [Nätverksdeltagare][rbac-network-contributor] en inbyggd roll i undernätet i det virtuella nätverket

### <a name="storage"></a>Lagring

Du kan behöva åtkomst till befintliga diskresurser i en annan resursgrupp. Tilldela behörigheter till någon av följande uppsättningar:

- Skapa en [anpassad roll][rbac-custom-role] och definiera följande rollbehörigheter:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Eller tilldela den inbyggda rollen [Lagringskontodeltagare][rbac-storage-contributor] i resursgruppen

### <a name="azure-container-instances"></a>Azure Container Instances

Om du använder Virtual Kubelet för att integrera med AKS och väljer att köra Azure Container Instances (ACI) i resursgruppen separat från AKS-klustret måste AKS-huvudnamn beviljas *deltagarbehörigheter* för ACI-resursgruppen.

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Tänk på följande när du använder AKS och Azure AD-tjänstens huvudnamn.

- Tjänstobjektet för Kubernetes är en del av klusterkonfigurationen. Men använd inte identiteten för att distribuera klustret.
- Som standard är tjänstens huvudreferenser giltiga i ett år. Du kan [uppdatera eller rotera tjänstens huvudreferenser][update-credentials] när som helst.
- Varje tjänstobjekt är associerat med ett Azure AD-program. Tjänsthuvudhuvudet för ett Kubernetes-kluster kan associeras med alla *https://www.contoso.org/example*giltiga Azure AD-programnamn (till exempel: ). URL:en för programmet behöver inte vara en verklig slutpunkt.
- När du anger **Klient-ID** för tjänstens huvudnamn använder du värdet för `appId`.
- På virtuella agentnoder i Kubernetes-klustret lagras autentiseringsuppgifterna för tjänstens huvudnamn i filen`/etc/kubernetes/azure.json`
- Om du använder kommandot [az aks create][az-aks-create] för att generera tjänstobjektet automatiskt skrivs autentiseringsuppgifterna för tjänstobjektet till filen `~/.azure/aksServicePrincipal.json` på den dator som används för att köra kommandot.
- Om du inte specifikt skickar ett tjänsthuvudnamn i ytterligare AKS CLI-kommandon används standardtjänstens huvudnamn som finns på. `~/.azure/aksServicePrincipal.json`  
- Du kan också ta bort filen aksServicePrincipal.json och AKS skapar ett nytt huvudnamn för tjänsten.
- När du tar bort ett AKS-kluster som skapats av [az aks create][az-aks-create] tas tjänstens huvudnamn som skapades automatiskt inte bort.
    - För att ta bort tjänstens huvudnamn kör du en fråga efter ditt kluster *servicePrincipalProfile.clientId* och tar sedan bort med [az ad app delete][az-ad-app-delete]. Ersätt följande resursgruppsnamn och klisternamn med dina egna värden:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Felsöka

Tjänsthuvudprincipautentiseringsuppgifterna för ett AKS-kluster cachelagras av Azure CLI. Om dessa autentiseringsuppgifter har upphört att gälla uppstår fel som distribuerar AKS-kluster. Följande felmeddelande när [az aks create][az-aks-create] körs kan tyda på ett problem med de cachelagrade huvudbehörighetsuppgifterna för cachelagrade tjänsten:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Kontrollera ålder på autentiseringsfilen med följande kommando:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Standardtiden för förfallodatum för tjänstens huvudbehörighet är ett år. Om filen *aksServicePrincipal.json* är äldre än ett år tar du bort filen och försöker distribuera ett AKS-kluster igen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Active Directory-tjänsthuvudnamn finns i [Program- och tjänsthuvudobjekt][service-principal].

Information om hur du uppdaterar autentiseringsuppgifterna finns i [Uppdatera eller rotera autentiseringsuppgifterna för ett tjänsthuvudnamn i AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
