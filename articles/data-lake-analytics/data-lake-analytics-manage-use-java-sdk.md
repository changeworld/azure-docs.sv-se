---
title: Hantera Azure Data Lake Analytics med Azure Java SDK
description: I den här artikeln beskrivs hur du använder Azure Java SDK för att skriva appar som hanterar DataSjöanalys-jobb, datakällor & användare.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 07830b36-2fe3-4809-a846-129cf67b6a9e
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: b8c7d2ba1c782c3b6ae3034d6a9aab5eb19be954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70813636"
---
# <a name="manage-azure-data-lake-analytics-using-a-java-app"></a>Hantera Azure Data Lake Analytics med hjälp av en Java-app
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

I den här artikeln beskrivs hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av en app som skrivits med Azure Java SDK. 

## <a name="prerequisites"></a>Krav
* **Java Development Kit (JDK) 8** (med Java version 1.8).
* **IntelliJ** eller annan lämplig Java utvecklingsmiljö. Instruktionerna i det här dokumentet använder IntelliJ.
* Skapa ett program med Azure Active Directory (AAD) och hämta dess **klient-ID**, **innehavar-ID** och **nyckel**. Mer information om AAD-program och instruktioner för hur du hämtar ett klient-ID finns i [Skapa Active Directory-program och tjänstobjekt med portalen](../active-directory/develop/howto-create-service-principal-portal.md). Reply URI och Key är tillgängliga från portalen när du har skapat programmet och nyckeln genereras.

## <a name="authenticating-using-azure-active-directory"></a>Autentisera med Azure Active Directory

Koden efter kodavsnittet innehåller kod för **icke-interaktiv** autentisering, där programmet tillhandahåller sina egna autentiseringsuppgifter.

## <a name="create-a-java-application"></a>Skapa ett Java-program
1. Öppna IntelliJ och skapa ett **Java-projekt med kommandoradsappmallen.**
2. Högerklicka på projektet på vänster sida av skärmen och klicka på **Lägg till stöd för Framework**. Välj **Maven** och klicka på **OK**.
3. Öppna den nyligen skapade filen **"pom.xml"** och lägg till följande fragment av text mellan taggen **\</version >** och **\</project>**:

```
<dependencies>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-authentication</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-runtime</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.rest</groupId>
        <artifactId>client-runtime</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-store</artifactId>
        <version>1.22.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-analytics</artifactId>
        <version>1.22.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.3.6</version>
        <exclusions>
            <exclusion>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-core</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```

Gå till **Inställningar för fil > > > > distribution**. Välj **Byggverktyg > Maven > importera**. Kontrollera sedan **Importera Maven-projekt automatiskt**.

Öppna `Main.java` och ersätt det befintliga kodblocket med följande kod:

