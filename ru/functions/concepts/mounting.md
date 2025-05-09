---
title: Монтирование файловых систем в функцию {{ sf-full-name }}
description: Из статьи вы узнаете, как монтировать файловые системы в функцию {{ sf-name }}.
---

# Монтирование внешних ресурсов на файловую систему функции

Вы можете смонтировать [бакеты](../../storage/concepts/bucket.md) и один эфемерный диск в функцию {{ sf-full-name }}. Чтобы не случались ошибки обращения к данным, все точки монтирования для эфемерных дисков и бакетов должны быть уникальными.

{% endnote %}

## Монтирование бакетов {#mount-backet}

Монтирование бакетов позволяет обращаться к бакетам через интерфейс файловой системы. В настройках [версии функции](function.md#version) пользователь может указать точку монтирования или несколько. Директория, к которой смонтируется бакет, будет доступна по пути `/function/storage/<точка_монтирования>`.

Смонтировать можно весь бакет или [папку](../../storage/concepts/object.md#folder).


{% include [roles-for-bucket-mounting](../../_includes/functions/roles-for-bucket-mounting.md) %}

## Монтирование эфемерного диска {#mount-ephemeral-storage}

{% include [preview-and-request](../../_includes/note-preview-by-request.md) %}

Для монтирования эфемерного диска пользователь может указать точку монтирования. Директория, к которой смонтируется эфемерный диск, будет доступна по пути `/function/storage/<точка_монтирования>`.

Размер эфемерного диска кратен 1 ГБ и устанавливается в диапазоне от 1 до 100 ГБ. Максимальный размер эфемерного диска на стадии [Preview](../../overview/concepts/launch-stages.md) не может превышать 100 ГБ.

Эфемерный диск заданного размера создается при вызове функции непосредственно перед выполнением пользовательского кода. Он форматируется в файловую систему [ext4](https://ru.wikipedia.org/wiki/Ext4) и монтируется по указанной точке монтирования.

В период всего времени жизни функции пользовательскому коду предоставляется доступ к эфемерному диску в режиме чтения-записи (Read-Write).

Если функция вызывается несколько раз, существует вероятность, что ее повторные вызовы будут обрабатываться одним и тем же экземпляром. В таком случае данные, оставшиеся на диске после предыдущего вызова, могут быть доступны. Такое поведение функции не детерминировано, и нельзя полагаться на то, что данные сохраняются между вызовами.

{% note info %}

Когда жизненный цикл функции завершается, эфемерный диск удаляется без возможности восстановления. Используйте этот вид дискового пространства только для сохранения временного состояния.

{% endnote %}


## См. также {#see-also}

* [Cмонтировать бакеты в функцию](../operations/function/mount-bucket.md)
* [Cмонтировать эфемерный диск в функцию](../operations/function/mount-ephemeral-disk.md)
* [Смонтировать бакеты в контейнер](../../serverless-containers/operations/mount-bucket.md)
* [Смонтировать эфемерный диск в контейнер](../../serverless-containers/operations/mount-ephemeral-disk.md)
