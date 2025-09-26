# Custom Cover

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

This component can be used to create custom covers in ESPHome using the C++ (Arduino) API.

Please first read [Custom Sensor Component](custom-sensor.md) guide, the same principles apply here.

The example below is an example of a custom cover - all covers must override two methods:

- `get_traits`: This should return a [CoverTraits][esphome-api-class-cover-traits] object representing
  the capabilities of the cover.
- `control`: This receives a [CoverCall][esphome-api-class-cover-call] object that contains
  the command the user tried to set.

```cpp
#include "esphome.h"

class MyCustomCover : public Component, public Cover {
 public:
  void setup() override {
    // This will be called by App.setup()
    pinMode(5, INPUT);
  }
  CoverTraits get_traits() override {
    auto traits = CoverTraits();
    traits.set_is_assumed_state(false);
    traits.set_supports_position(true);
    traits.set_supports_tilt(false);
    traits.set_supports_stop(true);
    return traits;
  }
  void control(const CoverCall &call) override {
    // This will be called every time the user requests a state change.
    if (call.get_position().has_value()) {
      float pos = *call.get_position();
      // Write pos (range 0-1) to cover
      // ...

      // Publish new state
      this->position = pos;
      this->publish_state();
    }
    if (call.get_stop()) {
      // User requested cover stop
    }
  }
};
```

(Store this file in your configuration directory, for example `my_cover.h`)

And in YAML:

```yaml
# Example configuration entry
esphome:
  includes:
    - my_cover.h

external_components:
  - source: github://igorlistopad/esphome-custom-components
    components: [custom, custom_component]

cover:
  - platform: custom
    lambda: |-
      auto my_custom_cover = new MyCustomCover();
      App.register_component(my_custom_cover);
      return {my_custom_cover};
    covers:
      - name: "My Custom Cover"
```

Configuration variables:

- **lambda** (**Required**, [lambda][esphome-docs-lambda]): The lambda to run for instantiating the cover(s).
- **covers** (**Required**, list): A list of covers to initialize.
  The length here must equal the number of items in the `return` statement of the `lambda`.
  - All options from [Cover][esphome-docs-config-cover].

## See Also

- [Cover Component][esphome-docs-cover]
- [API Reference][esphome-api-cover]

[esphome-docs-lambda]: https://esphome.io/automations/templates/#config-lambda
[esphome-docs-cover]: https://esphome.io/components/cover/
[esphome-docs-config-cover]: https://esphome.io/components/cover/#config-cover
[esphome-docs-external-components]: https://esphome.io/components/external_components/
[esphome-api-cover]: https://api-docs.esphome.io/cover_8h
[esphome-api-class-cover-traits]: https://api-docs.esphome.io/classesphome_1_1cover_1_1_cover_traits
[esphome-api-class-cover-call]: https://api-docs.esphome.io/classesphome_1_1cover_1_1_cover_call
[esphome-dev]: https://developers.esphome.io
[esphome-dev-removal-custom-components]: https://developers.esphome.io/blog/2025/02/19/about-the-removal-of-support-for-custom-components/