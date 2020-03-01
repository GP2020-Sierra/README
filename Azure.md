# Azure

1) Create a subscription on Azure for paying for the resources

2) Create a resource group linked to the subscription

3) Create an `IoT Hub`. We used S1 - Standard tier so we didn't have to worry about the amount of messages being sent but depending on how many devices are being used and the message frequency (by default 1 per device per 30 seconds containing 3 readings) the free tier may be enough (however, you cannot upgrade from the free tier to the paid tiers). Basic tier hubs are not supported as we use the device twin.

    - Add the tenant certificate

        - In the Azure Sphere Developer Command Prompt, run `azsphere tenant download-ca-certificate --output tenant-ca-cert.cer`

        - Under `Certificates`, Add the `tenant-ca-cert.cer` file

        - Generate a validation code for the certificate, and run `azsphere tenant download-validation-certificate -c [VALIDATION CODE] -o tenant-validation.cer`

        - Upload the validation certificate to confirm.     

    - Setup message routing

        - Under `Message routing` add an endpoint. Select the built in `events` endpoint and set the data source to `Device Telemetry Messages`.

4) Create an `IoT Hub Device Provisioning Service`.

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

    - Under `Firewalls and Virtual Networks`, ensure `Allow Azure services and resources to access this server` is ON.

    - Connect to the SQL database. We would recommend using [Azure Data Studio](https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio?view=sql-server-ver15)

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

    - Create a function app, remembering it's name / URL `[name].azurewebsites.net`. Select `Node.js` for the runtime stack

    - Under `Function App Settings`, set the `Runtime version` to `~2`.

    - Under `Configuration` we need to add several Application Settings.

        - For connecting to the IoT Hub

            - Name: `IOT_HUB_CONNECTION`

              Value: inside your IoT Hub, under `Built in endpoints`, the `Event Hub-compatible endpoint` in the `Events` section. (Should begin with `Endpoint=`...)

            - Name: `IOT_HUB_EVENT_HUB_NAME`

              Value: inside your IoT Hub, under `Built in endpoints`, the `Event Hub-compatible name` in the `Events` section.

        - For connecting to the DB

            - Name: `DATABASE_SERVER`

              Value: `[SQL Server name].database.windows.net` from part 5

            - Name: `DATABASE_NAME`

              Value: `[SQL DB name]` from part 5

            - Name: `DATABASE_USERNAME`

              Value: `azureData`

            - Name: `DATABASE_PASSWORD`

              Value: the password you generated from part 5

    - Follow Microsoft's documentation to setup VS Code for Azure functions.

    - Download our azure functions code
        - `git clone https://github.com/GP2020-Sierra/azure-functions`
        
        - `cd azure-functions`
        
        - `npm install`
        
        Open this directory in VS Code, and push it to your new Azure Functions instance.

7) Setup device twins

    When a device is first ran, it will be created under `IoT Devices` in the IoT Hub. 
    Clicking on it will bring up it's info page, where you can click at the top to modify the device twin.
    From here you can customize its location and owner.


TODO: modify app manifest before building it