---
title: Режим рассуждений в генеративных моделях {{ foundation-models-name }}
description: Из статьи вы узнаете об особенностях использования режима рассуждений для повышения точности ответов генеративных моделей {{ foundation-models-full-name }}.
---

# Режим рассуждений в генеративных моделях

Генеративные модели не всегда одинаково хорошо справляются с задачами, требующими рассуждений, то есть разбиения задачи на этапы и последовательного выполнения цепочки вычислений, при котором исходными данными для каждого последующего вычисления являются результаты предыдущего.

Точность ответов модели можно повысить, заставив модель рассуждать и выполнять генерацию с учетом таких цепочек промежуточных вычислений. Это можно сделать с помощью [промта](../../gpt-prompting-guide/techniques/CoT.md) или специального [параметра генерации](../../text-generation/api-ref/TextGeneration/completion.md#yandex.cloud.ai.foundation_models.v1.ReasoningOptions).

Задать настройки режима рассуждений с помощью параметра `reasoning_options` можно при [обращении](./models.md#addressing-models) через API или SDK к тем моделям, которые этот параметр поддерживают. Параметр `reasoning_options` может принимать следующие значения:
* `DISABLED` — режим рассуждений выключен. Значение по умолчанию. Если параметр `reasoning_options` не задан в запросе, режим рассуждений выключен.
* `ENABLED_HIDDEN` — режим рассуждений включен. Разные модели по-разному принимают решение, использовать ли этот режим для каждого конкретного запроса. Даже если при генерации ответа модель использует рассуждения, ответ не будет содержать непосредственно саму цепочку рассуждений модели.

Пример конфигурации запроса в режиме рассуждений:

{% list tabs group=programming_language %}

- SDK {#sdk}

  ```python
  model = sdk.models.completions('yandexgpt')
  modelRequest = model.configure(
          reasoning_mode='enabled_hidden',
      ).run("Текст запроса")
  ```

- API {#api}

  ```json
  {
    "modelUri": "gpt://<идентификатор_каталога>/yandexgpt",
    "completionOptions": {
      "stream": false,
      "temperature": 0.1,
      "maxTokens": "1000",
      "reasoningOptions": {
        "mode": "ENABLED_HIDDEN"
      }
    },
    "messages": [...]
  }
  ```

{% endlist %}

При использовании моделью режима рассуждений может увеличиться объем выполняемых вычислений и общее количество итоговых [токенов](./tokens.md) запроса: если рассуждения были использованы, ответ модели будет содержать поле `reasoningTokens` с ненулевым значением.

Режим рассуждений с помощью параметра `reasoning_options` доступен для модели {{ gpt-pro }}.

#### См. также {#see-also}

* [{#T}](../../gpt-prompting-guide/techniques/CoT.md)