---
title: Getting started with SMS in {{ cns-full-name }}
description: With {{ cns-name }}, you can send text messages (SMS) using SMS notification channels.
---

# Getting started with SMS in {{ cns-full-name }}

{% include [preview-stage](../_includes/notifications/preview-stage.md) %}

{% include [ask-for-turning-on](../_includes/notifications/ask-for-turning-on.md) %}

{% include [about-service](../_includes/notifications/about-service.md) %}

{% include [sms-short-description](../_includes/notifications/sms-short-description.md) %}


To get started with SMS:
1. [Get your cloud ready](#before-you-begin).
1. [Create an SMS notification channel with a common sender](#create-common-channel).
1. [Add a test phone number](#add-test-number).
1. [Send a test SMS](#send-test-sms).
1. [Create a notification channel with an individual sender](#create-individual-channel).
1. [Exit the sandbox](#quit-from-sandbox).

## Get your cloud ready {#before-you-begin}

{% include [before-you-begin](../_tutorials/_tutorials_includes/before-you-begin.md) %}

## Create an SMS notification channel with a common sender {#create-common-channel}

In {{ cns-name }}, messages to end users are sent through [notification channels](./concepts/index.md#channels).

{% include [common-sender-description](../_includes/notifications/common-sender-description.md) %}

To create a channel with a common sender:

{% include [sms-channel-common-create](../_includes/notifications/sms-channel-common-create.md) %}


## Add a test phone number {#add-test-number}

{% include [sandbox-test-numbers](../_includes/notifications/sandbox-test-numbers.md) %}

To add a test phone number:

{% list tabs group=instructions %}

- Management console {#console}

  1. Select the notification channel you previously created.
  1. Navigate to the ![image](../_assets/console-icons/handset-arrow-in.svg) **{{ ui-key.yacloud.cns.label_phone-numbers }}** tab.
  1. Click **Add a test phone number**.
  1. In the window that opens, enter a phone number and click **Get code**. An SMS with a confirmation code will be sent to the specified phone number.

      Russian phone numbers in [E.164](https://en.wikipedia.org/wiki/E.164) format are supported, e.g., `+79991112233`.

  1. Enter the code from the SMS and click **{{ ui-key.yacloud.common.confirm }}**.

{% endlist %}

## Send a test SMS {#send-test-sms}

{% list tabs group=instructions %}

- Management console {#console}

  1. Next to the test phone number you previously added, click ![image](../_assets/console-icons/ellipsis.svg) and select ![image](../_assets/console-icons/comment.svg) **{{ ui-key.yacloud.cns.action_send-msg }}**.
  1. In the window that opens, enter the message text and click **{{ ui-key.yacloud.cns.action_send-msg }}**.

{% endlist %}

## Create a notification channel with an individual sender {#create-individual-channel}

After practicing on a channel with a common sender, you can register an SMS notification channel with an individual sender.

{% include [individual-sender-description](../_includes/notifications/individual-sender-description.md) %}

To create a channel with an individual sender:

{% include [sms-channel-individual-create](../_includes/notifications/sms-channel-individual-create.md) %}


## Exit the sandbox {#quit-from-sandbox}

{% include [individual-in-sandbox](../_includes/notifications/individual-in-sandbox.md) %}

After practicing on test phone numbers, you can apply to exit the sandbox mode:

{% list tabs group=instructions %}

- Management console {#console}

  1. Select the individual sender notification channel you created earlier.
  1. Click **{{ ui-key.yacloud.cns.action_sms-leave-sandbox }}**.

      With that done, a tech support ticket to exit the sandbox will be automatically generated.

      {% include [sms-quota-increase](../_includes/notifications/sms-quota-increase.md) %}

{% endlist %}

Once the ticket is approved, you will be able to send SMS messages to any Russian phone numbers in [E.164](https://en.wikipedia.org/wiki/E.164) format, e.g., `+79991112233`.

## See also {#see-also}

* [Getting started with push notifications](quickstart-sms.md)
* [Getting started with the service via the AWS CLI](./tools/aws-cli.md)
* [Service overview](./concepts/index.md)
* [SMS notification channel](./concepts/sms.md)
