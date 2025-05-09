{% include [registry-and-device-topic-note](../../_includes/iot-core/registry-and-device-topic-note.md) %}

{% include [iot-before-you-begin-with-mosquitto](../../_includes/iot-core/iot-before-you-begin-with-mosquitto.md) %}

## Connecting to an MQTT server {#connect-mqtt-server}

{% include [connect-mqtt-broker](../../_includes/iot-core/connect-mqtt-broker.md) %}

## Sending a message with data {#pub-events}

Send a message with data using the following parameters:
- `-h`: MQTT server address.
- `-p`: MQTT server port.
- `--cafile`: Path to the certificate from the certificate authority.
- `--cert`: Path to the public part of the certificate.
- `--key`: Path to the private part of the device certificate.
- `-t`: Device topic.
- `-m`: Message text.
- `-q`: [Quality of service (QoS) level](../../iot-core/concepts/index.md#qos).

{% include [debug-note](../../_includes/iot-core/debug-note.md) %}

{% list tabs group=instructions %}

- Mosquitto {#mosquitto}

    - Send data to the device topic:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile rootCA.crt \
          --cert device-cert.pem \
          --key device-key.pem \
          -t '$devices/<device_ID>/events' \
          -m 'Test data' \
          -q 1
        ```

    - Send data to the permanent device topic:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile rootCA.crt \
          --cert device-cert.pem \
          --key device-key.pem \
          -t '$devices/<device_ID>/state' \
          -m 'Test data' \
          -q 1
        ```

        A registry subscribed to this topic will know which device sent the data because the topic contains a unique device ID.

  - Send data to the registry topic:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile rootCA.crt \
          --cert device-cert.pem \
          --key device-key.pem \
          -t '$registries/<registry_ID>/events' \
          -m 'Test data' \
          -q 1
        ```

    - Send data to the permanent registry topic:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile rootCA.crt \
          --cert device-cert.pem \
          --key device-key.pem \
          -t '$registries/<registry_ID>/state' \
          -m 'Test data' \
          -q 1
        ```

        A registry subscribed to this topic will not know which device sent the data because the topic does not contain a unique device ID.

{% endlist %}

## Sending messages with commands {#pub-commands}

A registry can send messages with commands to one, multiple, or all devices added to it. Let's look at all the options.

Send a message with a command using the following parameters:
- `-h`: MQTT server address.
- `-p`: MQTT server port.
- `--cafile`: Path to the certificate from the certificate authority.
- `--cert`: Path to the public part of the registry.
- `--key`: Path to the private part of the registry certificate.
- `-t`: Device topic.
- `-m`: Message text.
- `-q`: [Quality of service (QoS) level](../../iot-core/concepts/index.md#qos).

{% include [debug-note](../../_includes/iot-core/debug-note.md) %}

{% list tabs group=instructions %}

- Mosquitto {#mosquitto}

    - Send a command to a single device:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile rootCA.crt \
          --cert registry-cert.pem \
          --key registry-key.pem \
          -t '$devices/<device_ID>/commands' \
          -m 'Test command for first device' \
          -q 1
        ```

    - Send a command to a single device using the permanent topic:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile rootCA.crt \
          --cert registry-cert.pem \
          --key registry-key.pem \
          -t '$devices/<device_ID>/config' \
          -m 'Test command for first device via permanent topic' \
          -q 1
        ```

    - Send a command to two devices:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile cert.pem \
          --cert registry-cert.pem \
          --key registry-key.pem \
          -t '$devices/<device_1_ID>/commands' \
          -t '$devices/<device_2_ID>/commands' \
          -m 'Test command for first and second device' \
         -q 1 # Quality of service (QoS) level 1.
        ```

    - Send a command to two devices using the permanent topic:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile cert.pem \
          --cert registry-cert.pem \
          --key registry-key.pem \
          -t '$devices/<device_1_ID>/config' \
          -t '$devices/<device_2_ID>/config' \
          -m 'Test command for first and second devices via permanent topic' \
          -q 1 # Quality of service (QoS) level 1.
        ```

    - Send a command to all devices added to the registry:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile cert.pem \
          --cert registry-cert.pem \
          --key registry-key.pem \
          -t '$registries/<registry_ID>/commands' \
          -m 'Test command for all devices' \
          -q 1
        ```

    - Send a command to all devices added to the registry using the permanent topic:

        ```
        mosquitto_pub -h mqtt.cloud.yandex.net \
          -p 8883 \
          --cafile cert.pem \
          --cert registry-cert.pem \
          --key registry-key.pem \
          -t '$registries/<registry_ID>/config' \
          -m 'Test command for all devices via permanent topic' \
          -q 1
        ```

{% endlist %}