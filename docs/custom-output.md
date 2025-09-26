# Custom Output

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

This component can be used to create custom binary and float [outputs][esphome-docs-output]
in ESPHome using the C++ (Arduino) API.

Please first read [Custom Sensor Component](custom-sensor.md) guide, the same principles apply here.

The example below is an example of a custom float output; this custom output is essentially the
same as the [ESP8266 software PWM output][esphome-docs-output-esp8266-pwm].

```cpp
#include "esphome.h"
using namespace esphome;

class MyCustomFloatOutput : public Component, public FloatOutput {
 public:
  void setup() override {
    // This will be called by App.setup()
    pinMode(5, OUTPUT);
  }

  void write_state(float state) override {
    // state is the amount this output should be on, from 0.0 to 1.0
    // we need to convert it to an integer first
    int value = state * 1024;
    analogWrite(5, value);
  }
};

// Custom binary output, for exposing binary states
class MyCustomBinaryOutput : public Component, public BinaryOutput {
 public:
  void setup() override {
    // This will be called by App.setup()
    pinMode(5, OUTPUT);
  }

  void write_state(bool state) override {
    digitalWrite(5, state);
  }
};
```

(Store this file in your configuration directory, for example `my_output.h`)

And in YAML:

```yaml
# Example configuration entry
esphome:
  includes:
    - my_output.h

external_components:
  - source: github://igorlistopad/esphome-custom-components
    components: [custom, custom_component]

output:
  - platform: custom
    type: float
    lambda: |-
      auto my_custom_float_output = new MyCustomFloatOutput();
      App.register_component(my_custom_float_output);
      return {my_custom_float_output};
    outputs:
      - id: custom_float

  - platform: custom
    type: binary
    lambda: |-
      auto my_custom_binary_output = new MyCustomBinaryOutput();
      App.register_component(my_custom_binary_output);
      return {my_custom_binary_output};
    outputs:
      - id: custom_binary
```

### Configuration variables:

- **type** (**Required**, string): The type of output. One of `binary` and `float`.
- **lambda** (**Required**, [lambda][esphome-docs-lambda]): The lambda to run for instantiating the output(s).
- **outputs** (**Required**, list): A list of outputs to initialize.
  The length here must equal the number of items in the `return` statement of the `lambda`.
    - All options from [Output][esphome-docs-config-output].

## See Also

- [Output Component][esphome-docs-output]
- [binary_output.h][esphome-api-binary-output], [float_output.h][esphome-api-float-output]

[esphome-docs-lambda]: https://esphome.io/automations/templates/#config-lambda
[esphome-docs-output]: https://esphome.io/components/output/
[esphome-docs-config-output]: https://esphome.io/components/output/#config-output
[esphome-docs-output-esp8266-pwm]: https://esphome.io/components/output/esp8266_pwm/
[esphome-docs-external-components]: https://esphome.io/components/external_components/
[esphome-api-binary-output]: https://api-docs.esphome.io/binary__output_8h
[esphome-api-float-output]: https://api-docs.esphome.io/float__output_8h
[esphome-dev]: https://developers.esphome.io
[esphome-dev-removal-custom-components]: https://developers.esphome.io/blog/2025/02/19/about-the-removal-of-support-for-custom-components/