# Удалить словарь

В {{ speechsense-name }} можно удалить [словари](../../concepts/dictionaries.md) пространства или проекта, но нельзя удалить системные словари.

Для удаления словаря нужна роль `{{ roles-speechsense-admin }}` или `{{ roles-speechsense-editor }}` в [пространстве](../../concepts/resources-hierarchy.md#space) или [проекте](../../concepts/resources-hierarchy.md#project) — зависит от того, где находится словарь.

Чтобы удалить словарь:

1. Откройте [главную страницу]({{ link-speechsense-main }}) {{ speechsense-name }}.
1. Перейдите в нужное пространство, затем выберите проект.

   Если вы хотите удалить словарь проекта, выберите нужный проект.

1. Перейдите на вкладку **{{ ui-key.yc-ui-talkanalytics.dictionaries.dictionaries }}**.
1. В строке нужного словаря нажмите ![icon](../../../_assets/console-icons/ellipsis.svg), затем ![icon](../../../_assets/console-icons/trash-bin.svg) **{{ ui-key.yc-ui-talkanalytics.common.delete-key-value }}**.
1. Подтвердите удаление словаря.

Если вы не убирали словарь из тега и затем удалили словарь, он продолжит отображаться на странице тега с пометкой, что был удален. Тег со словарем останется на уже загруженных диалогах, но не будет применяться к новым диалогам.
