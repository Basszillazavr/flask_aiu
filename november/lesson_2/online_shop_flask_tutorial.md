
# Урок: Создание онлайн-магазина на Flask

## 1. Подготовка окружения
- Установите виртуальное окружение для изоляции проекта:
  ```bash
  python -m venv venv
  source venv/bin/activate  # на macOS и Linux
  .\venv\Scripts\activate  # на Windows
  ```
- Установка необходимых библиотек через `pip`. Основной список библиотек для старта:
  ```bash
  pip install Flask Flask-SQLAlchemy Flask-Migrate Flask-WTF Flask-Login Flask-Bcrypt Flask-Mail
  ```
- Установка библиотеки Bootstrap (опционально):
  ```bash
  pip install flask-bootstrap
  ```

## 2. Создание структуры проекта
Создайте структуру папок:
```
my_shop/
├── app/
│   ├── __init__.py
│   ├── models.py
│   ├── forms.py
│   ├── routes.py
│   ├── static/
│   └── templates/
├── migrations/
├── config.py
└── run.py
```

## 3. Конфигурация приложения
Создание файла конфигурации `config.py`, где хранятся настройки:
```python
import os

class Config:
    SECRET_KEY = os.getenv('SECRET_KEY', 'your_secret_key')
    SQLALCHEMY_DATABASE_URI = os.getenv('DATABASE_URL', 'sqlite:///site.db')
    SQLALCHEMY_TRACK_MODIFICATIONS = False
```

## 4. Модели базы данных (models.py)
Пример модели `User`:
```python
from app import db
from flask_login import UserMixin

class User(db.Model, UserMixin):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(150), nullable=False, unique=True)
    email = db.Column(db.String(120), nullable=False, unique=True)
    password_hash = db.Column(db.String(128))
```

## 5. Формы для ввода данных (forms.py)
Пример формы регистрации:
```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Email, EqualTo

class RegistrationForm(FlaskForm):
    username = StringField('Username', validators=[DataRequired()])
    email = StringField('Email', validators=[DataRequired(), Email()])
    password = PasswordField('Password', validators=[DataRequired()])
    confirm_password = PasswordField('Confirm Password', validators=[DataRequired(), EqualTo('password')])
    submit = SubmitField('Sign Up')
```

## 6. Маршруты (routes.py)
Пример маршрута для регистрации:
```python
from flask import render_template, url_for, flash, redirect
from app.forms import RegistrationForm
from app import db
from app.models import User

@app.route("/register", methods=['GET', 'POST'])
def register():
    form = RegistrationForm()
    if form.validate_on_submit():
        user = User(username=form.username.data, email=form.email.data)
        user.set_password(form.password.data)
        db.session.add(user)
        db.session.commit()
        flash('Account created!', 'success')
        return redirect(url_for('login'))
    return render_template('register.html', form=form)
```

## 7. Шаблоны (templates/)
Пример `base.html` с использованием Bootstrap:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Shop</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
</head>
<body>
    {% with messages = get_flashed_messages(with_categories=true) %}
      {% if messages %}
        <div class="container">
            {% for category, message in messages %}
              <div class="alert alert-{{ category }}">{{ message }}</div>
            {% endfor %}
        </div>
      {% endif %}
    {% endwith %}
    <div class="container">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
```

## 8. Авторизация и управление пользователями
Настройка Flask-Login и защита маршрутов для авторизованных пользователей:
```python
from flask_login import login_required

@app.route("/cart")
@login_required
def cart():
    return render_template('cart.html')
```

## 9. Работа с базой данных и миграции
- Инициализация миграций и создание таблиц:
  ```bash
  flask db init
  flask db migrate -m "Initial migration"
  flask db upgrade
  ```

## 10. Расширения и дополнительные функции
- Отправка email с Flask-Mail, например, для подтверждения заказа.
- Административная панель с Flask-Admin для управления товарами.
- REST API с Flask-RESTful (если потребуется для мобильного приложения).

---

## Завершение и тестирование
- Запуск приложения:
  ```bash
  flask run
  ```
- Тестирование функциональности и устранение ошибок.
