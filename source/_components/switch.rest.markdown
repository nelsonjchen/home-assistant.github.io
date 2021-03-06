---
layout: page
title: "RESTful Switch"
description: "Instructions how to integrate REST switches into Home Assistant."
date: 2015-09-14 19:10
sidebar: true
comments: false
sharing: true
footer: true
logo: restful.png
ha_category: Switch
ha_release: 0.7.6
---


The `rest` switch platform allows you to control a given endpoint that supports a [RESTful API](https://en.wikipedia.org/wiki/Representational_state_transfer). The switch can get the state via GET and set the state via POST on a given REST resource.

To enable this switch, add the following lines to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
switch:
  - platform: rest
    resource: http://IP_ADDRESS/ENDPOINT
```

Configuration variables:

- **resource** (*Required*): The resource or endpoint that contains the value.
- **name** (*Optional*): Name of the REST switch.
  - **method** (*Optional*): HTTP method to use (`post` or `put`). Defaults to `post`.
- **body_on** (*Optional*): The body of the POST request that commands the switch to become enabled. Default is "ON". This value can be a [template](/topics/templating/).
- **body_off** (*Optional*): The body of the POST request that commands the switch to become disabled. Default is "OFF". This value can also be a [template](/topics/templating/).
- **is_on_template** (*Optional*): A [template](/docs/configuration/templating/#processing-incoming-data) that determines the state of the switch from the value returned by the GET request on the resource URL. This template should compute to a boolean (True or False). If the value is valid JSON, it will be available in the template as the variable `value_json`. Default is equivalent to `'{% raw %}{{ value_json == body_on }}{% endraw %}'`. This means that by default, the state of the switch is on if and only if the response to the GET request matches .

<p class='note warning'>
Make sure that the URL matches exactly your endpoint or resource.
</p>

## {% linkable_title Example %}

### {% linkable_title Switch with templated value %}

This example shows a switch that uses a [template](/topics/templating/) to allow Home Assistant to determine its state. In this example the REST endpoint returns this JSON response with true indicating the switch is on.

```json
{"is_active": "true"}
```


```yaml
switch:
  - platform: rest
    resource: http://IP_ADDRESS/led_endpoint
    body_on: '{"active": "true"}'
    body_off: '{"active": "false"}'
    is_on_template: '{% raw %}{{value_json.is_active}}{% endraw %}'
```

`body_on` and `body_off` can also depend on the state of the system. For example, to enable a remote temperature sensor tracking on a radio thermostat, one has to send the current value of the remote temperature sensor. This can be achieved by using the template `{% raw %}'{"rem_temp":{{states.sensor.bedroom_temp.state}}}'{% endraw %}`.

