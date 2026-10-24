# Old house boiler control (archived)

Home Assistant config from the previous house, kept for reference for when there is a boiler to control again. The automations are no longer in Home Assistant.

The boiler was a Vaillant ecoTEC plus 630, controlled over eBUS by [ebusd](https://github.com/john30/ebusd) through MQTT (`ebusd/bai/...`). `switch.central_heating`, `switch.hot_water` and the `sensor.ecotec_plus_630_*` sensors came from that setup.

## What it did

- **Weather-compensated flow temperature** (`templates.yaml`). `sensor.boiler_desired_flow_temperature` follows a weather curve on `sensor.outside_temperature`, set by the helpers in `helpers.yaml`. `sensor.boiler_flow_boost_coefficient` raises it when a Better Thermostat room is below its target, weighted by `input_number.boiler_boost_coeffient`.
- **Boiler flow temperature** (automation). Every 30 seconds it sends that flow temperature to the boiler with `SetModeOverride`, or 75 °C while hot water is heating.
- **Boiler central heating control** (automation). Turns central heating on when `sensor.boiler_radiator_demand` (total TRV valve opening) reaches `input_number.boiler_min_radiator_demand`, and off below it, unless hot water is heating.
- **Boiler hot water schedule** (automation). Turns hot water on at 03:00, 09:00 and 13:00 unless away mode is on.
- **Boiler hot water boost** (automation). Runs when hot water turns on or the boost button is pressed. It pauses central heating, then turns hot water off once the boiler's average flow and return temperature reaches `input_number.boiler_dhw_temp`, or after the timeouts.

## Files

- `automations.yaml`: the four automations exactly as they were in `automations.yaml`.
- `templates.yaml`: the two template sensors exactly as they were in `templates/templates.yaml`.
- `helpers.yaml`: the curve settings and boost button, written as YAML with their last values.

## Reusing it

- `sensor.boiler_radiator_demand` and `input_number.boiler_min_radiator_demand` are still live in the current house.
- Boiler central heating control has a device condition on a device that no longer exists. Replace it with a state condition on the new hot water switch.
- The flow temperature payload is specific to ebusd and this boiler. A different boiler needs its own way of setting flow temperature.
