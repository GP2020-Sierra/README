# Hardware Programming

Now that we have created the resources on Azure we can update the code to point to the IoT Hub and then deploy the devices.

## Building the code
- Clone the repo `https://github.com/GP2020-Sierra/azure-sphere` 

- Update the `app_manifest.json` file

    1) Under `AllowedConnections` in `Capabilities`, replace `sierra.azure-devices.net` with the hostname listed for your IoT Hub in Azure.

    2) Under `DeviceAuthentication` in `Capabilities`, replace the UUID with the UUID of your tenant (can be seen in the output of `azsphere tenant list`)

- Build the code following Microsoft's instructions. 

- To simply deploy the code to multiple boards you can create a product and deploy the `.imagepackage` file from the `out` folder.

    1) `azsphere prd create -n sierra`

    2) `azsphere device-group deployment create --devicegroupname "Field Test" --productname "sierra" --filepath "<PATH>\out\Debug-3\Sierra2020.imagepackage"`

    3) (for each device) `azsphere dev ect --productname sierra`

## Setting the device locations

Once a device is deployed, its location can be set by following the instructions at the bottom of this README's [Azure page](/Azure.md) for setting up the device twins.

## Deployed device

Below is an image of what a deployed device looks like

![Deployed Device](/assets/device.jpg)