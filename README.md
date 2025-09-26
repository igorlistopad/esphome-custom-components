# ESPHome Custom Components

Brings back support for `custom` and `custom_component` to ESPHome after removal of custom components from ESPHome
in the 2025.2.0 release.

The custom components code hosted in this repository is a copy extracted from the [ESPHome][github-esphome] 2024.12.4
release.

The code will be updated as needed to support new releases of the ESPHome.

> [!IMPORTANT]
> This repository is created to support old projects using Custom Components, not recommended for new configurations.\
> Please look at creating a real ESPHome component and "importing" it into your configuration with
> [External Components][esphome-docs-external-components].
>
> You can find some basic documentation on creating your own components at [Developer site][esphome-dev].

## Usage

Add support for custom components into your YAML configuration using
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

[github-esphome]: https://github.com/esphome/esphome/
[github-esphome-docs]: https://github.com/esphome/esphome-docs/
[esphome-dev]: https://developers.esphome.io
[esphome-docs-external-components]: https://esphome.io/components/external_components/