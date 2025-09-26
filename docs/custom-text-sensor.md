# Custom Text Sensor

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

This component can be used to create custom text sensors in ESPHome using the C++ (Arduino) API.

Please first read :doc:`/components/sensor/custom` guide, the same principles apply here and text
sensors are very similar to sensors internally.

The example below is an example of a custom text sensor which constantly publishes
the message "Hello World!".

```cpp
#include "esphome.h"

class MyCustomTextSensor : public PollingComponent, public TextSensor {
 public:
  // constructor
  MyCustomTextSensor() : PollingComponent(15000) {}

  void setup() override {
    // This will be called by App.setup()
  }
  void update() override {
    // This will be called every "update_interval" milliseconds.
    // Publish state
    publish_state("Hello World!");
  }
};
```

(Store this file in your configuration directory, for example `my_text_sensor.h`)

And in YAML:

```yaml
# Example configuration entry
esphome:
  includes:
    - my_text_sensor.h

external_components:
  - source: github://igorlistopad/esphome-custom-components
    components: [custom, custom_component]

text_sensor:
  - platform: custom
    lambda: |-
      auto my_custom_sensor = new MyCustomTextSensor();
      App.register_component(my_custom_sensor);
      return {my_custom_sensor};

    text_sensors:
      - name: "My Custom Text Sensor"
```

### Configuration variables:

- **lambda** (**Required**, [lambda][esphome-docs-lambda]): The lambda to run for instantiating the text sensor(s).
- **text_sensors** (**Required**, list): A list of text sensors to initialize.
  The length here must equal the number of items in the `return` statement of the `lambda`.
  - All options from [Text Sensor][esphome-docs-config-text-sensor].

## See Also

- [Text Sensor Component][esphome-docs-text-sensor]
- [API Reference][esphome-api-text-sensor]

[esphome-docs-lambda]: https://esphome.io/automations/templates/#config-lambda
[esphome-docs-text-sensor]: https://esphome.io/components/text_sensor/
[esphome-docs-config-text-sensor]: https://esphome.io/components/text_sensor/#config-text_sensor
[esphome-docs-external-components]: https://esphome.io/components/external_components/
[esphome-api-text-sensor]: https://api-docs.esphome.io/text__sensor_8h
[esphome-dev]: https://developers.esphome.io
[esphome-dev-removal-custom-components]: https://developers.esphome.io/blog/2025/02/19/about-the-removal-of-support-for-custom-components/