
# Урок 1: Подключение ChatGPT к Flask-порталу

## Введение
В этом уроке мы научимся подключать ChatGPT к веб-приложению на Flask. Мы создадим простую веб-форму, через которую пользователи смогут отправлять вопросы, а ChatGPT будет возвращать ответы. Мы будем использовать OpenAI API для интеграции ChatGPT с Flask.

## Предварительные требования
Перед тем как начать, вам потребуется:
- Установленный Python (рекомендуется версия 3.8 или выше).
- Установленный Flask (`pip install Flask`).
- Создать аккаунт на [OpenAI](https://beta.openai.com/signup/), чтобы получить API-ключ.

## Установка зависимостей
Сначала создадим виртуальное окружение и установим необходимые библиотеки:

```bash
python -m venv venv
source venv/bin/activate  # Linux/macOS
venv\Scripts\activate  # Windows

pip install Flask openai
```

## Создание Flask-приложения
Создадим файл `app.py`, который будет основным файлом нашего приложения.

```python
import openai
from flask import Flask, render_template, request

app = Flask(__name__)

# Устанавливаем ваш API-ключ OpenAI
openai.api_key = "YOUR_OPENAI_API_KEY"

@app.route("/", methods=["GET", "POST"])
def index():
    answer = ""
    if request.method == "POST":
        user_question = request.form["question"]
        response = openai.Completion.create(
            engine="text-davinci-003",
            prompt=user_question,
            max_tokens=150
        )
        answer = response.choices[0].text.strip()
    return render_template("index.html", answer=answer)

if __name__ == "__main__":
    app.run(debug=True)
```

## Шаблон HTML для отображения формы
Создадим папку `templates` и внутри неё файл `index.html` для отображения веб-формы.

**Путь**: `templates/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ChatGPT Flask Integration</title>
</head>
<body>
    <h1>Chat with ChatGPT</h1>
    <form method="POST" action="/">
        <label for="question">Enter your question:</label>
        <input type="text" id="question" name="question" required>
        <button type="submit">Ask</button>
    </form>
    
    {% if answer %}
        <h2>Answer:</h2>
        <p>{{ answer }}</p>
    {% endif %}
</body>
</html>
```

## Объяснение кода
1. **Flask-приложение**: Мы создали простое Flask-приложение с одним маршрутом (`/`), который обрабатывает как GET, так и POST запросы.
2. **OpenAI API**: Мы используем библиотеку `openai` для отправки запроса к модели ChatGPT. Пользователь вводит вопрос в форму, и с помощью функции `openai.Completion.create()` мы получаем ответ от модели.
3. **Шаблон HTML**: Форма позволяет пользователю ввести вопрос и отправить его на сервер, который возвращает ответ модели и отображает его на странице.

## Запуск приложения
Для запуска приложения выполните следующую команду:

```bash
python app.py
```

Затем откройте браузер и перейдите по адресу [http://127.0.0.1:5000/](http://127.0.0.1:5000/), чтобы увидеть веб-форму. Введите вопрос и получите ответ от ChatGPT.

## Заключение
В этом уроке мы научились интегрировать ChatGPT с Flask и создавать простое веб-приложение, которое принимает вопрос от пользователя и возвращает ответ от модели. Это всего лишь основа, и вы можете расширять функциональность, добавляя, например, сохранение истории диалога или более сложную обработку пользовательских запросов.
