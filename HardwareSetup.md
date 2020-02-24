# Hardware Setup

Follow [Microsoft's guide](https://docs.microsoft.com/en-gb/azure-sphere/install/overview) to setup a development environment

## Physical setup
TODO soldering etc

## Software setup
### Create a tenant
TODO creating tenet and claiming devices

## Building the code
Clone the repo `https://github.com/GP2020-Sierra/azure-sphere` and build the code following Microsoft's instructions.

To simply deploy the code to multiple boards you can create a product and deploy the `.imagepackage` file from the `out` folder.
1) `azsphere prd create -n sierra`
2) `azsphere device-group deployment create --devicegroupname "Field Test" --productname "sierra" --filepath "<PATH>\out\Debug-3\Sierra2020.imagepackage"`
3) (for each device) `azsphere dev ect --productname sierra`