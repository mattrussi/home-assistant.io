---
layout: page
title: "OpenGarage Cover"
description: "Instructions on how to integrate OpenGarage.io covers within Home Assistant."
date: 2017-04-07 14:25
sidebar: true
comments: false
sharing: true
footer: true
logo: opengarage.png
ha_category: DIY
ha_release: 0.44
---

The `opengarage` cover platform lets you control the open-source [OpenGarage.io](https://opengarage.io/) device through Home Assistant.

## {% linkable_title Configuration %}

To enable OpenGarage Covers in your installation, add the following to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
cover:
  platform: opengarage
  covers:
      garage:
        host:  192.168.1.12
        device_key: opendoor
        name:  Left Garage Door
      garage2:
        host:  192.168.1.13
        device_key: opendoor
        name:  Right Garage Door
```

{% configuration %}
covers:
  description: List of your doors.
  required: true
  type: map
  keys:
    identifier:
      description: Name of the cover as slug. Multiple entries are possible.
      required: true
      type: map
      keys:
        host:
          description: IP address of device.
          required: true
          type: string
        port:
          description: HTTP Port.
          required: false
          default: 80
          type: integer
        device_key:
          description: Access key to control device.
          required: true
          default: opendoor
          type: string
        name:
          description: Name to use in the Frontend. If not provided, it will use name configured in device.
          required: false
          type: string
{% endconfiguration %}

**Example with more detail:**
<p class='img'>
  <img src='{{site_root}}/images/components/opengarage/cover_opengarage_details.jpg' />
</p>

```yaml
# Related configuration.yaml entry
cover:
  platform: opengarage
  covers:
      garage:
        host: 192.168.1.12
        device_key: opendoor
        name: honda

sensor:
  platform: template
  sensors:
    garage_status:
      friendly_name: 'Honda Door Status'
      value_template: {% raw %}'{% if states.cover.honda %}
          {% if states.cover.honda.attributes["door_state"] == "open" %}
            Open
          {% elif states.cover.honda.attributes["door_state"] == "closed" %}
            Closed
          {% elif states.cover.honda.attributes["door_state"] == "opening" %}
            Opening
          {% elif states.cover.honda.attributes["door_state"] == "closing" %}
            Closing
          {% else %}
            Unknown
          {% endif %}
          {% else %}
          n/a
          {% endif %}'{% endraw %}
    garage_car_present:
      friendly_name: 'Honda in Garage'
      value_template: {% raw %}'{% if states.cover.honda %}
          {% if states.cover.honda.state == "open" %}
            n/a
          {% elif ((states.cover.honda.attributes["distance_sensor"] > 40) and (states.cover.honda.attributes["distance_sensor"] < 100)) %}
            Yes
          {% else %}
            No
          {% endif %}
          {% else %}
          n/a
          {% endif %}'

group:
  garage:
    name: Garage
    entities:
      - cover.honda
      - sensor.garage_status
      - sensor.garage_car_present

customize:
  cover.honda:
    friendly_name: Honda
    entity_picture: /local/honda.gif
  sensor.garage_car_present:
    icon: mdi:car
```
{% endraw %}
