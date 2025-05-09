# Getting started with {{ ml-platform-name }}

{{ ml-platform-full-name }} is an end-to-end ML development environment where you can use well-known IDEs, serverless computing technology, and seamlessly combine a broad range of {{ yandex-cloud }} computing resource configurations. {{ ml-platform-full-name }} is part of the data platform and provides powerful features to work with {{ yandex-cloud }} services. As an IDE, {{ ml-platform-name }} provides [{{ jlab }}® Notebook](https://jupyter.org/).

In this section, you will learn how to:
1. [Create a project](#create-project).
1. [Run projects](#start-project).
1. [Configure the environment](#install-dependencies).
1. [Upload data to projects](#download-data).
1. [Start training](#start-ml).

## Getting started {#before-you-begin}

1. Go to the [management console]({{ link-console-main }}) and log in to {{ yandex-cloud }} or sign up if not signed up yet.
1. Go to [{{ billing-name }}]({{ link-console-billing }}) and make sure you have a [billing account](../billing/concepts/billing-account.md) linked and its status is `ACTIVE` or `TRIAL_ACTIVE`. If you do not have a billing account yet, [create one](../billing/quickstart/index.md#create_billing_account).
1. Open the {{ ml-platform-name }} [home page]({{ link-datasphere-main }}).
1. Accept the user agreement.
1. Select the organization to work with {{ ml-platform-name }} in or create a new one.

## Create a project {#create-project}

{% include [create-project](../_includes/datasphere/ui-create-project.md) %}

## Run the project {#start-project}

{% include [start-project](../_includes/datasphere/ui-start-project.md) %}

## Set up your environment {#install-dependencies}

{% include [install-dependencies-intro](../_includes/datasphere/install-dependencies-intro.md) %}

{% include [install-dependencies-steps](../_includes/datasphere/install-dependencies-steps.md) %}

You can also configure the environment to run your code [using Docker images](operations/user-images.md).

## Upload data to the project {#download-data}

{% include [download-data](../_includes/datasphere/download-data.md) %}

## Start training {#start-ml}

{% include [start-ml](../_includes/datasphere/start-ml.md) %}

## What's next {#what-is-next}

* [Learn about service features](concepts/index.md).
* [See other service guides](operations/index.md).
* [Deploy the trained model as a service](concepts/deploy/index.md).
