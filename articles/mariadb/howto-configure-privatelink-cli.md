---
title: Privat länk - Azure CLI - Azure-databas för MariaDB
description: Lär dig hur du konfigurerar privat länk för Azure Database för MariaDB från Azure CLI
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: c28c5494c1cff2c198a94ea6b92003ae74ee2c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371808"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>Skapa och hantera privat länk för Azure Database för MariaDB med CLI

En privat slutpunkt är den grundläggande byggstenen för privat länk i Azure. Det gör det möjligt för Azure-resurser, till exempel virtuella datorer (VMs), att kommunicera privat med privata länkresurser. I den här artikeln får du lära dig hur du använder Azure CLI för att skapa en virtuell dator i ett Virtuellt Azure-nätverk och en Azure-databas för MariaDB-server med en privat Azure-slutpunkt.

> [!NOTE]
> Den här funktionen är tillgänglig i alla Azure-regioner där Azure Database för MariaDB stöder prisnivåer för allmänt ändamål och minne.

## <a name="prerequisites"></a>Krav

För att gå igenom den här guiden behöver du:

- En [Azure-databas för MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i stället, måste du köra Azure CLI version 2.0.28 eller senare i den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa en resurs måste du skapa en resursgrupp som är värd för det virtuella nätverket. Skapa en resursgrupp med [az group create](/cli/azure/group). I det här exemplet skapas en resursgrupp med namnet *myResourceGroup* på *platsen i Westeurope:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk med [az nätverk vnet skapa](/cli/azure/network/vnet). I det här exemplet skapas ett virtuellt standardnätverk med namnet *myVirtualNetwork* med ett undernät med namnet *mySubnet:*

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Inaktivera privata slutpunktsprinciper för undernät 
Azure distribuerar resurser till ett undernät i ett virtuellt nätverk, så du måste skapa eller uppdatera undernätet för att inaktivera privata principer för slutpunktsnätverk. Uppdatera en undernätskonfiguration med namnet *mySubnet* med [az network vnet subnet update:](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Skapa den virtuella datorn 
Skapa en virtuell dator med az vm skapa. Ange ett lösenord som ska användas som inloggningsuppgifter för den virtuella datorn när du uppmanas att göra det. I det här exemplet skapas en virtuell dator med namnet *myVm:* 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Observera den offentliga IP-adressen för den virtuella datorn. Du kommer att använda den här adressen när du ansluter till den virtuella datorn från Internet i nästa steg.

## <a name="create-an-azure-database-for-mariadb-server"></a>Skapa en Azure Database for MariaDB-server 
Skapa en Azure-databas för MariaDB med kommandot az mariadb-server create. Kom ihåg att namnet på din MariaDB-server måste vara unikt i Hela Azure, så ersätt platshållarvärdet inom parentes med ditt eget unika värde: 

```azurecli-interactive
# Create a logical server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

MariaDB-server-ID:et ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMariaDB/servers/servername.``` liknar Du kommer att använda MariaDB-server-ID i nästa steg. 

## <a name="create-the-private-endpoint"></a>Skapa den privata slutpunkten 
Skapa en privat slutpunkt för MariaDB-servern i det virtuella nätverket: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MariaDB Server ID>" \  
    --group-ids mariadbServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen 
Skapa en privat DNS-zon för MariDB-serverdomän och skapa en associationslänk med det virtuella nätverket. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> FQDN i kundens DNS-inställning matchas inte till den privata IP-konfigurerade. Du måste ställa in en DNS-zon för den konfigurerade FQDN som visas [här](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM *myVm* från internet enligt följande:

1. I portalens sökfält anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna *filen downloaded.rdp.*

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Få tillgång till MariaDB-servern privat från den virtuella datorn

1. Öppna PowerShell i fjärrskrivbordet *för myVM.*

2. Ange  `nslookup mydemoserver.privatelink.mariadb.database.azure.com`. 

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Testa den privata länkanslutningen för MariaDB-servern med valfri tillgänglig klient. I exemplet nedan har jag använt [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) för att utföra operationen.

4. Ange eller välj den här informationen i **Ny anslutning:**

    | Inställning | Värde |
    | ------- | ----- |
    | Anslutningsnamn| Välj det anslutningsnamn du väljer.|
    | Värdnamn | Välj *mydemoserver.privatelink.mariadb.database.azure.com* |
    | Användarnamn | Ange användarnamn *username@servername* som anges under skapandet av MariaDB-servern. |
    | lösenord | Ange ett lösenord som anges när MariaDB-servern skapades. |
    ||

5. Välj **Testanslutning** eller **OK**.

6. (Valfritt) Bläddra i databaser från vänstermeny och Skapa eller fråga information från MariaDB-databasen

8. Stäng fjärrskrivbordsanslutningen till myVm.

## <a name="clean-up-resources"></a>Rensa resurser 
När det inte längre behövs kan du använda az-gruppborttagning för att ta bort resursgruppen och alla resurser den har: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azures privata slutpunkt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
