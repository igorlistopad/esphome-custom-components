# Generic Custom Component

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


This component can be used to create generic custom components in ESPHome
using the C++ (Arduino) API. This component should be used in cases where
none of ESPHome's abstraction layers (for example the "sensor", "binary sensor",
"switch", etc concepts) work well for your component.

Please first read [Custom Sensor Component](custom-sensor.md) guide, the same principles apply here.

The example below is an example of a custom component that can do anything you want really.

```cpp
#include "esphome.h"

class MyCustomComponent : public Component {
 public:
  void setup() override {
    // This will be called once to set up the component
    // think of it as the setup() call in Arduino
    pinMode(5, INPUT);
    pinMode(6, OUTPUT);
  }
  void loop() override {
    // This will be called very often after setup time.
    // think of it as the loop() call in Arduino
    if (digitalRead(5)) {
      digitalWrite(6, HIGH);

      // You can also log messages
      ESP_LOGD("custom", "The GPIO pin 5 is HIGH!");
    }
  }
};
```

(Store this file in your configuration directory, for example `my_custom_component.h`)

And in YAML:

```yaml
# Example configuration entry
esphome:
  includes:
    - my_custom_component.h

external_components:
  - source: github://igorlistopad/esphome-custom-components
    components: [custom, custom_component]

custom_component:
  - lambda: |-
      auto my_custom = new MyCustomComponent();
      return {my_custom};
    components: d
      - id: my_custom_id
```


### Configuration variables:

- **lambda** (**Required**, [lambda][esphome-docs-lambda]): The lambda to run for instantiating the binary sensor(s).
- **components** (*Optional*, list): A list of components to initialize.
  The length here must equal the number of items in the `return` statement of the `lambda`.
  This is useful if you need to give an `id` to the component you created.

## Native API Custom Component

If you want to communicate directly with Home Assistant via the [native API][esphome-docs-api]
you can use the [api::CustomAPIDevice][esphome-api-class-custom-api-device] class to declare actions
that can be performed from Home Assistant, as well as performing actions in Home Assistant.

```cpp
#include "esphome.h"

class MyCustomComponent : public Component, public CustomAPIDevice {
 public:
  void setup() override {
    // This will be called once to set up the component
    // think of it as the setup() call in Arduino
    pinMode(6, OUTPUT);

    // Declare a service "hello_world"
    //  - Service will be called "esphome.<NODE_NAME>_hello_world" in Home Assistant.
    //  - The service has no arguments
    //  - The function on_hello_world declared below will attached to the service.
    register_service(&MyCustomComponent::on_hello_world, "hello_world");

    // Declare a second service "start_washer_cycle"
    //  - Service will be called "esphome.<NODE_NAME>_start_washer_cycle" in Home Assistant.
    //  - The service has three arguments (type inferred from method definition):
    //     - cycle_duration: integer
    //     - silent: boolean
    //     - string_argument: string
    //  - The function start_washer_cycle declared below will attached to the service.
    register_service(&MyCustomComponent::on_start_washer_cycle, "start_washer_cycle",
                     {"cycle_duration", "silent", "string_argument"});

    // Subscribe to a Home Assistant state "sensor.temperature"
    //  - Each time the ESP connects or Home Assistant updates the state, the function
    //    on_state_changed will be called
    //  - The state is a string - if you want to use it as an int you must parse it manually
    subscribe_homeassistant_state(&MyCustomComponent::on_state_changed, "sensor.temperature");
  }
  void on_hello_world() {
    ESP_LOGD("custom", "Hello World!");

    if (is_connected()) {
      // Example check to see if a client is connected
    }
  }
  void on_start_washer_cycle(int cycle_duration, bool silent, std::string string_argument) {
    ESP_LOGD("custom", "Starting washer cycle!");
    digitalWrite(8, HIGH);
    // do something with arguments

    // Call a homeassistant service
    call_homeassistant_service("homeassistant.service");
  }
  void on_state_changed(std::string state) {
    ESP_LOGD(TAG, "Temperature has changed to %s", state.c_str());
  }
};
```

## MQTT Custom Component

In many cases however components should communicate with other appliances using the network.
That's why there is [mqtt::CustomMQTTDevice][esphome-api-class-custom-mqtt-device].
It is a helper class to create custom components that communicate using MQTT.

```cpp
#include "esphome.h"

class MyCustomComponent : public Component, public CustomMQTTDevice {
 public:
  void setup() override {
    // This will be called once to set up the component
    // think of it as the setup() call in Arduino
    pinMode(6, OUTPUT);

    subscribe("the/topic", &MyCustomComponent::on_message);

    // also supports JSON messages
    subscribe_json("the/json/topic", &MyCustomComponent::on_json_message);
  }
  void on_message(const std::string &payload) {
    if (payload == "ON") {
      digitalWrite(6, HIGH);
      publish("the/other/topic", "Hello World!");
    } else {
      digitalWrite(6, LOW);
      publish("the/other/topic", 42);
    }
  }
  void on_json_message(JsonObject root) {
    if (!root.containsKey("key"))
      return;

    int value = root["key"];
    // do something with Json Object

    // publish JSON using lambda syntax
    publish_json("the/other/json/topic", [=](JsonObject root2) {
      root2["key"] = "Hello World";
    });
  }
};
```

## See Also

- [API Reference][esphome-api-component]

[esphome-docs-api]: https://esphome.io/components/api/
[esphome-docs-lambda]: https://esphome.io/automations/templates/#config-lambda
[esphome-docs-external-components]: https://esphome.io/components/external_components/
[esphome-api-component]: https://api-docs.esphome.io/component_8h
[esphome-api-class-custom-api-device]: https://api-docs.esphome.io/classesphome_1_1api_1_1_custom_a_p_i_device
[esphome-api-class-custom-mqtt-device]: https://api-docs.esphome.io/classesphome_1_1mqtt_1_1_custom_m_q_t_t_device
[esphome-dev]: https://developers.esphome.io
[esphome-dev-removal-custom-components]: https://developers.esphome.io/blog/2025/02/19/about-the-removal-of-support-for-custom-components/