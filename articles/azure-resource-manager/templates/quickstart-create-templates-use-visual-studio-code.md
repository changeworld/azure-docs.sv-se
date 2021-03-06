---
title: Skapa mall - Visual Studio-kod
description: Använd Visual Studio Code och Azure Resource Manager-malltillägget för att arbeta med Resource Manager-mallar.
author: mumian
ms.date: 04/13/2020
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 96e57146fb6bb17cbb8bb5975371e07b66f3ec8b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255098"
---
# <a name="quickstart-create-arm-templates-by-using-visual-studio-code"></a>Snabbstart: Skapa ARM-mallar med Visual Studio-kod

Lär dig hur du använder Visual Studio-kod och Azure Resource Manager Tools-tillägget för att skapa och redigera ARM-mallar (Azure Resource Manager). Du kan skapa ARM-mallar i Visual Studio-kod utan tillägget, men tillägget innehåller alternativ för automatisk komplettering som förenklar mallutvecklingen. Information om vilka begrepp som är associerade med distribution och hantering av dina Azure-lösningar finns i [översikt över malldistribution](overview.md).

I den här snabbstarten distribuerar du ett lagringskonto:

![Snabbstarts visual studio-koddiagram för Visual Studio-mallmallar för Resurshanteraren](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

- [Visual Studio-kod](https://code.visualstudio.com/).
- Resource Manager Tools-tillägget. Följ dessa steg för att installera:

    1. Öppna Visual Studio Code.
    2. Tryck på **CTRL + SKIFT + X** för att öppna fönsterrutan Tillägg
    3. Sök efter **Azure Resource Manager Tools** och välj sedan **Installera**.
    4. Välj **Läs in på nytt** för att slutföra installationen av tillägget.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

I stället för att skapa en mall från början öppnar du en mall från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/). Azure Quickstart Templates är en lagringsplats för ARM-mallar.

Den mall som används i den här snabbstarten kallas [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Skapa ett standardlagringskonto). Mallen definierar en Azure Storage-kontoresurs.

1. Välj **Öppna**>**fil**i Visual Studio-kod .
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Välj **Öppna** för att öppna filen.
4. Välj **Spara fil**>**som** om du vill spara filen som **azuredeploy.json** på den lokala datorn.

## <a name="edit-the-template"></a>Redigera mallen

Om du vill veta hur du redigerar en mall med hjälp av Visual Studio Code lägger du till ytterligare ett element i avsnittet `outputs` för att visa lagrings-URI.

1. Lägg till ytterligare utdata i den exporterade mallen:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    När du är klar ser utdataavsnittet ut så här:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Om du kopierade och klistrade in koden i Visual Studio-koden försöker du skriva om **värdeelementet** så att det uppstår funktionen IntelliSense i tillägget Resource Manager Tools.

    ![Resource Manager template visual studio code IntelliSense](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Välj**Spara** **fil**>om du vill spara filen.

## <a name="deploy-the-template"></a>Distribuera mallen

Det finns många metoder för att distribuera mallar. Azure Cloud Shell används i den här snabbstarten. Cloud Shell stöder både Azure CLI och Azure PowerShell. Använd flikväljaren för att välja mellan CLI och PowerShell.

1. Logga in på [Azure Cloud Shell](https://shell.azure.com)

2. Välj önskad miljö genom att välja antingen **PowerShell** eller **Bash**(CLI) i det övre vänstra hörnet.  Du måste starta om gränssnittet när du byter.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Azure portal Cloud Shell CLI](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure portal Cloud Shell PowerShell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Välj **Ladda upp/ned filer** och välj sedan **Ladda upp**.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Ladda upp -fil för Azure portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Ladda upp -fil för Azure portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Välj den fil som du sparade i föregående avsnitt. Standardnamnet är **azuredeploy.json**. Mallfilen måste vara tillgänglig från gränssnittet.

    Alternativt kan du använda kommandona **ls** och **cat** för att kontrollera att filen laddas upp.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Listfil för Azure portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Listfil för Azure portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. Kör följande kommandon från Cloud Shell. Välj fliken så att du ser PowerShell-koden eller CLI-koden. Ange ett projektnamn som används för att generera ett resursgruppsnamn.  Resursgruppsnamnet är projektnamnet med **rg** bifogat.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" &&
    echo "Press [ENTER] to continue ..."
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

    Uppdatera mallfilnamnet om du sparar filen som ett annat namn än **azuredeploy.json**.

    Följande skärmbild visar ett distributionsexempel:

    # <a name="cli"></a>[CLI](#tab/CLI)

    ![Distribuera distributionsmall för Azure portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Distribuera distributionsmall för Azure portal Cloud Shell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    Lagringskontonamnet och lagrings-URL:en i utdataavsnittet är markerade på skärmbilden. Du behöver lagringskontonamnet i nästa steg.

5. Kör följande CLI- eller PowerShell-kommando för att visa det nyligen skapade lagringskontot:

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName &&
    echo "Press [ENTER] to continue ..."
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

Mer information om hur du använder Azure Storage-konton finns i [Snabbstart: Ladda upp, ladda ned och lista blobar med Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn. Resursgruppsnamnet är projektnamnet med **rg** bifogat. Du ska se en lagringskontoresurs i resursgruppen.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

Huvudfokus för den här snabb är att använda Visual Studio Code för att redigera en befintlig mall från snabbstartsmallarna i Azure. Du har också lärt dig hur du distribuerar mallen med cli eller PowerShell från Azure Cloud Shell. Snabbstartsmallarna i Azure kanske inte innehåller exakt det du behöver. Mer information om mallutveckling finns i vår nya videoprogramledningsserie:

> [!div class="nextstepaction"]
> [Självstudier för nybörjare](./template-tutorial-create-first-template.md)
