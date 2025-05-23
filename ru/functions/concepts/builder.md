# Сборщик

_Сборщик_ — это механизм, который используется для сборки функции во время создания новой версии. Он занимается загрузкой необходимых зависимостей и, в случае использования компилируемого языка программирования, компиляцией исходных файлов в исполняемый.

Добавить [динамические библиотеки](./runtime/environment-variables.md#dynamic-library) можно, загрузив их в ZIP-архиве.

### Журналирование {#logging}

Выходные данные сборщика как в случае успешного создания версии, так и в случае ошибки записываются:
* в [лог-группу по умолчанию](../../logging/concepts/log-group.md), где каждая запись содержит параметр `json_payload` со значением `source=builder`;
* в файл, ссылку на который можно найти в разделе операций.

Если сборщик сигнализирует об ошибке сборки проекта, [среда выполнения](runtime/index.md) автоматически перехватывает ошибку и записывает подробную информацию о типе, тексте и месте ее возникновения. Версия функции при этом не создается.
