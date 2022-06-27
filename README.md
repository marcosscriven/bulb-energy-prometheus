# :chart_with_downwards_trend: Bulb Energy Prometheus exporter

> **This project is now archived**. At the time of writing (June 2022), it still works fine, however the [current financial status](https://www.bbc.co.uk/news/business-59373198) of Bulb means this likely won't last for ever.
>
> The [Hildebrand Glow IHD](https://shop.glowmarkt.com/products/display-and-cad-combined-for-smart-meter-customers) now supports publishing energy data to a [MQTT server](https://medium.com/@joshua.cooper/glow-local-mqtt-f69b776b7af4) entirely over your local network, removing the need to rely on energy-supplier-specific hacks and/or cloud services. I'm now using one of these rather than this code.

## Intro

This is a [Prometheus](https://prometheus.io/) exporter for [Bulb Energy](https://bulb.co.uk/) customers with SMETS2 smart meters. It allows you to fetch electricity and gas meter readings and exposes them as a Prometheus endpoint.

This relies on Bulb's SmartThings integration to exfiltrate the meter readings. It will not work with other energy suppliers.

~Ideally we would be able to integrate directly with the SMETS2 home area network, but this (still!) doesn't appear to be possible.~ (See above.)

Electricity meter readings are sent to SmartThings every minute or so, but gas meter readings are only updated a few times per day (due to battery life). This exporter polls the SmartThings API every 10 seconds for an updated reading - it's technically possible to subscribe to event notifications but this requires opening a port for webhooks and life is too short.

## :gear: Configuration

* First, you'll need to set up the [SmartThings integration](https://help.bulb.co.uk/hc/en-us/articles/360034651651-Setting-up-SmartThings-Energy-Control-STEC-) on your Chameleon IHD.
* Next, you'll need to [generate a personal access token on the SmartThings website](https://account.smartthings.com/tokens). The token needs the "List all devices" and "See all devices" permissions.

### Install using pip

Install this exporter using `pip3 install git+https://github.com/russss/bulb-energy-prometheus#egg=bulb-energy-prometheus`.

You'll need to run the `bulb-energy-prometheus` process with the `SMARTTHINGS_API_TOKEN` environment variable set to the API token you generated earlier. You can do this with `SMARTTHINGS_API_TOKEN=<token> bulb-energy-prometheus`.

The Prometheus endpoint should be visible at `http://localhost:8023`.

You'll want to run the exporter under a process supervisor, as it will crash if it can't access the SmartThings API.

### Docker

Alternatively, you can use [the Docker image](https://github.com/russss/bulb-energy-prometheus/pkgs/container/bulb-energy-prometheus):

`docker pull ghcr.io/russss/bulb-energy-prometheus`

Set the `SMARTTHINGS_API_TOKEN` environment variable as above.

There are two supported platforms: `arm64` and `amd64`. Hence, this docker image works on Raspberry Pi 4s.

## Example output
An example of the exported metrics:
```
# HELP bulb_electricity_used_kwh Electricity meter reading
# TYPE bulb_electricity_used_kwh gauge
bulb_electricity_used_kwh 787.517
# HELP bulb_gas_used_units Gas meter reading
# TYPE bulb_gas_used_units gauge
bulb_gas_used_units 1871.826
# HELP smartthings_refresh_time_seconds Time taken to fetch data from SmartThings
# TYPE smartthings_refresh_time_seconds summary
smartthings_refresh_time_seconds_count 3.0
smartthings_refresh_time_seconds_sum 0.13676668800000036
# TYPE smartthings_refresh_time_seconds_created gauge
smartthings_refresh_time_seconds_created 1.590315433754121e+09
```
