Все цены указаны с НДС. Цены за месяц указаны из расчета для месяца в 30 календарных дней. Для более коротких месяцев цена соответственно выше, для более длинных — ниже.

Операции с данными                                                  | Цена,<br>вкл. НДС
------------------------------------------------                    | ----------------------
Фактическое потребление Request Units, менее 1 млн в месяц          | {{ sku|KZT|ydb.v1.serverless.requests|string }}
Фактическое потребление Request Units, свыше 1 млн в месяц          | {{ sku|KZT|ydb.v1.serverless.requests|pricingRate.1|string }} за 1 миллион RU
Выделенная пропускная способность                                   | 8,00 ₸ за 100 RU/с×час

Другие тарифицируемые операции                                      | Цена,<br>вкл. НДС
------------------------------------------------                    | ----------------------
Хранение данных, менее 1 ГБ в месяц                                 | {{ sku|KZT|ydb.v1.serverless.storage|month|string }}
Хранение данных, свыше 1 ГБ в месяц                                 | {{ sku|KZT|ydb.cluster.v1.ssd|month|string }} за 1 ГБ в месяц
Хранение резервных копий по требованию в {{ objstorage-full-name }} | 10,05 ₸ за 1 ГБ в месяц
