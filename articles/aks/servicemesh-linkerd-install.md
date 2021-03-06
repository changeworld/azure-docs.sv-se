---
title: Installera Linkerd i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och använder Linkerd för att skapa ett tjänstnät i ett AKS-kluster (Azure Kubernetes Service)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 419b61527b68299c82dec4f2f5da6b0220859cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593755"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Installera Linkerd i Azure Kubernetes Service (AKS)

[Linkerd][linkerd-github] är ett servicenät med öppen källkod och [CNCF-inkuberingsprojekt][linkerd-cncf]. Linkerd är ett ultralätt servicenät som tillhandahåller funktioner som inkluderar trafikhantering, tjänstidentitet och säkerhet, tillförlitlighet och observerbarhet. Mer information om Linkerd finns i den officiella dokumentationen för [Linkerd och][linkerd-faq] [Linkerd Architecture.][linkerd-architecture]

Den här artikeln visar hur du installerar Linkerd. Linkerd-klientbinären `linkerd` installeras på klientdatorn och Linkerd-komponenterna installeras i ett Kubernetes-kluster på AKS.

> [!NOTE]
> Dessa instruktioner refererar till `stable-2.6.0`Linkerd version .
>
> Linkerd `stable-2.6.x` kan köras mot Kubernetes versioner `1.13+`. Du kan hitta ytterligare stabila och kant Linkerd versioner på [GitHub - Linkerd Releases][linkerd-github-releases].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Ladda ner och installera Linkerd linkerd-klienten binär
> * Installera Linkerd på AKS
> * Verifiera Linkerd-installationen
> * Få tillgång till instrumentpanelen
> * Avinstallera Linkerd från AKS

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här artikeln förutsätter att du `1.13` har skapat ett AKS-kluster (Kubernetes och högre, med RBAC aktiverat) och har upprättat en `kubectl` anslutning till klustret. Om du behöver hjälp med något av dessa objekt, sedan se [AKS snabbstart][aks-quickstart].

Alla Linkerd pods måste schemaläggas för att köras på Linux-noder - den här inställningen är standard i installationsmetoden nedan och kräver ingen ytterligare konfiguration.

Den här artikeln separerar Linkerd-installationsvägledningen i flera diskreta steg. Resultatet är detsamma i struktur som den officiella Linkerd komma igång [vägledning][linkerd-getting-started].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Installera Linkerd på AKS

Innan vi installerar Linkerd kör vi förinstallationskontroller för att avgöra om kontrollplanet kan installeras i vårt AKS-kluster:

```console
linkerd check --pre
```

Du bör se något liknande följande för att ange att AKS-klustret är ett giltigt installationsmål för Linkerd:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Nu är det dags att installera Linkerd-komponenterna. Använd `linkerd` och `kubectl` binärfiler för att installera Linkerd-komponenterna i AKS-klustret. Ett `linkerd` namnområde skapas automatiskt och komponenterna installeras i det här namnområdet.

```console
linkerd install | kubectl apply -f -
```

Linkerd distribuerar ett antal objekt. Du ser listan från utdata `linkerd install` från kommandot ovan. Distributionen av Linkerd-komponenterna bör ta cirka 1 minut att slutföra, beroende på klustermiljön.

Nu har du distribuerat Linkerd till AKS-klustret. För att säkerställa att vi har en lyckad distribution av Linkerd, låt oss gå vidare till nästa avsnitt för att [validera Linkerd-installationen](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>Verifiera Linkerd-installationen

Bekräfta att resurserna har skapats. Använd [kubectl get svc][kubectl-get] och [kubectl get][kubectl-get] `linkerd` pod-kommandon för att fråga namnområdet, `linkerd install` där Linkerd-komponenterna installerades av kommandot:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

Följande exempelutdata visar de tjänster och poddar (schemalagda på Linux-noder) som nu ska köras:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd tillhandahåller ett `linkerd` kommando via klientbinären för att verifiera att Linkerd-kontrollplanet har installerats och konfigurerats.

```console
linkerd check
```

Du bör se något liknande följande för att ange att installationen lyckades:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Få tillgång till instrumentpanelen

Linkerd levereras med en instrumentpanel som ger insikt i tjänstnätet och arbetsbelastningarna. Använd kommandot för att `linkerd dashboard` komma åt instrumentpanelen. Det här kommandot utnyttjar [kubectl port-forward][kubectl-port-forward] för att skapa en säker anslutning mellan klientdatorn och relevanta poddar i AKS-klustret. Den öppnas sedan automatiskt på instrumentpanelen i standardwebbläsaren.

```console
linkerd dashboard
```

Kommandot skapar också en port-forward och returnerar en länk för Grafana-instrumentpanelerna.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Avinstallera Linkerd från AKS

> [!WARNING]
> Om du tar bort Linkerd från ett system som körs kan det leda till trafikrelaterade problem mellan dina tjänster. Se till att du har gjort avsättningar för att ditt system fortfarande ska fungera korrekt utan Linkerd innan du fortsätter.

Först måste du ta bort dataplanet proxyservrar. Ta bort alla [automatiska proxyinjektionsanteckningar][linkerd-automatic-proxy-injection] från arbetsbelastningsnamnområden och distribuera dina arbetsbelastningsdistributioner. Dina arbetsbelastningar bör inte längre ha några associerade dataplankomponenter.

Ta slutligen bort kontrollplanet enligt följande:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utforskar installations- och konfigurationsalternativ för Linkerd finns i följande officiella Linkerd-vägledning:

- [Linkerd - Helm installation][linkerd-install-with-helm]
- [Linkerd - Flerstegsinstallation för att tillgodose rollprivilegier][linkerd-multi-stage-installation]

Du kan också följa ytterligare scenarier med hjälp av:

- [Linkerd emojivoto demo][linkerd-demo-emojivoto]
- [Linkerd böcker demo][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
