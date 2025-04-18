---
title: Запрос прав доступа
description: Вы можете запросить права доступа к папке или объекту, к которому у вас нет доступа. Также вы можете запросить изменение прав доступа к папке или объекту, к которому у вас есть доступ на просмотр.
---

# Запрос прав доступа

{% note info %}

Посмотреть права доступа для дашборда, в котором настроено [сообщение при ошибке доступа](../../dashboard/settings.md#message-settings), могут только пользователи с правами [{{ permission-write }}](../../security/manage-access.md#permission-write) или [{{ permission-admin }}](../../security/manage-access.md#permission-admin) на этот дашборд.

Пользователи с правом [{{ permission-read }}](../../security/manage-access.md#permission-read) не могут просмотреть список прав доступа на этот дашборд или запросить изменение доступа к нему через интерфейс {{ datalens-short-name }}.

{% endnote %}

Вы можете запросить права доступа к папке или объекту, к которому у вас нет доступа.
Также вы можете запросить изменение прав доступа к папке или объекту, к которому у вас есть доступ на просмотр:

{% list tabs %}

- Запрос прав доступа

  1. На [странице навигации]({{ link-datalens-main }}/navigation) найдите папку или объект и нажмите на него.

     {% include [nav-filter-note](../../../_includes/datalens/datalens-nav-filter-note.md) %}

  1. Введите имя пользователя, выберите права доступа и нажмите кнопку **Запросить**.

- Изменение прав доступа

  1. На [странице навигации]({{ link-datalens-main }}/navigation) найдите папку или объект.

     {% include [nav-filter-note](../../../_includes/datalens/datalens-nav-filter-note.md) %}

  1. Нажмите значок ![image](../../../_assets/console-icons/ellipsis.svg) у объекта или папки и выберите **Права доступа**.
  1. В появившемся окне нажмите кнопку **Права на <тип_объекта>**, где `<тип_объекта>` — подключение, датасет, чарт или дашборд.
  1. Выберите права доступа и нажмите кнопку **Запросить**.

{% endlist %}

{% note info %}


Запрос хранится год. После чего срабатывает автоматический отказ, который записывается в историю изменений.


{% endnote %}
