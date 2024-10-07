
# Урок 1: Подключение Hugging Face Transformers к Flask-порталу

## Введение
В этом уроке мы научимся подключать библиотеку Hugging Face Transformers к веб-приложению на Flask. Мы создадим простое веб-приложение, которое принимает текст от пользователя, использует модель Transformer для обработки текста, а затем возвращает результат. В этом примере мы будем использовать модель для анализа настроений.

## Предварительные требования
Перед тем как начать, вам потребуется:
- Установленный Python (рекомендуется версия 3.8 или выше).
- Установленный Flask (`pip install Flask`).
- Установленный `transformers` и `torch` для работы с моделями Hugging Face (`pip install transformers torch`).

## Установка зависимостей
Сначала создадим виртуальное окружение и установим необходимые библиотеки:

```bash
python -m venv venv
source venv/bin/activate  # Linux/macOS
venv\Scripts\activate  # Windows

pip install Flask transformers torch
```

## Создание Flask-приложения
Создадим файл `app.py`, который будет основным файлом нашего приложения.

```python
from transformers import pipeline
from flask import Flask, render_template, request

app = Flask(__name__)

# Загружаем модель анализа настроений из Hugging Face
sentiment_analysis = pipeline("sentiment-analysis")

@app.route("/", methods=["GET", "POST"])
def index():
    result = ""
    if request.method == "POST":
        user_input = request.form["text"]
        # Анализируем настроение введённого текста
        analysis = sentiment_analysis(user_input)
        result = f"Label: {analysis[0]['label']}, Score: {analysis[0]['score']:.2f}"
    return render_template("index.html", result=result)

if __name__ == "__main__":
    app.run(debug=True)
```

## Шаблон HTML для ввода текста
Создадим папку `templates` и внутри неё файл `index.html` для отображения веб-формы.

**Путь**: `templates/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hugging Face Transformers Flask Integration</title>
</head>
<body>
    <h1>Sentiment Analysis with Hugging Face Transformers</h1>
    <form method="POST" action="/">
        <label for="text">Enter text for sentiment analysis:</label>
        <textarea id="text" name="text" rows="4" cols="50" required></textarea>
        <br>
        <button type="submit">Analyze</button>
    </form>
    
    {% if result %}
        <h2>Result:</h2>
        <p>{{ result }}</p>
    {% endif %}
</body>
</html>
```

## Объяснение кода
1. **Flask-приложение**: Мы создали простое Flask-приложение, которое позволяет пользователю ввести текст и получить анализ настроения с использованием модели Hugging Face.
2. **Hugging Face Transformers**: Мы используем библиотеку `transformers` и предварительно обученную модель для анализа настроений. После получения текста от пользователя, модель возвращает результат с меткой настроения и значением вероятности.
3. **Шаблон HTML**: Форма позволяет пользователю ввести текст, который отправляется на сервер для анализа настроений.

## Запуск приложения
Для запуска приложения выполните следующую команду:

```bash
python app.py
```

Затем откройте браузер и перейдите по адресу [http://127.0.0.1:5000/](http://127.0.0.1:5000/), чтобы увидеть веб-форму. Введите текст, и получите результат анализа настроений с помощью модели Transformer.

## Заключение
В этом уроке мы научились интегрировать Hugging Face Transformers с Flask и создавать простое веб-приложение, которое принимает текст от пользователя и возвращает результат анализа настроений. Это базовая структура, которую можно расширять, добавляя новые модели и улучшая функциональность, например, анализ тональности текста, суммаризацию или генерацию ответов.
