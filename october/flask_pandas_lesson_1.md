
# Урок 1: Подключение Pandas к Flask-порталу

## Введение
В этом уроке мы научимся подключать Pandas к веб-приложению на Flask. Мы создадим простое веб-приложение, которое принимает CSV файл от пользователя, анализирует данные с помощью Pandas, а затем возвращает некоторые основные статистики. Это поможет вам понять, как использовать Pandas для анализа данных и отображения результатов в веб-интерфейсе.

## Предварительные требования
Перед тем как начать, вам потребуется:
- Установленный Python (рекомендуется версия 3.8 или выше).
- Установленный Flask (`pip install Flask`).
- Установленный Pandas (`pip install pandas`).

## Установка зависимостей
Сначала создадим виртуальное окружение и установим необходимые библиотеки:

```bash
python -m venv venv
source venv/bin/activate  # Linux/macOS
venv\Scripts\activate  # Windows

pip install Flask pandas
```

## Создание Flask-приложения
Создадим файл `app.py`, который будет основным файлом нашего приложения.

```python
import pandas as pd
from flask import Flask, render_template, request, redirect, url_for
from werkzeug.utils import secure_filename
import os

app = Flask(__name__)
app.config['UPLOAD_FOLDER'] = 'uploads/'

@app.route("/", methods=["GET", "POST"])
def index():
    summary = ""
    if request.method == "POST":
        if 'file' not in request.files:
            return redirect(request.url)
        file = request.files['file']
        if file.filename == '':
            return redirect(request.url)
        if file:
            filename = secure_filename(file.filename)
            filepath = os.path.join(app.config['UPLOAD_FOLDER'], filename)
            file.save(filepath)
            
            # Анализ данных с использованием Pandas
            df = pd.read_csv(filepath)
            summary = df.describe().to_html()  # Получаем основные статистики и преобразуем в HTML
    
    return render_template("index.html", summary=summary)

if __name__ == "__main__":
    if not os.path.exists(app.config['UPLOAD_FOLDER']):
        os.makedirs(app.config['UPLOAD_FOLDER'])
    app.run(debug=True)
```

## Шаблон HTML для загрузки CSV файла
Создадим папку `templates` и внутри неё файл `index.html` для отображения веб-формы.

**Путь**: `templates/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pandas Flask Integration</title>
</head>
<body>
    <h1>Upload CSV for Analysis</h1>
    <form method="POST" action="/" enctype="multipart/form-data">
        <label for="file">Upload a CSV file:</label>
        <input type="file" id="file" name="file" required>
        <button type="submit">Analyze</button>
    </form>
    
    {% if summary %}
        <h2>Summary Statistics:</h2>
        {{ summary | safe }}
    {% endif %}
</body>
</html>
```

## Объяснение кода
1. **Flask-приложение**: Мы создали простое Flask-приложение, которое позволяет загружать CSV файл и использовать Pandas для его анализа.
2. **Pandas**: Мы используем Pandas для анализа данных в загруженном CSV файле. Метод `df.describe()` генерирует основные статистики (среднее, минимальное, максимальное значения и др.), которые затем преобразуются в HTML и отображаются на странице.
3. **Шаблон HTML**: Форма позволяет пользователю загрузить CSV файл, который отправляется на сервер для анализа.

## Запуск приложения
Для запуска приложения выполните следующую команду:

```bash
python app.py
```

Затем откройте браузер и перейдите по адресу [http://127.0.0.1:5000/](http://127.0.0.1:5000/), чтобы увидеть веб-форму. Загрузите CSV файл, и получите основные статистики по данным с помощью Pandas.

## Заключение
В этом уроке мы научились интегрировать Pandas с Flask и создавать простое веб-приложение, которое принимает CSV файл от пользователя и возвращает основные статистики по данным. Это базовая структура, которую можно расширять, добавляя более сложный анализ данных, визуализации и более глубокую обработку данных.
