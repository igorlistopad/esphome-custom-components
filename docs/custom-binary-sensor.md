# Custom Binary Sensor

> [!WARNING]
> [Custom Components are deprecated][esphome-dev-removal-custom-components], not recommended for new configurations.
> Please look at creating a real ESPHome component and "importing" it into your configuration with
> [External Components][esphome-docs-external-components].
>
> You can find some basic documentation on creating your own components at [Developer site][esphome-dev].

> [!WARNING]
> While ESPHome developers try to keep the ESPHome YAML configuration options as stable as possible,
> the ESPHome API is less stable. If something in the APIs needs to be changed in order for something else to work,
> ESPHome developers will do so.

This component can be used to create custom binary sensors in ESPHome using the C++ (Arduino) API.

Please first read [Custom Sensor Component](custom-sensor.md) guide, the same principles apply here and binary
sensors are very similar to sensors internally.

The example below is an example of a custom binary sensor; this custom sensor is essentially the
same as the gpio binary sensor.

```cpp
#include "esphome.h"

class MyCustomBinarySensor : public PollingComponent, public BinarySensor {
 public:
  // constructor
  MyCustomBinarySensor() : PollingComponent(15000) {}

  void setup() override {
    // This will be called by App.setup()
    pinMode(5, INPUT);
  }
  void update() override {
    // This will be called every "update_interval" milliseconds.

    // Publish an OFF state
    bool state = digitalRead(5);
    publish_state(state);
  }
};
```

(Store this file in your configuration directory, for example `my_binary_sensor.h`)

And in YAML:

```yaml
# Example configuration entry
esphome:
  includes:
    - my_binary_sensor.h

external_components:
  - source: github://igorlistopad/esphome-custom-components
    components: [custom, custom_component]

binary_sensor:
  - platform: custom
    lambda: |-
      auto my_custom_sensor = new MyCustomBinarySensor();
      App.register_component(my_custom_sensor);
      return {my_custom_sensor};
    binary_sensors:
      name: "My Custom Binary Sensor"
```

### Configuration variables:

- **lambda** (**Required**, [lambda][esphome-docs-lambda]): The lambda to run for instantiating the binary sensor(s).
- **binary_sensors** (**Required**, list): A list of binary sensors to initialize.
  The length here must equal the number of items in the `return` statement of the `lambda`.
  - All options from [Binary Sensor][esphome-docs-config-binary-sensor].

## See Also

- [Binary Sensor Component][esphome-docs-binary-sensor]
- [API Reference][esphome-api-binary-sensor]

[esphome-docs-lambda]: https://esphome.io/automations/templates/#config-lambda
[esphome-docs-binary-sensor]: https://esphome.io/components/binary_sensor/
[esphome-docs-config-binary-sensor]: https://esphome.io/components/binary_sensor/#config-binary_sensor
[esphome-docs-external-components]: https://esphome.io/components/external_components/
[esphome-api-binary-sensor]: https://api-docs.esphome.io/binary__sensor_8h
[esphome-dev]: https://developers.esphome.io
[esphome-dev-removal-custom-components]: https://developers.esphome.io/blog/2025/02/19/about-the-removal-of-support-for-custom-components/