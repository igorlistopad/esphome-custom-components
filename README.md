# ESPHome Custom Components

Brings back support for `custom` and `custom_component` to ESPHome after removing **Custom Components** from ESPHome in
the 2025.2.0 release.

The **Custom Components** code hosted in this repository is a copy extracted from the [ESPHome][github-esphome]
2024.12.4 release.

#### Changes:
  - Update components to use *_schema(...) to support [breaking change in ESPHome 2025.11.0][esphome-dev-blog-schema]

ⓘ The code will be updated as needed to support new releases of the ESPHome.

> [!IMPORTANT]
> This repository is created to support old projects using Custom Components, not recommended for new configurations.
> Please look at creating a real ESPHome component and "importing" it into your configuration with
> [External Components][esphome-docs-external-components].
>
> You can find some basic documentation on creating your own components at
> [ESPHome developer documentation][esphome-dev].

## Usage

Add support for **Custom Components** into your YAML configuration using
the [external components][esphome-docs-external-components] feature:

```yaml
external_components:
  - source: github://igorlistopad/esphome-custom-components
    components: [custom, custom_component]
```

## Documentation

This documentation is a modified copy extracted from the [ESPHome-Docs][github-esphome-docs] 2024.12.4 release.

- [Generic Custom Component](docs/custom-component.md)
- [Custom I²C Device](docs/custom-i2c.md)
- [Custom SPI Device](docs/custom-spi.md)
- [Custom UART Device](docs/custom-uart.md)
- [Custom Sensor](docs/custom-sensor.md)
- [Custom Binary Sensor](docs/custom-binary-sensor.md)
- [Custom Climate](docs/custom-climate.md)
- [Custom Cover](docs/custom-cover.md)
- [Custom Light Output](docs/custom-light.md)
- [Custom Output](docs/custom-output.md)
- [Custom Switch](docs/custom-switch.md)
- [Custom Text Sensor](docs/custom-text-sensor.md)

---

Don't forget to **star the repository** if you found it useful! ⭐

[github-esphome]: https://github.com/esphome/esphome/
[github-esphome-docs]: https://github.com/esphome/esphome-docs/
[esphome-docs-external-components]: https://esphome.io/components/external_components/
[esphome-dev]: https://developers.esphome.io
[esphome-dev-blog-schema]: https://developers.esphome.io/blog/2025/05/14/_schema-deprecations/