---
title: 'Självstudiekurs: Distribuera från GitHub till Azure App Service med Jenkins'
description: Konfigurera Jenkins för kontinuerlig integrering (CI) från GitHub och kontinuerlig distribution (CD) till Azure App Service för Java-webbappar
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9fcf178b71ac1f07bfb58cd2502701ae5392b472
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74158400"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Självstudie: Distribuera från GitHub till Azure App Service med kontinuerlig integrering och distribution i Jenkins

Den här självstudien distribueras en Java-webbexempelapp från GitHub till [Azure App Service i Linux](/azure/app-service/containers/app-service-linux-intro) genom att konfigurera kontinuerlig integrering (CI) och kontinuerlig distribution (CD) i Jenkins. När du uppdaterar appen genom att push-överföra incheckningar till GitHub skapar och publicerar Jenkins om din app automatiskt till Azure App Service. Exempelappen i den här självstudien har utvecklats med hjälp av [Spring Boot](https://projects.spring.io/spring-boot/)-ramverket. 

![Översikt över distributionen av GitHub till Azure App Service](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Installera Jenkins-plugin-program så att du kan skapa från GitHub, distribuera till Azure App Service och andra relaterade uppgifter.
> * Förgrena GitHub-exempellager så att du har en fungerande kopia.
> * Anslut Jenkins till GitHub.
> * Skapa tjänstens huvudnamn för Azure så att Jenkins kan få åtkomst till Azure utan att använda dina autentiseringsuppgifter.
> * Lägg till tjänstens huvudnamn till Jenkins.
> * Skapa Jenkins-pipelinen som skapar och distribuerar exempelappen varje gång du uppdaterar appen i GitHub.
> * Skapa Build and Deployment-filer för din Jenkins-pipeline.
> * Peka din Jenkins-pipeline på Build and Deployment-skriptet.
> * Distribuera din exempelapp till Azure genom att köra en manuell version.
> * Skicka en appuppdatering i GitHub som utlöser Jenkins för att skapa och distribuera om till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande objekt:

* En [Jenkins](https://jenkins.io/)-server med Java Development Kit (JDK) och Maven-verktyg installerade på en virtuell Linux-baserad dator i Azure

  Om du inte har någon Jenkins-server kan du slutföra stegen nu i Azure-portalen: [Skapa Jenkins-server på en virtuell Linux-baserad dator i Azure](/azure/jenkins/install-jenkins-solution-template)

* Ett [GitHub-konto](https://github.com) så att du kan skaffa en fungerande kopia (gaffel) för exempelappen Java.A GitHub account so you can get a working copy (fork) for the sample Java web app. 

* [Azure CLI](/cli/azure/install-azure-cli), som du kan köra antingen från din lokala kommandorad eller [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Installera Jenkins-plugin-program

1. Logga in på Jenkins-webbkonsolen på den här platsen:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. På Jenkins huvudsida väljer du **Hantera Jenkins** > **Hantera plugins**.

   ![Hantera Jenkins-plugin-program](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. På fliken **Tillgängligt** väljer du de här plugin-programmen:

   - [Azure App-tjänst](https://plugins.jenkins.io/azure-app-service)
   - [GitHub Branch Source](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [miljö injektor Plug-in](https://plugins.jenkins.io/envinject)
   - [Azure-autentiseringsuppgifter](https://plugins.jenkins.io/azure-credentials)

   Om plugin-programmen inte visas kontrollerar du att de inte redan har installerats genom att titta på fliken **Installerade**.

1. Om du vill installera de valda plugin-programmen väljer du **Hämta nu och installerar efter omstart**.

1. När du är klar går du till Jenkins-menyn och väljer **Hantera Jenkins** så du kommer till Jenkins-hanteringssidan för kommande steg.

## <a name="fork-sample-github-repo"></a>Förgrening av GitHub-exempellagringsplats

1. [Logga in på GitHub-lagringsplatsen för exempelappen Spring Boot](https://github.com/spring-guides/gs-spring-boot). 

1. Välj **Gaffel**i det övre högra hörnet i GitHub .

   ![Förgreningsexempellager från GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Följ anvisningarna för att välja ditt GitHub-konto och slutför förgreningen.

Konfigurera sedan Jenkins med dina autentiseringsuppgifter för GitHub.

## <a name="connect-jenkins-to-github"></a>Anslut Jenkins till GitHub

Om du vill att Jenkins övervakar GitHub och svarar när nya incheckningar push-överförs till din webbapp i din GitHub-förgrening aktiverar du [GitHub-webhooks](https://developer.github.com/webhooks/) i Jenkins.

> [!NOTE]
> 
> De här stegen skapar personliga autentiseringsuppgifter för åtkomsttoken för Jenkins för att arbeta med GitHub med ditt GitHub-användarnamn och -lösenord. 
> Men om ditt GitHub-konto använder tvåfaktorsautentisering ska du skapa din token i GitHub och konfigurera Jenkins för att använda denna token i stället. 
> I dokumentationen för [Jenkins GitHub-plugin-programmet](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) finns mer information.

1. På sidan **Hantera Jenkins** väljer du **Konfigurera system**. 

   ![Konfigurera system i Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. I **GitHub**-avsnittet anger du information för din GitHub-server. Från listan **Lägga till GitHub-server** väljer du **GitHub-server**. 

   ![Lägga till GitHub-server i Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Om egenskapen **Manage hooks** (Hantera hookar) inte är markerad markerar du den här egenskapen. Välj **Avancerat** så att du kan ange andra inställningar. 

   ![Ange avancerade Jenkins-inställningar för GitHub Server](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. I listan **Hantera ytterligare GitHub-åtgärder** väljer du **Konvertera inloggning och lösenord till token**.

   ![Konvertera inloggning och lösenord till token för GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Välj **Från användarnamn och lösenord** och ange ditt GitHub-användarnamn och -lösenord. När du är klar väljer du **Skapa tokenautentiseringsuppgifter**, som skapar en [GitHub-token (Personal Access Token)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Skapa GitHub-PAT från användarnamn och lösenord](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. I avsnittet **GitHub-server** på listan **Autentiseringsuppgifter** väljer du din nya token. Kontrollera att autentiseringen fungerar genom att välja **Testanslutning**.

   ![Kontrollera anslutningen till GitHub-servern med ny personlig åtkomsttoken](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Skapa därefter tjänstens huvudnamn för Azure som Jenkins använder för autentisering och åtkomst till Azure-resurser.

## <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

I ett senare avsnitt skapar du ett Jenkins-pipeline-jobb som skapar din app från GitHub och distribuerar appen till Azure App Service. För att ge Jenkins tillgång till Azure utan att ange dina autentiseringsuppgifter skapar du [tjänstens huvudnamn](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) i Azure Active Directory för Jenkins. Tjänstens huvudnamn är en separat identitet som Jenkins kan använda för att autentisera åtkomst till Azure-resurser. Om du vill skapa det här [**`az ad sp create-for-rbac`**](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)tjänstens huvudnamn kör du kommandot Azure CLI , antingen från din lokala kommandorad eller Azure Cloud Shell, till exempel: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Kontrollera att du använder tjänstens huvudnamn inom citattecken. Skapa även ett starkt lösenord baserat på [reglerna och begränsningarna för Azure Active Directory-lösenord](/azure/active-directory/active-directory-passwords-policy). Om du inte anger ett lösenord skapar Azure CLI ett lösenord. 

Här är utdata som **`create-for-rbac`** genereras av kommandot: 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Om du redan har ett huvudnamn för tjänsten kan du återanvända den identiteten i stället.
> När du anger värden för tjänstens huvudnamn för autentisering använder du egenskapsvärdena `appId`, `password` och `tenant`. 
> Använd egenskapsvärdet `displayName` när du söker efter ett befintligt huvudnamn för tjänsten.

## <a name="add-service-principal-to-jenkins"></a>Lägga till tjänstens huvudnamn till Jenkins

1. På Jenkins huvudsida väljer du **Autentiseringssystem** > **System**. 

1. På sidan **System** under **Domän** väljer du **Global credentials (unrestricted)** (Globala autentiseringsuppgifter (obegränsade)).

1. På menyn till vänster väljer du **Lägg till autentiseringsuppgifter**.

1. På listan **Variant** väljer du **Azure Service Principal** (Tjänstens huvudnamn för Azure).

1. Ange information för din tjänstens huvudnamn och Azure-prenumeration i de egenskaper som beskrivs i tabellen i det här steget:

   ![Lägga till autentiseringsuppgifter för tjänstens huvudnamn](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Egenskap | Värde | Beskrivning | 
   |----------|-------|-------------| 
   | **Prenumerations-ID** | <*dinAzureSubscription-ID*> | GUID-värde för din Azure-prenumeration <p>**Tips**! Om du inte kan ditt Azure-prenumerations-ID kan du köra följande Azure CLI-kommando från antingen kommandoraden eller i Cloud Shell och sedan använda `id` GUID-värdet: <p>`az account list` | 
   | **Klient-ID** | <*dittAzureServicePrincipal-ID*> | `appId` GUID-värdet som tidigare har skapats för Azure-tjänstens huvudnamn | 
   | **Klienthemlighet** | <*yourSecurePassword*> | Värdet `password` eller ”hemligheten” du angav för Azure-tjänstens huvudnamn | 
   | **Klient-ID:t** | <*dittAzureActiveDirectoryTenant-ID*> | `tenant` GUID-värdet för din Azure Active Directory-klient | 
   | **ID** | <*dinAzureServicePrincipalName*> | `displayName`-värdet för Azure-tjänstens huvudnamn | 

1. Om du vill bekräfta att tjänstens huvudnamn fungerar väljer du **Verifiera tjänstens huvudnamn**. När du är klar väljer du **Ok**.

Skapa sedan Jenkins-pipelinen som skapar och distribuerar din app.

## <a name="create-jenkins-pipeline"></a>Skapa en Jenkins-pipeline

I Jenkins skapar du pipeline-jobbet för att skapa och distribuera din app.

1. Gå tillbaka till din Jenkins-startsida och välj **Nytt objekt**. 

   ![Skapa en Jenkins-pipeline](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Ange ett namn för din pipeline-jobb, till exempel ”My-Java-Web-App” och välj **Pipeline**. Längst ned väljer du **OK**.  

   ![Namnge Jenkins pipeline-jobb](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Konfigurera Jenkins med tjänstens huvudnamn så att Jenkins kan distribuera till Azure utan att använda dina autentiseringsuppgifter.

   1. På fliken **Allmänt** väljer du **Förbered en miljö för körningen**. 

   1. I rutan för **egenskapsinnehåll** som visas lägger du till dessa miljövariabler och deras värden. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Förbereda en miljö för körningen och ange miljövariabler](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. När du är klar väljer du **Spara**.

Skapa därefter versions- och distributionsskript för Jenkins.

## <a name="create-build-and-deployment-files"></a>Skapa build and deployment-filer

Nu ska du skapa de filer som Jenkins använder för att skapa och distribuera din app.

1. I din GitHub-förgrenings `src/main/resources/`-mapp skapar du den här appkonfigurationsfilen med namnet `web.config`, som innehåller den här XML-filen, men byter ut `$(JAR_FILE_NAME)` mot `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. I rotmappen för GitHub-förgreningsmappen skapar du det här build and deployment-skriptet med namnet `Jenkinsfile`, som innehåller den här texten ([källkoden i GitHub finns här](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Checka in både filerna `web.config` och `Jenkinsfile` till GitHub-förgreningen och push-överför dina ändringar.

## <a name="point-pipeline-at-script"></a>Rikta pipeline mot skript

Ange nu det Build and Deployment-skript du vill att Jenkins ska använda.

1. Välj pipeline-jobbet du skapade tidigare i Jenkins. 

   ![Välj Jenkins pipeline-jobb för din webbapp](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. På menyn till vänster väljer du **Konfigurera**.

1. På fliken **Pipeline** på listan **Definition** väljer du **Pipeline-skript från SCM**.

   1. I rutan **SCM** som visas väljer du **Git** som din källkontroll. 

   1. För **Lagringsplats-URL** i avsnittet **Lagringsplats** anger du GitHub-förgreningens URL, till exempel: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. För **Autentiseringsuppgifter** väljer du det personliga åtkomsttoken du skapade tidigare i GitHub.

   1. I rutan **Skriptsökväg** lägger du till sökvägen i ”Jenkinsfile”-skriptet.

   När du är klar ser pipelinedefinitionen ut som i det här exemplet: 

   ![Peka din Jenkins pipeline på skriptet](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. När du är klar väljer du **Spara**.

Sedan skapar och distribuerar du din app till Azure App Service. 

## <a name="build-and-deploy-to-azure"></a>Skapa och distribuera till Azure

1. Med Azure CLI, antingen från kommandoraden eller Azure Cloud Shell, skapar du en [Azure App Service-webbapp i Linux](/azure/app-service/containers/app-service-linux-intro) där Jenkins distribuerar din webbapp när en version är klar. Se till att webbappen har ett unikt namn.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Mer information om Azure CLI-kommandona finns på dessa sidor:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. Välj ditt pipeline-jobb i Jenkins och välj **Skapa nu**.

   När versionen har slutförts distribuerar Jenkins din app, som nu är aktiv på Azure på publikationswebbadressen, till exempel: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Visa den distribuerade appen på Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Skicka ändringar och omdistribuera

1. Gå till den här platsen i din webbapps GitHub-förgrening i webbläsaren:

   `complete/src/main/java/Hello/Application.java`
   
1. Välj **Redigera**filen i det övre högra hörnet i GitHub .

1. Gör den här ändringen i metoden `commandLineRunner()` och checka in ändringen till lagringsplatsens `master`-gren. Incheckningen på `master`-grenen startar en version i Jenkins. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. När versionen har slutförts och Jenkins distribuerar om till Azure kan du uppdatera din app, som visar nu uppdateringen.

   ![Visa den distribuerade appen på Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda virtuella Azure-datorer som versionsagenter](/azure/jenkins/jenkins-azure-vm-agents)
