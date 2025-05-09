# Работа с {{ iot-full-name }} с устройства Android на языке Java

В этом руководстве вы узнаете как подключиться к {{ iot-full-name }} с устройства Android с помощью библиотеки [Paho](https://www.eclipse.org/paho/) и языка программирования Java. Данное руководство предполагает, что вы имеете навыки разработки для операционной системы Android на языке Java в Android Studio.

{% note info %}

Исходный код руководства доступен в репозитории на [GitHub](https://github.com/yandex-cloud-examples/yc-iot-android). 

{% endnote %}

После подключения вы сможете обмениваться сообщениями между устройством и реестром:

* [Отправлять сообщения](../../iot-core/operations/publish.md).
* [Подписывать устройство или реестр на получение сообщений](../../iot-core/operations/subscribe.md).

Чтобы подключиться к {{ iot-full-name }} и начать обмен сообщениями:

* [Создайте необходимые ресурсы {{ iot-full-name }}](#resources):
   * [Создайте реестр и добавьте ему сертификат](#registry).
   * [Создайте устройство и добавьте ему сертификат](#device).
* [Подключитесь к {{ iot-full-name }}](#connect).
* [Аутентифицируйтесь в {{ iot-full-name }}](#auth):
   * [Аутентификация с помощью X.509-сертификатов](#certs).
   * [Аутентификация по логину и паролю](#log-pass).
* [Установите соединение](#establish-connection).
* [Подпишитесь на топик и принимайте сообщения с данными](#subscribe).
* [Отправьте сообщение с данными](#publish).
* [Завершите соединение](#disconnect).

## Перед началом работы {#before-you-begin}

1. Если у вас еще нет интерфейса командной строки {{ yandex-cloud }}, [установите и инициализируйте его](../../cli/quickstart.md#install).
1. Скачайте и установите [Android Studio](https://developer.android.com/studio) — среду разработки для операционной системы Android.

## Создайте необходимые ресурсы {{ iot-full-name }} {#resources}

### Создайте реестр и добавьте ему сертификат {#registry}

Если у вас уже есть сертификат, перейдите сразу ко второму шагу.

1. Создайте сертификат для реестра (пропустите этот шаг, если у вас уже есть сертификат реестра):

   ```bash
   openssl req -x509 \
     -newkey rsa:4096 \
     -keyout registry-key.pem \
     -out registry-cert.pem \
     -nodes \
     -days 365 \
     -subj '/CN=localhost'
   ```

1. Создайте реестр:

   ```bash
   yc iot registry create --name my-registry
   ```

1. Добавьте сертификат реестру:

   ```bash
   yc iot registry certificate add \
   --registry-name my-registry \ # Имя реестра.
     --certificate-file registry-cert.pem # Путь к публичной части сертификата.
   ```

### Создайте устройство и добавьте ему сертификат {#device}

Если у вас уже есть сертификат, перейдите сразу ко второму шагу.

1. (опционально) Создайте сертификат для устройства:

   ```bash
   openssl req -x509 \
     -newkey rsa:4096 \
     -keyout device-key.pem \
     -out device-cert.pem \
     -nodes \
     -days 365 \
     -subj '/CN=localhost'
   ```

1. Создайте устройство:

   ```bash
   yc iot device create --name my-device
   ```

1. Добавьте сертификат устройству:

   ```bash
   yc iot device certificate add \
     --device-name my-device \ # Имя устройства.
     --certificate-file device-cert.pem # Путь к публичной части сертификата.
   ```

## Подключитесь к {{ iot-full-name }} {#connect}

Перед подключением настройте параметры соединения с помощью следующего кода:

```java
String clientId = "YandexIoTCoreAndroidTextClient";
int connectionTimeout = 60;
int keepAliveInterval = 60;
MqttAndroidClient mqttAndroidClient = new MqttAndroidClient(getApplicationContext(),"ssl://mqtt.cloud.yandex.net:8883", clientId);

// Настройка параметров соединения.
MqttConnectOptions options = new MqttConnectOptions();
options.setKeepAliveInterval(keepAliveInterval);
```

Где:
* `MqttAndroidClient` — класс, в котором указываются параметры подключения к {{ iot-full-name }}. Адрес, порт и идентификатор клиента.
* `MqttConnectOptions` — класс для установки параметров соединения. Вы можете оставить настройки по умолчанию, но рекомендуется задать параметр `KeepAliveInterval`. От его значения зависит частота отправки команд `PINGREQ`. Чем меньше данный параметр, тем быстрее клиент понимает, что соединение было разорвано нештатным путем. Но для этого чаще отправляются тарифицируемые команды `PINGREQ`.

## Аутентифицируйтесь в {{ iot-full-name }} {#auth}

Есть два способа [аутентификации](../../iot-core/concepts/authorization.md):
* [С помощью X.509-сертификатов](#certs).
* [По логину и паролю](#log-pass).

### Аутентификация с помощью X.509-сертификатов {#certs}

Для такого вида аутентификации удобнее всего использовать сертификаты [PKCS#12](https://ru.wikipedia.org/wiki/PKCS12) в PFX-формате. Сгенерировать сертификат в PKCS#12-формате из PEM-сертификатов можно с помощью команды:

```bash
openssl pkcs12 -export -in cert.pem -inkey key.pem -out keystore.p12
```

Для загрузки сертификатов в проекте используйте метод:

```java
private SSLSocketFactory getSocketFactory(final InputStream caCrtFile, final InputStream devCert, final String password)
```

Загрузка сертификатов происходит в несколько этапов:

1. Загрузите сертификат, используемый для аутентификации сервера:

    ```java
    // Загрузка сертификата удостоверяющего центра.
    CertificateFactory cf = CertificateFactory.getInstance("X.509");
    X509Certificate caCert = (X509Certificate) cf.generateCertificate(caCrtFile);

    // Использование сертификата удостоверяющего центра для аутентификации сервера.
    KeyStore serverCaKeyStore = KeyStore.getInstance(KeyStore.getDefaultType());
    serverCaKeyStore.load(null, null);
    serverCaKeyStore.setCertificateEntry("ca", caCert);
    TrustManagerFactory tmf = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
    tmf.init(serverCaKeyStore);
    ```

1. Загрузите сертификат клиента, используемый для аутентификации на сервере:

    ```java
    KeyStore clientKeystore = KeyStore.getInstance("PKCS12");
    clientKeystore.load(devCert, password.toCharArray());
    ```

В результате метод возвращает `AdditionalKeyStoresSSLSocketFactory`:

```java
return new AdditionalKeyStoresSSLSocketFactory(clientKeystore, serverCaKeyStore);
```

Класс `AdditionalKeyStoresSSLSocketFactory` является наследником `SSLSocketFactory` и используется для работы с самоподписанными сертификатами. На последнем этапе полученный выше `sslSocketFactory` нужно передать в параметры соединения:

```java
options.setSocketFactory(sslSocketFactory);
```

### Аутентификация по логину и паролю {#log-pass}

Так как {{ iot-full-name }} требует TLS-протокол при аутентификации с помощью логина и пароля, инициализируйте класс `AdditionalKeyStoresSSLSocketFactory` с помощью метода:

```java
private SSLSocketFactory getSocketFactory(final InputStream caCrtFile, final InputStream devCert, final String password)
```

В качестве `devCert` передайте значение `null`. В этом случае будет загружен только сертификат удостоверяющего центра сервера:

```java
// Загрузка сертификата удостоверяющего центра.
CertificateFactory cf = CertificateFactory.getInstance("X.509");
        X509Certificate caCert = (X509Certificate) cf.generateCertificate(caCrtFile);

// Использование сертификата удостоверяющего центра для аутентификации сервера.
KeyStore serverCaKeyStore = KeyStore.getInstance(KeyStore.getDefaultType());
serverCaKeyStore.load(null, null);
serverCaKeyStore.setCertificateEntry("ca", caCert);
TrustManagerFactory tmf = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
tmf.init(serverCaKeyStore);

return new AdditionalKeyStoresSSLSocketFactory(null, serverCaKeyStore);
```

В настройках соединения укажите логин (идентификатор реестра или устройства) и пароль:

```java
options.setUserName(mqttUserName);
options.setPassword(mqttPassword.toCharArray());
```

и `SSLSocketFactory`, полученный из кода выше:

```java
options.setSocketFactory(sslSocketFactory);
```

## Установите соединение {#establish-connection}

Установите соединение с {{ iot-full-name }} с помощью следующего кода:

```java
mqttAndroidClient.connect(options,null, new IMqttActionListener() {
   @Override
   public void onSuccess(IMqttToken asyncActionToken) {
   }

   @Override
   public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
   }
});
```

## Подпишитесь на топик и принимайте сообщения с данными {#subscribe}

Для обработки полученных данных используйте функцию обратного вызова:

```java
mqttAndroidClient.setCallback(new MqttCallback() {
   @Override
   public void messageArrived(String topic, MqttMessage message) throws Exception {   }
});
```

Подпишитесь на топик с помощью следующего кода. В методе `subscribe` нужно указать топик, на который вы хотите подписаться, и [уровень качества обслуживания](../../glossary/qos.md).

```java
IMqttToken subToken = mqttAndroidClient.subscribe(topic, qos);
subToken.setActionCallback(new IMqttActionListener() {
   @Override
   public void onSuccess(IMqttToken asyncActionToken) {
       // Публикация сообщения.
   }

   @Override
   public void onFailure(IMqttToken asyncActionToken,
                         Throwable exception) {
       // Подписка на сообщение не может быть выполнена. Возможно, пользователь не был
       // авторизован для подписки на указанный топик.
       System.out.println("Failed to subscribe");
   }
});
```

## Отправьте сообщение с данными {#publish}

Отправьте сообщения с помощью следующего кода. В методе `publish` нужно указать топик, в который вы хотите отправить сообщение, и текст сообщения. Опционально вы можете указать желаемый уровень качества обслуживания для экземпляра класса `MqttMessage`.

```java
IMqttDeliveryToken publish = mqttAndroidClient.publish("<топик>", new MqttMessage("Your message text.".getBytes()));
```

Обрабатывайте события об обрыве связи и отслеживайте доставку сообщений с помощью функций обратного вызова:

```java
mqttAndroidClient.setCallback(new MqttCallback() {
   @Override
   public void connectionLost(Throwable cause) {

   }

   @Override
   public void deliveryComplete(IMqttDeliveryToken token) {

   }
});
```

## Завершите соединение {#disconnect}

Завершите соединение с {{ iot-full-name }} с помощью методов:

```java
mqttAndroidClient.disconnect();
mqttAndroidClient.close();
```

Где:
* Метод `disconnect` закрывает соединение с сервером.
* Метод `close` освобождает ресурсы класса `MqttAndroidClient`.
