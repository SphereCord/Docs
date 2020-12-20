---
description: Получение оповещений в реальном времени.
---

# Веб-крючки

Это руководство научит вас отслеживать добавление голосов и отзывов на вашем листинге при помощи веб-крючка \(webhook\).

## Обработка конечной точки

Примеры кода для вашей конечной точки и обработки входящего запроса при срабатывании событий.

{% tabs %}
{% tab title="Node.js" %}
```javascript
const express = require('express')

const app = express()
const PORT = 3266 // У вас может быть любой другой

app.use(express.json())

app.post('/webhook', (req, res) => {
    // Ваш код
})

app.listen(PORT, () => console.log(`App is listening on port ${PORT}`))
```
{% endtab %}

{% tab title="Python" %}
```python
from flask import Flask

app = Flask(__name__)
PORT = 3266  # У вас может быть любой другой

@app.route('/webhook', methods=['POST'])
def webhook():
    # Ваш код

app.run(port=PORT)
```
{% endtab %}
{% endtabs %}

### Тело входящего запроса

Каждый входящий запрос содержит в себе JSON данные в теле запроса, ниже приведен пример.

{% tabs %}
{% tab title="Голоса" %}
| Ключ | Тип | Описание |
| :--- | :--- | :--- |
| `event` | string | Название события |
| `guild_id` | snowflake | Идентификатор сервера |
| `user_id` | snowflake | Идентификатор пользователя |

```javascript
{
    event: "VOTE_ADD",
    guild_id: "338202622731485185",
    user_id: "258317078560243712"
}
```
{% endtab %}

{% tab title="Отзывы" %}
| Ключ | Тип | Описание |
| :--- | :--- | :--- |
| `event` | string | Название события |
| `guild_id` | snowflake | Идентификатор сервера |
| `user_id` | snowflake | Идентификатор пользователя |
| `review` | object | Данные оставленного отзыва |

```javascript
{
    event: "REVIEW_ADD",
    guild_id: "338202622731485185",
    user_id: "258317078560243712",
    review: {
        id: "258317078560243712",
        published_timestamp: 1604839992654,
        stars: 5,
        content: {
            title: "Lorem Ipsum",
            description: "Lorem Ipsum is simply dummy text"
        }
    }
}
```
{% endtab %}
{% endtabs %}

## Проверка подлинности

Мы предлагаем вам использовать это для идентификации нашего сервиса. Обратите внимание, что запрос содержит в себе заголовок `X-Sphere-Signature`, который представляет из себя HEX-digest, содержащий тело запроса подписанный с помощью [HMAC](https://ru.wikipedia.org/wiki/HMAC) \(алгоритм SHA256\) с использованием вашего секретного ключа. Помните, что секретный ключ всегда должен оставаться секретным.

{% tabs %}
{% tab title="Node.js" %}
```javascript
const crypto = require('crypto')

const SECRET_KEY = 'ВАШ_СЕКРЕТНЫЙ_КЛЮЧ'
const DATA = JSON.stringify(req.body)

const hash = req.headers['x-sphere-signature']
const hmac = crypto.createHmac('sha256', SECRET_KEY).update(DATA).digest('hex')

if (hash === hmac) {
    // Ваш код
}

else {
    // Ваш код
}
```
{% endtab %}
{% tab title="Python" %}
```python
import hmac
import hashlib

secret_key = b'126f6954b62cd81a11bc0adf42e571ec129bb8883c3c34d82da1e42f84a27cab'
data = request.data.encode('utf-8')

_hash = request.headers.get('X-Sphere-Signature')
_hmac = hmac.new(secret_key, data, hashlib.sha256).hexdigest()

if _hash == _hmac:
    # Ваш код

else:
    # Ваш код
```
{% endtab %}
{% endtabs %}

