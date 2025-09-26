# Custom UART Device

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

Lots of devices communicate using the UART protocol. If you want to integrate
a device into ESPHome that uses this protocol you can pretty much use almost
all Arduino-based code because ESPHome has a nice abstraction over the UART bus.

See the other custom component guides for how to register components and make
them publish values.

```cpp
#include "esphome.h"

class MyCustomComponent : public Component, public UARTDevice {
 public:
  MyCustomComponent(UARTComponent *parent) : UARTDevice(parent) {}

  void setup() override {
    // nothing to do here
  }
  void loop() override {
    // Use Arduino API to read data, for example
    String line = readString();
    int i = parseInt();
    while (available()) {
      char c = read();
    }
    // etc
  }
};
```

And in YAML:

```yaml
# Example configuration entry
esphome:
  includes:
    - my_custom_component.h

uart:
  id: uart_bus
  tx_pin: GPIOXX
  rx_pin: GPIOXX
  baud_rate: 9600

external_components:
  - source: github://igorlistopad/esphome-custom-components
    components: [custom, custom_component]

custom_component:
  - lambda: |-
      auto my_custom = new MyCustomComponent(id(uart_bus));
      return {my_custom};
```

## See Also

- [UART Bus][esphome-docs-uart]
- [API Reference][esphome-api-uart]

[esphome-docs-uart]: https://esphome.io/components/uart/
[esphome-docs-external-components]: https://esphome.io/components/external_components/
[esphome-api-uart]: https://api-docs.esphome.io/uart_8h
[esphome-dev]: https://developers.esphome.io
[esphome-dev-removal-custom-components]: https://developers.esphome.io/blog/2025/02/19/about-the-removal-of-support-for-custom-components/