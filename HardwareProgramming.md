# Hardware Programming

Now that we have created the resources on Azure we can update the code to point to the IoT Hub and then deploy the devices.

## Setting up the ESP

To program the ESP, you hook it up to the USB-to-UART converter; ensure it is in programming mode (by ensuring IO0 is connected to ground) then programming it. If it refuses to program, try unpluging and re-pluging the power pin, as the ESP may have booted in run mode rather than program mode. To program it you clone [the repo](https://github.com/GP2020-Sierra/esp8266-code) and then use PlatformIO to program it.

[Hackster have a good guide with pictures](https://www.hackster.io/pratikdesai/how-to-program-esp8266-esp-01-module-with-arduino-uno-598166) on wiring if you have never programmed an ESP8266 before.

## Building the code
- Clone the repo `https://github.com/GP2020-Sierra/azure-sphere` 

- Update the `app_manifest.json` file

    1) Under `AllowedConnections` in `Capabilities`, replace `sierra.azure-devices.net` with the hostname listed for your IoT Hub in Azure.

    2) Under `DeviceAuthentication` in `Capabilities`, replace the UUID with the UUID of your tenant (can be seen in the output of `azsphere tenant list`)
    
    3) Under `CmdArgs`, replace the ID with the `ID Scope` from your IoT Hub DPS in Azure.

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
