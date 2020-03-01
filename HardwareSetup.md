# Hardware Setup

Follow [Microsoft's guide](https://docs.microsoft.com/en-gb/azure-sphere/install/overview) to setup a development environment

## Physical setup

Solder pins into:
* The PMOD Uart connector edge most row; underneath CLICK socket 2
* The 5v header next to the CLICK 2 (optional; for fan)
* The ESP8266 pin P13

## Software setup

We used VSCode for building all our projects

You also need the AzureSphere SDK installed, as well as ninja added to the path. Microsoft has guides on setting this up.

### Create a tenant

To create a tenant, you start an azure sphere command line (either with the provided script or by adding it straight into the path) and then call `azsphere tenant create --name [name]`, noting that this requires a device attached, and one device can only create at most one tenant.

You select a tenant by calling `azsphere tenant select --tenantid [id]`, with the id being found by either listing them or during creation (noting creation auto selects tenants (at least at first))

If you have the right tenant selected, you can claim the devices with `azsphere dev claim`

## Building the code
Clone the repo `https://github.com/GP2020-Sierra/azure-sphere` and build the code following Microsoft's instructions.

To simply deploy the code to multiple boards you can create a product and deploy the `.imagepackage` file from the `out` folder.
1) `azsphere prd create -n sierra`
2) `azsphere device-group deployment create --devicegroupname "Field Test" --productname "sierra" --filepath "<PATH>\out\Debug-3\Sierra2020.imagepackage"`
3) (for each device) `azsphere dev ect --productname sierra`

## Image of a deployed device
![Deployed Device](/assets/device.jpg)