---
title: Skapa en Java-app med Azure Cosmos DB Cassandra API
description: Den här snabbstarten visar hur du använder Cassandra API i Azure Cosmos DB för att skapa ett profilprogram med Azure-portalen och Java
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 124bbcedceffca318367799441f66e330bc41fef
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811325"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data"></a>Snabbstart: Skapa en Java-app för att hantera Azure Cosmos DB Cassandra API-data

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

I den här snabbstarten skapar du ett Azure Cosmos DB Cassandra API-konto och använder en Cassandra Java-app som klonats från GitHub för att skapa en Cassandra-databas och behållare. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärde- och diagramdatabaser med globala distributions- och vågräta skalfunktioner.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Eller [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Peka `JAVA_HOME` din miljövariabel till mappen där JDK är installerat.
- En [Maven binära arkiv](https://maven.apache.org/download.cgi). På Ubuntu, `apt-get install maven` kör för att installera Maven.
- [Git](https://www.git-scm.com/downloads). På Ubuntu `sudo apt-get install git` kör du för att installera Git.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan börja skapa en dokumentdatabas måste du skapa ett Cassandra-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Nu ska vi övergå till att arbeta med kod. Vi ska klona en Cassandra API-app från GitHub, ange anslutningssträngen och köra appen. Du kommer att se hur lätt det är att arbeta med data programmässigt. 

1. Öppna en kommandotolk. Skapa en ny mapp med namnet `git-samples`. Stäng kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). Dessa utdrag är alla hämtade från *filen src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.*  

* Alternativen Cassandra-värd,port, användarnamn, lösenord och TLS/SSL anges. Information om anslutningssträngen kommer från sidan med anslutningssträngen i Azure-portalen.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* `cluster` ansluter till Azure Cosmos DB Cassandra-API och kör en session för åtkomst.

    ```java
    return cluster.connect();
    ```

Följande utdrag kommer från filen *src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.*

* Skapa ett nytt keyspace.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Skapa en ny tabell.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Infoga användarentiteter med ett förberett instruktionsobjekt.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Fråga för att hämta all användarinformation.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Fråga för att hämta en användares information.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure Portal för att hämta information om din anslutningssträng och kopiera den till appen. Informationen i anslutningssträngen gör det möjligt för appen att kommunicera med den värdbaserade databasen.

1. Välj **Anslutningssträng**i ditt Azure Cosmos DB-konto i [Azure-portalen](https://portal.azure.com/). 

    ![Visa och kopiera ett användarnamn från Azure-portalen, sidan Anslutningssträng](./media/create-cassandra-java/copy-username-connection-string-azure-portal.png)

2. Använd ![Knappen Kopiera](./media/create-cassandra-java/copy-button-azure-portal.png) knappen på höger sida av skärmen för att kopiera värdet KONTAKTPUNKT.

3. Öppna filen *config.properties* från mappen *C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources.* 

3. Klistra in KONTAKTPUNKT-värdet från portalen över `<Cassandra endpoint host>` på rad 2.

    Linje 2 av *config.properties* bör nu se ut ungefär som 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Gå tillbaka till portalen och kopiera användarnamnet. Klistra in värdet för ANVÄNDARNAMN från portalen över `<cassandra endpoint username>` på rad 4.

    Linje 4 av *config.properties* bör nu se ut ungefär som 

    `cassandra_username=cosmos-db-quickstart`

4. Gå tillbaka till portalen och kopiera värdet LÖSENORD. Klistra in värdet för LÖSENORD från portalen över `<cassandra endpoint password>` på rad 5.

    Linje 5 av *config.properties* bör nu se ut ungefär som 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. På rad 6, om du vill använda ett specifikt `<SSL key store file location>` TLS/SSL-certifikat, ersätter du med platsen för TLS/SSL-certifikatet. Om inget värde anges kommer JDK-certifikatet som är installerat på <JAVA_HOME>/jre/lib/security/cacerts att användas. 

6. Om du har ändrat rad 6 för att använda ett specifikt TLS/SSL-certifikat uppdaterar du rad 7 för att använda lösenordet för certifikatet. 

7. Spara filen *config.properties.*

## <a name="run-the-java-app"></a>Kör Java-appen

1. I git-terminalfönstret `cd` till mappen `azure-cosmosdb-cassandra-java-getting-started`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started"
    ```

2. I git-terminalfönstret använder du följande kommando för att generera filen `cosmosdb-cassandra-examples.jar`.

    ```git
    mvn clean install
    ```

3. I git-terminalfönstret kör du följande kommando för att starta Java-programmet.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    Terminalfönstret visar aviseringar om att keyspace och tabell har skapats. Det markerar och returnerar sedan alla användare i tabellen och visar utdata, och väljer sedan ett rad-ID och visar värdet.  

    Tryck på Ctrl+C för att stoppa körningen av programmet och stänga konsolfönstret.

4. I **Datautforskaren** på Azure-portalen kan du fråga, ändra och arbeta med dessa nya data. 

    ![Visa data i Data Explorer - Azure Cosmos DB](./media/create-cassandra-java/view-data-explorer-java-app.png)

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Azure Cosmos DB-konto med Cassandra API och kör en Cassandra Java-app som skapar en Cassandra-databas och behållare. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera Cassandra-data till Azure Cosmos DB](cassandra-import-data.md)
