
# Урок 1: Подключение Keras к Flask-порталу

## Введение
В этом уроке мы научимся подключать Keras к веб-приложению на Flask. Мы создадим простое веб-приложение, которое принимает изображение от пользователя, использует модель Keras для предсказания, а затем возвращает результат. Мы будем использовать предварительно обученную модель для классификации изображений.

## Предварительные требования
Перед тем как начать, вам потребуется:
- Установленный Python (рекомендуется версия 3.8 или выше).
- Установленный Flask (`pip install Flask`).
- Установленный TensorFlow, который включает Keras (`pip install tensorflow`).

## Установка зависимостей
Сначала создадим виртуальное окружение и установим необходимые библиотеки:

```bash
python -m venv venv
source venv/bin/activate  # Linux/macOS
venv\Scripts\activate  # Windows

pip install Flask tensorflow
```

## Создание Flask-приложения
Создадим файл `app.py`, который будет основным файлом нашего приложения.

```python
import tensorflow as tf
import numpy as np
from flask import Flask, render_template, request, redirect, url_for
from werkzeug.utils import secure_filename
import os
from PIL import Image

app = Flask(__name__)
app.config['UPLOAD_FOLDER'] = 'uploads/'

# Загрузка предварительно обученной модели Keras
model = tf.keras.applications.MobileNetV2(weights='imagenet')

@app.route("/", methods=["GET", "POST"])
def index():
    prediction = ""
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
            
            # Предсказание с использованием модели Keras
            image = Image.open(filepath).resize((224, 224))
            image_array = np.array(image) / 255.0
            image_array = np.expand_dims(image_array, axis=0)
            predictions = model.predict(image_array)
            decoded_predictions = tf.keras.applications.mobilenet_v2.decode_predictions(predictions, top=1)
            prediction = decoded_predictions[0][0][1]  # Название предсказанного класса
    
    return render_template("index.html", prediction=prediction)

if __name__ == "__main__":
    if not os.path.exists(app.config['UPLOAD_FOLDER']):
        os.makedirs(app.config['UPLOAD_FOLDER'])
    app.run(debug=True)
```

## Шаблон HTML для загрузки изображения
Создадим папку `templates` и внутри неё файл `index.html` для отображения веб-формы.

**Путь**: `templates/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Keras Flask Integration</title>
</head>
<body>
    <h1>Image Classification with Keras</h1>
    <form method="POST" action="/" enctype="multipart/form-data">
        <label for="file">Upload an image:</label>
        <input type="file" id="file" name="file" required>
        <button type="submit">Classify</button>
    </form>
    
    {% if prediction %}
        <h2>Prediction:</h2>
        <p>{{ prediction }}</p>
    {% endif %}
</body>
</html>
```

## Объяснение кода
1. **Flask-приложение**: Мы создали простое Flask-приложение, которое позволяет загружать изображение и использовать модель Keras для его классификации.
2. **Keras**: Мы используем предварительно обученную модель `MobileNetV2` для классификации изображений. После загрузки изображения, оно преобразуется в нужный формат, передаётся модели, и результат предсказания отображается на веб-странице.
3. **Шаблон HTML**: Форма позволяет пользователю загрузить изображение, которое отправляется на сервер для предсказания.

## Запуск приложения
Для запуска приложения выполните следующую команду:

```bash
python app.py
```

Затем откройте браузер и перейдите по адресу [http://127.0.0.1:5000/](http://127.0.0.1:5000/), чтобы увидеть веб-форму. Загрузите изображение, и получите предсказание от Keras.

## Заключение
В этом уроке мы научились интегрировать Keras с Flask и создавать простое веб-приложение, которое принимает изображение от пользователя и возвращает результат классификации. Это базовая структура, которую можно расширять, добавляя новые модели, улучшая обработку изображений и создавая более сложные предсказательные сервисы.
