# CLI. Импорт ключа, отправка токенов, делегирование

## Использование CLI

### 1. Для доступа к командам [Mina CLI](https://docs.minaprotocol.com/en/using-mina/cli-reference):

{% hint style="warning" %}
Этот шаг нужно выполнять только если вы запускали ноду с помощью Докера. Если же вы не использовали Докер, переходите сразу к шагу 2.
{% endhint %}

```text
sudo docker exec -it mina bash
```

Выход из CLI осуществляется командой:

```text
exit
```

### 2. Проверка статуса, состояния узла:

```text
mina client status
```

Выглядит это вот так:

![](../.gitbook/assets/image%20%283%29.png)

Дождитесь пока нода синхронизируется. В поле `Sync status:` должно быть написано `Synced`. Если в статусе написано `Catched`, значит нужно подождать чуть больше.  
После этого можно приступать к импорту ваших ключей.

### 3. Импортируем ключи

 Экспортируем приватный ключ:

```text
export KEYPATH=$HOME/keys/my-wallet
```

Импорт аккаунта с ключем производим следующей командой:

```text
mina accounts import -privkey-path $KEYPATH
```

Список ваших аккаунтов можно посмотреть командой ниже:

```text
mina accounts list
```

### 4. Разблокировка аккаунта:

Экспортируем Публичный ключ:

```text
export MINA_PUBLIC_KEY=$(cat $HOME/keys/my-wallet.pub)
```

Разблокируем аккаунт, чтобы можно было перемещать токены:

```text
mina accounts unlock -public-key $MINA_PUBLIC_KEY
```

В поле ввода пароля пишем ваш пароль от ключа и жмем ENTER.

### 5. Транзакции

Теперь можно отправлять токены.  
  
Вам нужно добавить:  
- отправителя `-sender`  
- получателя `-receiver`  
- комиссию транзакции `-fee`  
- количество отправляемых токенов `-amount`

```text
mina client send-payment \
-sender B62qnJHBeVJqWamtDhWDPwrX7Y5jiXKcMKTuvug9LQ8ictwNTWN7YvJ \
-receiver B62qqV16g8s744GHM6Dph1uhW4fggYwyvtDnVSoRUyYqNvTir3Rqqzx \
-fee 0.01 \
-amount 2
```

#### 5.1. Транзакция с `-memo` \(с подписью\)

Вам нужно добавить:  
- подпись`-memo`  
Ваш текст к транзакции.

```text
mina client send-payment \
-sender B62qnJHBeVJqWamtDhWDPwrX7Y5jiXKcMKTuvug9LQ8ictwNTWN7YvJ \
-receiver B62qqV16g8s744GHM6Dph1uhW4fggYwyvtDnVSoRUyYqNvTir3Rqqzx \
-fee 0.01 \
-amount 2 \
-memo "Ваш текст"
```

Готово. Токены отправлены.

### 6. Проверим баланс токенов

Баланс токенов проверяем командой ниже с вашим ID токенов:

```text
mina client get-balance \
-public-key $MINA_PUBLIC_KEY
```

Мы увидим баланс mina токенов.

### 7. Транзакция делегирования

Вам нужно добавить:  
- отправителя `-sender`  
- получателя `-receiver`  
- комиссию транзакции `-fee`

Флаг `-amount` не указывается, т.к. делегируется весь баланс адреса.

```text
mina client delegate-stake \
-sender B62qnJHBeVJqWamtDhWDPwrX7Y5jiXKcMKTuvug9LQ8ictwNTWN7YvJ \
-receiver B62qqV16g8s744GHM6Dph1uhW4fggYwyvtDnVSoRUyYqNvTir3Rqqzx \
-fee 0.01
```

## Другие команды \(В ДАННЫЙ МОМЕНТ НЕ ИСПОЛЬЗУЮТСЯ\)

{% hint style="warning" %}
Внимание!  
Следующие пункты с 1 по 4 в данный момент не используются. 
{% endhint %}

### 1. Создание токенов

Теперь создадим токены:

```text
mina client create-token \
-sender $MINA_PUBLIC_KEY
```

В ответ мы получим следующее:

{% code title="\#ПРИМЕР ОТВЕТА " %}
```text
Dispatched create new token command with ID 2cUDm3QoJ14znWj5LxN8hjwwuvtwi9FGXcy56i4dPvYizPNwr3V9c9ePYxy3pJKjgogNx28jwHhqupi6wHFgXBmU5iX27iK1zUvJarj6wJsUG8segWXc4LGPed66YbYk3u9HiWw4v8cYYEqcy1mU6hqfj5JPMPthEBifxUMHZTqCwZmYWSdiERxB6PtPEdXVraWaYPVU4Q8vtpSN7oSTK1AXyXLYYR835CBrNSmgbLvoBDNroCKwcQrzw4b76BFNLe6EuWvBcMgX6npeeAbPg8z8iJ4PKz3gA64o1Y72kCrqyqus718LwXcmp5jxsYvJB2CJHzyZ
```
{% endcode %}

### 2. Получим ID токенов

Чтобы проводить следующие операции нам нужно знать ID токенов. Получим его следующей командой:

```text
mina client get-tokens \
-public-key $MINA_PUBLIC_KEY
```

В ответ мы получим следующее:

{% code title="\#ПРИМЕР ОТВЕТА" %}
```text
Accounts are held for token IDs:
1
```
{% endcode %}

### 3. Минт токенов

Чтобы сминтить новые токены нужно выполнить команду `mint-tokens`. Будут созданы 1,000 токенов в учетной записи отправителя транзакции под номером token ID 2.

```text
mina client mint-tokens \
-sender $MINA_PUBLIC_KEY \
-token 2 \
-amount 10
```

Проверим баланс командой \(баланс появится не сразу, нужно подождать около 5 минут\):

```text
mina client get-balance \
-token 2 \
-public-key $MINA_PUBLIC_KEY
```

Через некоторое время мы должны увидеть на балансе 1,000 mina токенов.

{% code title="\#ПРИМЕР ОТВЕТА" %}
```text
mina client get-balance \
-token 2 \
-public-key $MINA_PUBLIC_KEY
Balance: 1000 tokens
```
{% endcode %}

### 4. Отправка токенов

Теперь можно отправить токены.  
Чтобы это сделать нам сначала нужно добавить получателя командой ниже:

```text
mina client create-token-account \
-sender $MINA_PUBLIC_KEY \
-receiver B62qoDWfBZUxKpaoQCoFqr12wkaY84FrhxXNXzgBkMUi2Tz4K8kBDiv \
-token 2
```

Например отправим 50 токенов.   
В поле `-memo "My First TX"` вместо `My First TX` можно вписать что угодно. Либо оставить так, как есть.

```text
mina client send-payment \
-sender $MINA_PUBLIC_KEY \
-receiver B62qoDWfBZUxKpaoQCoFqr12wkaY84FrhxXNXzgBkMUi2Tz4K8kBDiv \
-token 2 \
-fee 0.1 \
-amount 50
```

Готово. Токены отправлены.

