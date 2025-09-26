# Custom Switch

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

This component can be used to create custom switches in ESPHome using the C++ (Arduino) API.

Please first read [Custom Sensor Component](custom-sensor.md) guide, the same principles apply here.

The example below is an example of a custom switch; this custom switch is essentially the
same as the gpio switch implementation.

```cpp
#include "esphome.h"

class MyCustomSwitch : public Component, public Switch {
 public:
  void setup() override {
    // This will be called by App.setup()
    pinMode(5, INPUT);
  }
  void write_state(bool state) override {
    // This will be called every time the user requests a state change.

    digitalWrite(5, state);

    // Acknowledge new state by publishing it
    publish_state(state);
  }
};
```

(Store this file in your configuration directory, for example `my_switch.h`)

And in YAML:

```yaml
# Example configuration entry
esphome:
  includes:
    - my_switch.h

external_components:
  - source: github://igorlistopad/esphome-custom-components
    components: [custom, custom_component]

switch:
  - platform: custom
    lambda: |-
      auto my_custom_switch = new MyCustomSwitch();
      App.register_component(my_custom_switch);
      return {my_custom_switch};

    switches:
      - id: my_custom_switch
        name: "My Custom Switches"
```

Configuration variables:

- **lambda** (**Required**, [lambda][esphome-docs-lambda]): The lambda to run for instantiating the switch(es).
- **switches** (**Required**, list): A list of switches to initialize.
  The length here must equal the number of items in the `return` statement of the `lambda`.
  - All options from [Switch][esphome-docs-config-switch].

## See Also

- [Switch Component][esphome-docs-switch]
- [API Reference][esphome-api-switch]

[esphome-docs-lambda]: https://esphome.io/automations/templates/#config-lambda
[esphome-docs-switch]: https://esphome.io/components/switch/
[esphome-docs-config-switch]: https://esphome.io/components/switch/#config-switch
[esphome-docs-external-components]: https://esphome.io/components/external_components/
[esphome-api-switch]: https://api-docs.esphome.io/switch_8h
[esphome-dev]: https://developers.esphome.io
[esphome-dev-removal-custom-components]: https://developers.esphome.io/blog/2025/02/19/about-the-removal-of-support-for-custom-components/