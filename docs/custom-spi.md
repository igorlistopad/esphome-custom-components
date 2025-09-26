# Custom SPI Device

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

Lots of devices communicate using the SPI protocol. If you want to integrate
a device into ESPHome that uses this protocol you can pretty much use almost
all Arduino-based code because the `SPI` library is also available in ESPHome.

See the other custom component guides for how to register components and make
them publish values.

Please refer to the SPI library docs for more information.

```cpp
#include "esphome.h"

class MyCustomComponent : public Component {
 public:
  void setup() override {
    SPI.pins(SCK_PIN, MISO_PIN, MOSI_PIN, CS_PIN);
    SPI.begin();
  }
  void loop() override {
    SPI.beginTransaction(...)

    SPI.write(0x42);
  }
};
```
## See Also

- [SPI Bus][esphome-docs-spi]
- [API Reference][esphome-api-spi]

[esphome-docs-spi]: https://esphome.io/components/spi/
[esphome-docs-external-components]: https://esphome.io/components/external_components/
[esphome-api-spi]: https://api-docs.esphome.io/spi_8h
[esphome-dev]: https://developers.esphome.io
[esphome-dev-removal-custom-components]: https://developers.esphome.io/blog/2025/02/19/about-the-removal-of-support-for-custom-components/