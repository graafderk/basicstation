# LoRa Basics™ Station
This repository is a fork of the LoRa Basics™ Station made to work with the RAK2278 concentrator board on a RAK2004 v2 PI HAT.
For a full description of the LoRa Basics™ Station software, go to https://github.com/lorabasics/basicstation

In addition to the examples available from the original repository, an example is provided for connection to Actility's ThingPark LNS. 

## Prerequisites

Building the Station binary from source, requires

* gcc (C11 with GNU extensions)
* GNU make
* git
* bash

## First Steps

The following is a three-step quick start guide on how to build and run Station. It uses a Raspberry Pi as host platform and assumes a Concentrator Reference Design 1.5 compatible radio board connected via SPI, and assumes that SPI port is enabled using the [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) tool. In this example the build process is done on the target platform itself (the make environment also supports cross compilation in which case the toolchain is expected in `~/toolchain-$platform` - see [setup.gmk](setup.gmk)).

#### Step 1: Cloning the Station Repository

``` sourceCode
git clone https://github.com/lorabasics/basicstation.git
```

#### Step 2: Compiling the Station Binary

``` sourceCode
cd basicstation
make platform=rpi variant=std
```

The build process consists of the following steps:

*  Fetch and build dependencies, namely [mbedTLS](https://github.com/ARMmbed/mbedtls) and [libloragw](https://github.com/Lora-net/lora_gateway)
*  Setup build environment within subdirectory `build-$platform-$variant/`
*  Compile station source files into executable `build-$platform-$variant/bin/station`

#### Step 3: Running the Actility Example Configuration on a Raspberry Pi

``` sourceCode
cd examples/live-s2.sm.tc
RADIODEV=/dev/spidev0.0 ../../build-rpi-std/bin/station
```

**Note:** The SPI device for the radio MAY be passed as an environment variable using `RADIODEV`.

The example configuration connects to a public test server [s2.sm.tc](wss://s2.sm.tc) through which Station fetches all required credentials and a channel plan matching the region as determined from the IP address of the gateway. Provided there are active LoRa devices in proximity, received LoRa frames are printed in the log output on `stderr`.

## Instruction for Supported Platfroms

#### Corecell Platform (Raspberry Pi as HOST + [SX1302CxxxxGW Concentrator](https://www.semtech.com/products/wireless-rf/lora-gateways/sx1302cxxxgw1))

##### Compile and Running the Example

``` sourceCode
cd basicstation
make platform=corecell variant=std
cd examples/corecell
./start-station.sh -l ./lns-ttn
```

This example configuration for Corecell connects to [The Things Network](https://www.thethingsnetwork.org/) public LNS. The example [station.conf](station.conf) file holds the required radio configurations and station fetches the channel plan from the configured LNS url ([tc.uri](tc.uri)).

Note: SPI port requires to be activated on Raspberry Pi thanks to [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) tool.

#### PicoCell Gateway (Linux OS as HOST + [SX1308 USB Reference design](https://www.semtech.com/products/wireless-rf/lora-gateways/sx1308p868gw))


##### Compile and Running the Example

``` sourceCode
cd basicstation
make platform=linuxpico variant=std
cd examples/live-s2.sm.tc
RADIODEV=/dev/ttyACM0 ../../build-linuxpico-std/bin/station
```

**Note:** The serial device for the PicoCell MAY be passed as an environment variable using `RADIODEV`.

## Next Steps

## Usage

The Station binary accepts the following command-line options:

```
Usage: station [OPTION...]

  -d, --daemon               First check if another process is still alive. If
                             so do nothing and exit. Otherwise fork a worker
                             process to operate the radios and network
                             protocols. If the subprocess died respawn it with
                             an appropriate back off.
  -f, --force                If a station process is already running, kill it
                             before continuing with requested operation mode.
  -h, --home=DIR             Home directory for configuration files. Default is
                             the current working directory. Overrides
                             environment STATION_DIR.
  -i, --radio-init=cmd       Program/script to run before reinitializing radio
                             hardware. By default nothing is being executed.
                             Overrides environment STATION_RADIOINIT.
  -k, --kill                 Kill a currently running station process.
  -l, --log-level=LVL|0..7   Set a log level LVL=#loglvls# or use a numeric
                             value. Overrides environment STATION_LOGLEVEL.
  -L, --log-file=FILE[,SIZE[,ROT]]
                             Write log entries to FILE. If FILE is '-' then
                             write to stderr. Optionally followed by a max file
                             SIZE and a number of rotation files. If ROT is 0
                             then keep only FILE. If ROT is 1 then keep one
                             more old log file around. Overrides environment
                             STATION_LOGFILE.
  -N, --no-tc                Do not connect to a LNS. Only run CUPS
                             functionality.
  -p, --params               Print current parameter settings.
  -t, --temp=DIR             Temp directory for frequently written files.
                             Default is /tmp. Overrides environment
                             STATION_TEMPDIR.
  -x, --eui-prefix=id6       Turn MAC address into EUI by adding this prefix.
                             If the argument has value ff:fe00:0 then the EUI
                             is formed by inserting FFFE in the middle. If
                             absent use MAC or routerid as is. Overrides
                             environment STATION_EUIPREFIX.
  -?, --help                 Give this help list
      --usage                Give a short usage message
  -v, --version              Print station version.

Mandatory or optional arguments to long options are also mandatory or optional
for any corresponding short options.
```
