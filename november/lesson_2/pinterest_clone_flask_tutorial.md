
# Урок: Создание веб-сайта на Flask, похожего на Pinterest

## 1. Подготовка окружения
- Создайте виртуальное окружение для изоляции проекта:
  ```bash
  python -m venv venv
  source venv/bin/activate  # для macOS/Linux
  .\venv\Scripts\activate  # для Windows
  ```
- Установите Flask и основные библиотеки:
  ```bash
  pip install Flask Flask-SQLAlchemy Flask-Migrate Flask-WTF Flask-Login Flask-Bcrypt Flask-Uploads Pillow
  ```
- (Опционально) Установка Flask-Bootstrap для интерфейса:
  ```bash
  pip install flask-bootstrap
  ```

## 2. Создание структуры проекта
Создайте структуру папок:
```
pinterest_clone/
├── app/
│   ├── __init__.py
│   ├── models.py
│   ├── forms.py
│   ├── routes.py
│   ├── static/
│   │   └── uploads/  # для хранения загруженных изображений
│   └── templates/
├── migrations/
├── config.py
└── run.py
```

## 3. Настройка конфигурации (`config.py`)
Создайте файл конфигурации `config.py`:
```python
import os

class Config:
    SECRET_KEY = os.getenv('SECRET_KEY', 'your_secret_key')
    SQLALCHEMY_DATABASE_URI = os.getenv('DATABASE_URL', 'sqlite:///site.db')
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    UPLOADED_PHOTOS_DEST = os.path.join(os.getcwd(), 'app/static/uploads')
```

## 4. Основные модели (models.py)
Пример модели `User`:
```python
from app import db
from flask_login import UserMixin

class User(db.Model, UserMixin):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(150), nullable=False, unique=True)
    email = db.Column(db.String(120), nullable=False, unique=True)
    password_hash = db.Column(db.String(128))
    boards = db.relationship('Board', backref='user', lazy=True)
```
Пример модели `Board`:
```python
class Board(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    description = db.Column(db.Text)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    images = db.relationship('Image', backref='board', lazy=True)
```
Пример модели `Image`:
```python
from datetime import datetime
from app import db

class Image(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    image_file = db.Column(db.String(20), nullable=False)
    description = db.Column(db.Text)
    board_id = db.Column(db.Integer, db.ForeignKey('board.id'), nullable=False)
    date_posted = db.Column(db.DateTime, nullable=False, default=datetime.utcnow)
```

## 5. Формы (forms.py)
Пример формы для загрузки изображения:
```python
from flask_wtf import FlaskForm
from flask_wtf.file import FileField, FileAllowed
from wtforms import StringField, TextAreaField, SubmitField
from wtforms.validators import DataRequired

class ImageUploadForm(FlaskForm):
    image = FileField('Upload Image', validators=[FileAllowed(['jpg', 'png'], 'Images only!')])
    description = TextAreaField('Description', validators=[DataRequired()])
    submit = SubmitField('Post')
```

## 6. Маршруты (routes.py)
Пример маршрута для загрузки изображения:
```python
from flask import render_template, url_for, flash, redirect, request
from app import db, photos
from app.forms import ImageUploadForm
from app.models import Board, Image

@app.route("/upload_image", methods=['GET', 'POST'])
def upload_image():
    form = ImageUploadForm()
    if form.validate_on_submit():
        filename = photos.save(form.image.data)
        image = Image(image_file=filename, description=form.description.data)
        db.session.add(image)
        db.session.commit()
        flash('Image uploaded!', 'success')
        return redirect(url_for('home'))
    return render_template('upload_image.html', form=form)
```

## 7. Шаблоны (templates/)
Пример базового шаблона `base.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pinterest Clone</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="{{ url_for('home') }}">Pinterest Clone</a>
        <div class="navbar-nav">
            <a class="nav-item nav-link" href="{{ url_for('upload_image') }}">Upload Image</a>
            <a class="nav-item nav-link" href="{{ url_for('profile') }}">Profile</a>
        </div>
    </nav>
    <div class="container">
        {% with messages = get_flashed_messages(with_categories=true) %}
          {% if messages %}
            {% for category, message in messages %}
              <div class="alert alert-{{ category }}">{{ message }}</div>
            {% endfor %}
          {% endif %}
        {% endwith %}
        {% block content %}{% endblock %}
    </div>
</body>
</html>
```

## 8. Обработка изображений и файлов (Flask-Uploads)
Настройка Flask-Uploads:
```python
from flask_uploads import UploadSet, configure_uploads, IMAGES
photos = UploadSet('photos', IMAGES)
configure_uploads(app, photos)
```
Добавьте в `config.py` директорию для загрузок:
```python
UPLOADED_PHOTOS_DEST = os.path.join(os.getcwd(), 'app/static/uploads')
```

## 9. Тестирование и завершение
- Запуск приложения:
  ```bash
  flask run
  ```
- Проверьте основные функции, такие как загрузка изображений, просмотр профилей и создание досок.

## 10. Расширенные функции
- Добавьте такие функции, как:
  - **Комментарии и лайки** для изображений.
  - **Поиск** по загруженным изображениям.
  - **Фильтры** по категориям или доскам.
  - **Подписка на других пользователей** и просмотр их обновлений.
