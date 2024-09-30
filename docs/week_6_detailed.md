
# Неделя 6: Введение в CSS стили и их подключение в Flask, а также работа с Flask-WTF для создания форм

## Урок 1: Подключение CSS стилей в Flask

### Цель:
Научиться подключать внешние CSS стили и файлы для оформления страниц веб-приложений.

### Структура проекта

```bash
flask_project/
├── app.py                # Основной файл Flask приложения
├── templates/            # Папка с HTML-шаблонами
│   ├── base.html         # Базовый шаблон для всех страниц
│   ├── form_page.html    # Шаблон формы
│   ├── home.html         # Главная страница
├── static/               # Папка для статических файлов (CSS, изображения, JS)
│   ├── styles.css        # Файл CSS стилей
├── users.db              # Файл базы данных SQLite
```

### Шаг 1: Создание структуры для стилей

1. В корневой папке проекта создайте папку `static` для хранения всех статических файлов, включая стили, изображения и JavaScript файлы.
   
2. В папке `static` создайте файл `styles.css`. В этом файле будут храниться все CSS правила.

   Пример файла `styles.css`:

   ```css
   body {
       font-family: Arial, sans-serif;
       background-color: #f4f4f9;
       margin: 0;
       padding: 0;
   }

   header {
       background-color: #333;
       color: #fff;
       padding: 1rem;
       text-align: center;
   }

   h1 {
       margin: 0;
   }

   .container {
       margin: 2rem auto;
       max-width: 800px;
   }

   form {
       display: flex;
       flex-direction: column;
   }

   input, button {
       padding: 10px;
       margin-bottom: 10px;
       border: 1px solid #ccc;
       border-radius: 5px;
   }
   ```

### Шаг 2: Подключение CSS в шаблоны

1. В шаблоне `base.html`, который является базовым для всех страниц, подключите созданный файл стилей.

   Пример файла `base.html`:

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Flask Project</title>
       <link rel="stylesheet" href="{{ url_for('static', filename='styles.css') }}">
   </head>
   <body>
       <header>
           <h1>Добро пожаловать на наш сайт</h1>
       </header>
       <div class="container">
           {% block content %}{% endblock %}
       </div>
   </body>
   </html>
   ```

   Важно! Используйте функцию `url_for` для правильного подключения статических файлов. Это гарантирует, что CSS файл всегда будет найден Flask.

### Шаг 3: Применение стилей

1. Теперь все страницы, которые расширяют `base.html`, будут автоматически использовать подключенный файл стилей.

   Например, в шаблоне `form_page.html` можно создать форму, которая будет стилизована с помощью подключенных стилей:

   ```html
   {% extends "base.html" %}

   {% block content %}
   <h2>Заполните форму</h2>
   <form method="POST" action="/submit">
       <label for="name">Имя:</label>
       <input type="text" id="name" name="name">
       
       <label for="email">Email:</label>
       <input type="email" id="email" name="email">
       
       <button type="submit">Отправить</button>
   </form>
   {% endblock %}
   ```

### Домашнее задание:
1. Добавьте свои стили для других элементов страницы.
2. Создайте адаптивный дизайн с использованием медиа-запросов в CSS для лучшего отображения на мобильных устройствах.

---

## Урок 2: Создание форм с Flask-WTF и валидаторами

### Цель:
Научиться создавать формы с помощью Flask-WTF, использовать валидаторы для проверки данных и работать с CSRF защитой.

### Структура проекта

```bash
flask_project/
├── app.py                # Основной файл Flask приложения
├── forms.py              # Файл с описанием форм
├── templates/            # Папка с HTML-шаблонами
│   ├── base.html         # Базовый шаблон для всех страниц
│   ├── form_page.html    # Шаблон формы
├── static/               # Папка для стилей
│   ├── styles.css        # Файл CSS стилей
├── users.db              # Файл базы данных SQLite
```

### Шаг 1: Установка Flask-WTF

1. Установите Flask-WTF:

   ```bash
   pip install Flask-WTF
   ```

2. В `app.py` настройте CSRF защиту и инициализируйте Flask-WTF:

   ```python
   from flask_wtf import FlaskForm
   from wtforms import StringField, SubmitField
   from wtforms.validators import DataRequired, Email
   from flask_wtf.csrf import CSRFProtect

   app.config['SECRET_KEY'] = 'your_secret_key'
   csrf = CSRFProtect(app)
   ```

### Шаг 2: Создание форм

1. Создайте файл `forms.py`, в котором будут описаны формы. Пример формы для регистрации:

   ```python
   from flask_wtf import FlaskForm
   from wtforms import StringField, SubmitField
   from wtforms.validators import DataRequired, Email

   class RegistrationForm(FlaskForm):
       name = StringField('Имя', validators=[DataRequired()])
       email = StringField('Email', validators=[DataRequired(), Email()])
       submit = SubmitField('Зарегистрироваться')
   ```

### Шаг 3: Использование форм в маршрутах

1. В `app.py` добавьте маршрут для отображения формы:

   ```python
   from forms import RegistrationForm

   @app.route('/register', methods=['GET', 'POST'])
   def register():
       form = RegistrationForm()
       if form.validate_on_submit():
           name = form.name.data
           email = form.email.data
           # Сохраняем данные в базу или выполняем другие действия
           return f'Спасибо за регистрацию, {name}!'
       return render_template('form_page.html', form=form)
   ```

### Шаг 4: Отображение формы в шаблоне

1. В шаблоне `form_page.html` используйте Jinja2 для отображения полей формы:

   ```html
   {% extends "base.html" %}

   {% block content %}
   <h2>Регистрация</h2>
   <form method="POST" action="/register">
       {{ form.hidden_tag() }}
       <label for="name">{{ form.name.label }}</label>
       {{ form.name(size=32) }}
       
       <label for="email">{{ form.email.label }}</label>
       {{ form.email(size=32) }}
       
       {{ form.submit() }}
   </form>
   {% endblock %}
   ```

   Здесь `{{ form.hidden_tag() }}` добавляет скрытое поле для CSRF защиты, что предотвращает атаки с подделкой запросов.

### Шаг 5: Тестирование валидации формы

1. Flask-WTF автоматически проверяет валидаторы, такие как обязательные поля и правильность формата email.

### Домашнее задание:
1. Создайте форму для входа с полями для имени пользователя и пароля.
2. Добавьте валидацию, чтобы проверить правильность заполнения всех полей.

