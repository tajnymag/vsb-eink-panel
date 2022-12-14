# VŠB E-Ink - Panel firmware

This repository contains the firmware for the VŠB E-Ink panels.

The repository contains up-to-date built firmware binaries in the [releases](https://github.com/tajnymag/vsb-eink-panel/releases) section.

## Building

The firmware is built on top of [esp-idf](https://github.com/espressif/esp-idf) and [ESP-IDF-InkPlate](https://github.com/turgu1/ESP-IDF-InkPlate). The Inkplate library is however not vendored in the repository, instead, it is referenced as a git submodule. To build the firmware, you need to clone the repository with the `--recursive` flag.

After cloning the repository and setting up IDF, you can build the firmware by running `idf.py build` in the root of the repository. Just like any other ESP-IDF project, you can also use `idf.py menuconfig` to configure the build.

## Provisioning

Right now, provisioning is done semi-manually by creating a `csv` file describing content of the NVS partition of the panel to be set up and flashing a generated partition image afterward to a panel. A wizard with automatic panel identification is yet to be written.

```csv
# provisioning/example.nvs.csv
key,type,encoding,value
eeprom,namespace,,
eeprom,data,base64,VxYAAwMDAwMDAwAAAQIBAQICAQAAAgICAQICAQAAAAICAgICAQAAAwMCAQEBAgAAAwMCAgEBAgAAAgECAQIBAgAAAwMDAgICAgAU7g==
vsb_eink,namespace,,
wifi_ssid,data,string,"TUONET-IOT"
wifi_password,data,string,"tuo12345"
websocket_url,data,string,"ws://192.168.0.175:8888/ws"
```

Keys `wifi_ssid`, `wifi_password` and `websocket_url` are self-explanatory. `eeprom` is a special section containing waveform data for the underlying panel library. The data is so far to be taken as hardcoded, because to set it up, an image of a real panel rom had to be dissected.

After the `csv` file is ready, the partition image can be generated by running the following command:

```bash
python provisioning/provision_panel.py --output example_nvs_image.bin images/example.nvs.csv
```

Or, if instead it was desirable to flash the image directly to a connected panel, replace the `--output` argument with `--flash`:

```bash
python provisioning/provision_panel.py --flash images/example.nvs.csv
```