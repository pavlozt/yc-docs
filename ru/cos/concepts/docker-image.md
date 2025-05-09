# Docker-образ

При создании виртуальной машины на базе {{ coi }} есть возможность указать [Docker-образ](../../container-registry/concepts/docker-image.md), на основе которого будет запущен [Docker-контейнер](/blog/posts/2022/03/docker-containers).

Этот Docker-образ может быть загружен как из публичного реестра Docker-образов, так и из {{ container-registry-full-name }}. В случае использования {{ container-registry-name }} для доступа к Docker-образу будет использован привязанный к ВМ [сервисный аккаунт](../../iam/concepts/index.md#sa). Образ {{ coi }} в реестре {{ container-registry-name }} может обновляться и меняться в соответствии с [релизами](../release-notes.md). При этом образ на ВМ не обновится автоматически до последней версии.

Пример имени Docker-образа для разных реестров:
* Docker Hub: `ubuntu:16.04`.
* {{ container-registry-name }}: `{{ registry }}/<идентификатор_реестра>/ubuntu:16.04`.

## Примеры использования {#examples}

* [{#T}](../tutorials/coi-fluent-bit-logging.md)
* [{#T}](../tutorials/deploy-app-container.md)
* [{#T}](../tutorials/docker-compose.md)
* [{#T}](../tutorials/serial-port.md)
