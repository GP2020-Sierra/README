# Azure

1) Create a subscription on Azure for paying for the resources

2) Create a resource group linked to the subscription

3) Create an `IoT Hub`. We used S1 - Standard tier so we didn't have to worry about the amount of messages being sent but depending on how many devices are being used and the message frequency (by default 1 per device per 30 seconds containing 3 readings) the free tier may be enough (however, you cannot upgrade from the free tier to the paid tiers). Basic tier hubs are not supported as we use the device twin.

    - Add the tenant certificate

        - In the Azure Sphere Developer Command Prompt, run `azsphere tenant download-ca-certificate --output tenant-ca-cert.cer`

        - Under `Certificates`, Add the `tenant-ca-cert.cer` file

        - Generate a validation code for the certificate, and run `azsphere tenant download-validation-certificate -c [VALIDATION CODE] -o tenant-validation.cer`

        - Upload the validation certificate to confirm.        

4) Create an `IoT Hub Device Provisioning Service`. We used 

    - Link to the IoT Hub

        - Under `Linked IoT hubs`, Add the IoT Hub you created above

    - Add the tenant certificate

        - Repeat the same steps as for the IoT Hub but on the DPS. (Don't have to re-download the ca cert)

    - Add an enrollment group
    
        - Under `Manage Enrollments`, add an enrollment group. Select the primary and secondary certificate to be the certificate you just added, and the IoT Hub to assign devices to. Update the `initial device twin state` to match below

            ```json
            {
                "tags": {
                    "deployment": {
                        "name": "DEFAULT LOCATION",
                        "owner": "DEFAULT OWNER"
                    },
                },
                "properties": {
                    "desired": {}
                }
            }
            ```

            (optionally updating the default deployment name and owner (which isn't publically displayed on the site, but is exposed via our API))

5) Create an `SQL Server`. 

    - Create an `SQL Database` inside the server. We chose standard tier and 10 DTUs.

    - Run the setup code for the DB which can be found in the [`db-setup.ipynb` file within the GP2020-Sierra/azure-sql-db](https://github.com/GP2020-Sierra/azure-sql-db/blob/master/db-setup.ipynb) repo, or below:

        ```sql
        CREATE SCHEMA iot;

        CREATE TABLE [iot].[locations]
        (
            [id] INT NOT NULL IDENTITY(1,1) PRIMARY KEY,
            [owner] NVARCHAR(50) NOT NULL,
            [name] NVARCHAR(50) NOT NULL UNIQUE,
            [nameID] NVARCHAR(50) NOT NULL UNIQUE,
            [shown] BIT NOT NULL DEFAULT (1)
        );

        CREATE TABLE [iot].[messages]
        (
            [id] INT NOT NULL IDENTITY(1,1) PRIMARY KEY,
            [locationID] INT NOT NULL,
            [timestamp] DATETIME2(0) NOT NULL,
            [readingNumber] INT NOT NULL,
            [temperatureLPS] REAL NOT NULL,
            [temperatureLSM] REAL NOT NULL,
            [temperatureDHT] REAL NOT NULL,
            [pressureLPS] REAL NOT NULL,
            [humidityDHT] REAL NOT NULL,
            [eco2] SMALLINT NOT NULL,
            [tvoc] SMALLINT NOT NULL,
            [wifiDevices] SMALLINT NOT NULL,
            [wifiBaseStations] SMALLINT NOT NULL,
            CONSTRAINT FK_messages_location FOREIGN KEY (locationID)
                REFERENCES [iot].[locations] (id)
                ON DELETE NO ACTION
                ON UPDATE CASCADE
        );

        CREATE USER azureData WITH PASSWORD = '[ENTER A PASSWORD]';
        GRANT INSERT, SELECT TO azureData;

        GO
        ```

        Remembering to replace the password for the `azureData` user with a strong generated password.

6) Create a `Function App`

7) Setup device twins




TODO: modify app manifest before building it