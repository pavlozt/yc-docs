# Эскалации

{% include [escalations-preview](../../../_includes/monitoring/escalations-preview.md) %}


<iframe width="640" height="360" src="https://runtime.strm.yandex.ru/player/video/vplvwouze3np3m4t5rkb?autoplay=0&mute=0" allow="autoplay; fullscreen; picture-in-picture; encrypted-media" frameborder="0" scrolling="no"></iframe>

[Смотреть видео на YouTube](https://www.youtube.com/watch?v=iAGfn-I0CtY).



{% include [escalations-definition](../../../_includes/monitoring/escalation-definition.md) %}

Политика эскалации добавляется как [способ уведомления](notification-channel.md#channel-parameters) к алерту. Одну политику эскалаций можно добавить к любому количеству алертов.

Отличие эскалации от других способов уведомления:

* эскалацию можно запустить только для алертов в статусе **Alarm**;
* система будет повторять последовательность уведомлений, пока не будет остановлена.

## Основные понятия {#intro}

* **Политика эскалации** — последовательность шагов уведомлений, задержка между ними и дополнительные настройки.

* **Эскалация** — экземпляр политики эскалаций, запущенный при срабатывании алерта.

* **План эскалации** — планируемые (будущие) уведомления в рамках запущенной эскалации. План эскалации строится перед запуском очередной итерации. При редактировании уже запущенной эскалации изменения будут учтены в следующей итерации.

* **Шаг эскалации** — очередное уведомление, которое необходимо отправить получателям.

* **Итерация эскалации** — разовое прохождение шагов эскалации от первого до последнего. Максимальное количество итераций одной эскалации — 10, после чего эскалация автоматически останавливается.

* **История эскалации** — список уведомлений, которые уже были отправлены в этой эскалации. Включает информацию об уведомлениях из всех итераций и всех шагов внутри каждой итерации.

## Настройка эскалации {#escalation-config}

### Шаги эскалации {#steps}

Настройки шага эскалации включают метод уведомления (транспорт) и список получателей или параметры интеграции с внешним сервисом, а также период задержки перед переходом к следующему шагу в случае успешной отправки уведомления. Количество шагов в эскалации не ограничено.

Шаг эскалации считается успешным, если от API транспорта получен ответ течение установленного периода времени. Для звонка шаг считается успешным, когда пользователь ответил на звонок и нажал кнопку `5` в тоновом режиме.

{% note info %}

Подтверждение телефонного звонка нажатием на кнопку помогает исключить ситуации, когда звонок был принят по ошибке. Например, при работе автоответчика, ошибке оператора или случайном ответе, когда телефон находился в кармане.

Если на каком-либо шаге указано несколько получателей, успешная доставка хотя бы одному из них означает успешность всего шага.

{% endnote %}

Сервис совершает не более двух попыток доставки уведомления на каждом шаге. Время ожидания ответа — 5 минут для телефонного звонка и 2 минуты для остальных способов уведомления. Если обе попытки неуспешны, шаг отмечается как неуспешный и эскалация переходит к следующему шагу.

Неуспешная доставка уведомления означает, что произошла ошибка при его отправке (ошибка транспорта). Если это был звонок, то получатель либо не ответил, либо не подтвердил уведомление – не нажал кнопку `5` в тоновом режиме.

### Задержки при отправке уведомлений {#sending-notifications-delay}

* **Начальная задержка** — задержка первой итерации при запуске эскалации.

  Механизм планирования уведомлений учитывает все запущенные эскалации и их получателей. Система старается группировать уведомления по способу доставки и отправлять их получателю только один раз. Например, если один получатель участвует в нескольких параллельных эскалациях и по каждой из них должен получить звонок, то он получит один звонок с уведомлением обо всех запущенных эскалациях.
 
  Начальная задержка помогает отслеживать и группировать параллельные запуски эскалаций.

* **Задержка** — задержка перед переходом к следующему шагу эскалации в случае успешной доставки уведомления.

    {% note warning %}

    Для телефонных звонков будет применяться минимальная задержка в 1 минуту, независимо от настроек шага эскалации и начальной задержки. Это помогает объединять звонки в параллельных эскалациях.
    
    {% endnote %}

    Если шаг был отмечен как неуспешный, то переход к следующему шагу будет без задержки. Если текущий шаг – последний в итерации, то задержка перед переходом к следующей итерации сработает независимо от успешности шага.


### Остановка эскалации {#escalation-stop}

Эскалация может быть остановлена по следующим причинам:

* получатель ответил на звонок и **нажал 4 в тоновом режиме**;
* алерт, запустивший эскалацию, больше не находится в статусе **Alarm**;
* пользователь остановил эскалацию через консоль.

### Методы уведомления {#notification-methods}

{% include [escalation-channels](../../../_includes/monitoring/escalation-channels.md) %}

## Мои эскалации {#my-escalations-popup}

При запуске эскалации, в которой текущий пользователь указан получателем уведомлений, в интерфейсе {{ monitoring-name }} появляется окно с информацией о запущенной эскалации. Это окно отображается на всех страницах {{ monitoring-name }}. Если окно закрыть, оно откроется при запуске новой эскалации.

{% note info %}

Окно появится только при новом запуске эскалации. При начале очередной итерации запущенной эскалаций окно не появится.

{% endnote %}

В окне с эскалациями можно:

* перейти на страницу алерта, который запустил эскалацию;
* открыть страницу ленты уведомлений;
* остановить запущенную эскалацию или все запущенные эскалации сразу.