```java
import com.microsoft.azure.CloudException;
import com.microsoft.azure.credentials.ApplicationTokenCredentials;
import com.microsoft.azure.datalake.store.*;
import com.microsoft.azure.datalake.store.oauth2.*;
import com.microsoft.azure.management.datalake.analytics.implementation.*;
import com.microsoft.azure.management.datalake.store.*;
import com.microsoft.azure.management.datalake.store.implementation.*;
import com.microsoft.azure.management.datalake.store.models.*;
import com.microsoft.azure.management.datalake.analytics.*;
import com.microsoft.azure.management.datalake.analytics.models.*;
import com.microsoft.rest.credentials.ServiceClientCredentials;

import java.io.*;
import java.nio.charset.Charset;
import java.util.ArrayList;
import java.util.UUID;
import java.util.List;

public class Main {
    private static String _adlsAccountName;
    private static String _adlaAccountName;
    private static String _resourceGroupName;
    private static String _location;

    private static String _tenantId;
    private static String _subscriptionId;
    private static String _clientId;
    private static String _clientSecret;

    private static DataLakeStoreAccountManagementClient _adlsClient;
    private static ADLStoreClient _adlsStoreClient;
    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;

    public static void main(String[] args) throws Exception {
        _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
        _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
        _resourceGroupName = "<RESOURCE-GROUP-NAME>";
        _location = "East US 2";

        _tenantId = "<TENANT-ID>";
        _subscriptionId =  "<SUBSCRIPTION-ID>";
        _clientId = "<CLIENT-ID>";

        _clientSecret = "<CLIENT-SECRET>";

        String localFolderPath = "C:\\local_path\\";

        // ----------------------------------------
        // Authenticate
        // ----------------------------------------
        ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
        SetupClients(creds);

        // ----------------------------------------
        // List Data Lake Store and Analytics accounts that this app can access
        // ----------------------------------------
        System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subscriptionId));
        List<DataLakeStoreAccountBasic> adlsListResult = _adlsClient.accounts().list();
        for (DataLakeStoreAccountBasic acct : adlsListResult) {
            System.out.println(acct.name());
        }

        System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subscriptionId));
        List<DataLakeAnalyticsAccountBasic> adlaListResult = _adlaClient.accounts().list();
        for (DataLakeAnalyticsAccountBasic acct : adlaListResult) {
            System.out.println(acct.name());
        }
        WaitForNewline("Accounts displayed.", "Creating files.");

        // ----------------------------------------
        // Create a file in Data Lake Store: input1.csv
        // ----------------------------------------
        CreateFile("/input1.csv", "123,abc", true);
        WaitForNewline("File created.", "Submitting a job.");

        // ----------------------------------------
        // Submit a job to Data Lake Analytics
        // ----------------------------------------
        String script = "@input = EXTRACT Row1 string, Row2 string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();";
        UUID jobId = SubmitJobByScript(script, "testJob");
        WaitForNewline("Job submitted.", "Getting job status.");

        // ----------------------------------------
        // Wait for job completion and output job status
        // ----------------------------------------
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        System.out.println("Waiting for job completion.");
        WaitForJob(jobId);
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        WaitForNewline("Job completed.", "Downloading job output.");

        // ----------------------------------------
        // Download job output from Data Lake Store
        // ----------------------------------------
        DownloadFile("/output1.csv", localFolderPath + "output1.csv");
        WaitForNewline("Job output downloaded.", "Deleting file.");

        DeleteFile("/output1.csv");
        WaitForNewline("File deleted.", "Done.");
    }

    public static void SetupClients(ServiceClientCredentials creds) {
        _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
        _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
        _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
        _adlsClient.withSubscriptionId(_subscriptionId);
        _adlaClient.withSubscriptionId(_subscriptionId);

        String authEndpoint = "https://login.microsoftonline.com/" + _tenantId + "/oauth2/token";
        AccessTokenProvider provider = new ClientCredsTokenProvider(authEndpoint, _clientId, _clientSecret);
        _adlsStoreClient = ADLStoreClient.createClient(_adlsAccountName + ".azuredatalakestore.net", provider);
    }

    public static void WaitForNewline(String reason, String nextAction) {
        if (nextAction == null)
            nextAction = "";

        System.out.println(reason + "\r\nPress ENTER to continue...");
        try {
            System.in.read();
        } catch (Exception e) {
        }

        if (!nextAction.isEmpty()) {
            System.out.println(nextAction);
        }
    }

    // Create accounts
    public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
        // Create ADLS account
        CreateDataLakeStoreAccountParameters adlsParameters = new CreateDataLakeStoreAccountParameters();
        adlsParameters.withLocation(_location);

        _adlsClient.accounts().create(_resourceGroupName, _adlsAccountName, adlsParameters);

        // Create ADLA account
        AddDataLakeStoreWithAccountParameters adlsInfo = new AddDataLakeStoreWithAccountParameters();
        adlsInfo.withName(_adlsAccountName);

        List<AddDataLakeStoreWithAccountParameters> adlsInfoList = new ArrayList<>();
        adlsInfoList.add(adlsInfo);

        CreateDataLakeAnalyticsAccountParameters adlaParameters = new CreateDataLakeAnalyticsAccountParameters();
        adlaParameters.withLocation(_location);
        adlaParameters.withDefaultDataLakeStoreAccount(_adlsAccountName);
        adlaParameters.withDataLakeStoreAccounts(adlsInfoList);

        _adlaClient.accounts().create(_resourceGroupName, _adlaAccountName, adlaParameters);
    }

    // Create a file
    public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
        byte[] bytesContents = contents.getBytes();

        ADLFileOutputStream stream = _adlsStoreClient.createFile(path, IfExists.OVERWRITE, "777", force);
        stream.write(bytesContents);
        stream.close();
    }

    // Delete a file
    public static void DeleteFile(String filePath) throws IOException, CloudException {
        _adlsStoreClient.delete(filePath);
    }

    // Download a file
    private static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
        ADLFileInputStream stream = _adlsStoreClient.getReadStream(srcPath);

        PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());

        String fileContents = "";
        if (stream != null) {
            Writer writer = new StringWriter();

            char[] buffer = new char[1024];
            try {
                Reader reader = new BufferedReader(
                        new InputStreamReader(stream, "UTF-8"));
                int n;
                while ((n = reader.read(buffer)) != -1) {
                    writer.write(buffer, 0, n);
                }
            } finally {
                stream.close();
            }
            fileContents = writer.toString();
        }

        pWriter.println(fileContents);
        pWriter.close();
    }

    // Submit a U-SQL job
    private static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
        UUID jobId = java.util.UUID.randomUUID();
        CreateJobProperties properties = new CreateUSqlJobProperties();
        properties.withScript(script);
        CreateJobParameters parameters = new CreateJobParameters();
        parameters.withName(jobName);
        parameters.withType(JobType.USQL);
        parameters.withProperties(properties);

        JobInformation jobInfo = _adlaJobClient.jobs().create(_adlaAccountName, jobId, parameters);

        return jobInfo.jobId();
    }

    // Wait for job completion
    private static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
        JobInformation jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        while (jobInfo.state() != JobState.ENDED) {
            jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        }
        return jobInfo.result();
    }

    // Retrieve job status
    private static String GetJobStatus(UUID jobId) throws IOException, CloudException {
        JobInformation jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        return jobInfo.state().toString();
    }
}
```

Ange värden för parametrar som kallas i kodavsnittet:
* `localFolderPath`
* `_adlaAccountName`
* `_adlsAccountName`
* `_resourceGroupName`
* `_tenantId`
* `_subId`
* `_clientId`
* `_clientSecret`

## <a name="next-steps"></a>Nästa steg

* Mer information om U-SQL finns i [Kom igång med Azure Data Lake Analytics U-SQL-språket](data-lake-analytics-u-sql-get-started.md) och [U-SQL-språkreferens](https://docs.microsoft.com/u-sql/).
* Hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med Azure-portalen](data-lake-analytics-manage-use-portal.md).
* Om du vill få en översikt över Data Lake Analytics, se [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).
