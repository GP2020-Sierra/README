# Hardware Setup

Follow [Microsoft's guide](https://docs.microsoft.com/en-gb/azure-sphere/install/overview) to setup a development environment

## Physical setup

Solder pins into:
* The PMOD Uart connector edge most row; underneath CLICK socket 2
* The 5v header next to the CLICK 2 (optional; for fan)
* The ESP8266 pin P13

When attaching the DHT11 (assuming your using the same boards we are):
* Seperate out the signal (green wire)
* Plug the signal wire into pin 13 on the ESP (we angled the pin to make it fit better)
* Plug the power pair into the PMOD connector, with the blank in the pin socket trailing over the edge

Double check the power config, as it is easy to make the DHT11's melt (We blew one up unintentionally, it was fun)

## Software setup

We used VSCode for building all our projects

You also need the AzureSphere SDK installed, as well as ninja added to the path. Microsoft has guides on setting this up.

### Create a tenant

To create a tenant, you start an azure sphere command line (either with the provided script or by adding it straight into the path) and then call `azsphere tenant create --name [name]`, noting that this requires a device attached, and one device can only create at most one tenant.

You select a tenant by calling `azsphere tenant select --tenantid [id]`, with the id being found by either listing them or during creation (noting creation auto selects tenants (at least at first))

If you have the right tenant selected, you can claim the devices with `azsphere dev claim`

