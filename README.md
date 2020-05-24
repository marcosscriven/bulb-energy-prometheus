# Bulb Energy Prometheus exporter

This is a [Prometheus](https://prometheus.io/) exporter for [Bulb Energy](https://bulb.co.uk/) customers with SMETS2 smart meters. It allows you to fetch electricity and gas meter readings and exposes them as a Prometheus endpoint.

This relies on Bulb's SmartThings integration to exfiltrate the meter readings. It will not work with other energy suppliers.

Ideally we would be able to integrate directly with the SMETS2 home area network, but this (still!) doesn't appear to be possible.

Electricity meter readings are updated every minute or so, but gas meter readings are only updated a few times per day (due to battery life).

## Configuration

* First, you'll need to set up the [SmartThings integration](https://help.bulb.co.uk/hc/en-us/articles/360034651651-Setting-up-SmartThings-Energy-Control-STEC-) on your Chameleon IHD.
* Install this exporter using `pip3 install git+https://github.com/russss/bulb-energy-prometheus#egg=bulb-energy-prometheus`.
* Next, you'll need to generate a personal access token on the SmartThings website. The token needs the "List all devices" and "See all devices" permissions.


Now you can run the exporter as `SMARTTHINGS_API_TOKEN=<token> bulb-energy-prometheus`, where `<token>` is the token you generated in the previous step. The Prometheus endpoint should be visible at `http://localhost:8023`. An example of the exported metrics is:

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