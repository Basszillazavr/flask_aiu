
# Flask Interview Questions and Technical Tasks

## Теоретические вопросы по Flask

1. **Что такое Flask и чем он отличается от других фреймворков, таких как Django?**
   - **Ответ**: Flask — это микро-фреймворк для Python, предназначенный для создания веб-приложений. Он легковесный и модульный, предоставляя разработчику больше гибкости. В отличие от Django, который является "батарейками в комплекте" фреймворком, Flask не имеет встроенных инструментов, таких как ORM или аутентификация, но предоставляет возможность добавлять их по мере необходимости, обеспечивая более гибкий и минималистичный подход.

2. **Как работает маршрутизация (routing) в Flask?**
   - **Ответ**: В Flask маршрутизация определяется через декоратор `@app.route()` или метод `add_url_rule()`. Эти методы связывают URL-адрес с определенной функцией представления. Когда приходит запрос на определенный URL, Flask сопоставляет его с маршрутом и вызывает соответствующую функцию, которая возвращает ответ.

3. **Какие бывают HTTP методы и как они реализуются в Flask?**
   - **Ответ**: Основные HTTP методы включают GET, POST, PUT, DELETE и PATCH. В Flask они указываются как параметры декоратора `@app.route()`. Например, `@app.route('/example', methods=['GET', 'POST'])` определяет, что функция может обрабатывать запросы GET и POST.

4. **Как осуществляется обработка форм и запросов в Flask?**
   - **Ответ**: В Flask данные форм доступны через объект `request`, который импортируется из модуля `flask`. Например, `request.form` содержит данные из формы, отправленной через POST-запрос. Также можно использовать `request.args` для получения данных из строки запроса (GET-параметров).

5. **Как управлять сессиями в Flask?**
   - **Ответ**: Flask поддерживает управление сессиями с помощью модуля `flask.session`, который хранит данные о пользователе между запросами. Сессии по умолчанию подписываются с использованием `SECRET_KEY` для обеспечения безопасности и хранятся на стороне клиента в виде защищенных cookie.

6. **Что такое Blueprint в Flask и для чего он используется?**
   - **Ответ**: Blueprint — это способ организации кода приложения в более структурированную и модульную форму. Blueprints позволяют разделить приложение на различные модули или компоненты, каждый из которых может иметь свои маршруты, статические файлы и шаблоны. Это особенно полезно для крупных приложений.

7. **Как в Flask организована работа со статическими файлами?**
   - **Ответ**: Статические файлы, такие как CSS, JavaScript и изображения, размещаются в папке `static`. Flask предоставляет URL `/static/filename` для доступа к этим файлам. Например, файл `static/style.css` будет доступен по адресу `/static/style.css`.

8. **Как использовать Jinja2 шаблоны и что такое контекст в Flask?**
   - **Ответ**: Jinja2 — это шаблонный движок, встроенный в Flask, который позволяет вставлять Python-код в HTML и обрабатывать его. Контекст — это данные, которые передаются в шаблон из представления с помощью функции `render_template()`. Например, `render_template('index.html', user=user)` передает переменную `user` в шаблон `index.html`.

9. **Как Flask поддерживает работу с базой данных?**
   - **Ответ**: Flask не предоставляет встроенного ORM, но часто используется с SQLAlchemy, который можно интегрировать в проект через Flask-SQLAlchemy. Flask-SQLAlchemy предоставляет методы для взаимодействия с базой данных и работы с моделями. Также можно использовать `Flask-Migrate` для управления миграциями базы данных.

10. **Какие средства для тестирования приложений предлагает Flask?**
    - **Ответ**: Flask предоставляет тестовый клиент через `app.test_client()`, который позволяет отправлять запросы к приложению и проверять ответы, не поднимая сервер. Кроме того, Flask поддерживает тестирование с помощью `pytest` и `unittest`.

---

## Технические задачи по Flask

### Легкие задачи

1. **Задача:** Создать простое Flask-приложение с маршрутом `/hello`, который возвращает строку "Hello, World!".
   - **Решение**:
     ```python
     from flask import Flask

     app = Flask(__name__)

     @app.route('/hello')
     def hello():
         return "Hello, World!"

     if __name__ == '__main__':
         app.run()
     ```

2. **Задача:** Добавить маршрут `/greet/<name>`, который принимает имя в URL и возвращает сообщение "Hello, <name>!".
   - **Решение**:
     ```python
     @app.route('/greet/<name>')
     def greet(name):
         return f"Hello, {name}!"
     ```

---

### Средние задачи

1. **Задача:** Реализовать форму для отправки имени пользователя с помощью POST-запроса на маршрут `/submit`. Форма должна отправлять имя пользователя, и приложение должно отображать приветственное сообщение с этим именем на странице `/welcome`.
   - **Решение**:
     ```python
     from flask import Flask, request, render_template_string

     app = Flask(__name__)

     @app.route('/submit', methods=['GET', 'POST'])
     def submit():
         if request.method == 'POST':
             name = request.form['name']
             return render_template_string("<h1>Welcome, {{ name }}!</h1>", name=name)
         return render_template_string('''
             <form method="post">
                 <input type="text" name="name" placeholder="Enter your name">
                 <input type="submit">
             </form>
         ''')

     if __name__ == '__main__':
         app.run()
     ```

2. **Задача:** Создать API с маршрутом `/json` для обработки GET-запроса, который возвращает JSON-ответ с данными о пользователе (например, имя и возраст).
   - **Решение**:
     ```python
     from flask import Flask, jsonify

     app = Flask(__name__)

     @app.route('/json')
     def get_json():
         return jsonify({"name": "John", "age": 30})

     if __name__ == '__main__':
         app.run()
     ```

---

### Сложные задачи

1. **Задача:** Создать CRUD-приложение для управления списком задач (tasks) с использованием Flask и SQLite. Реализовать функции добавления, удаления, редактирования и просмотра задач.
   - **Решение**:
     - Создать базу данных с таблицей для хранения задач.
     - Настроить маршруты для CRUD-операций.
     - Реализовать шаблоны для отображения задач и форм.

     ```python
     from flask import Flask, request, render_template, redirect, url_for
     from flask_sqlalchemy import SQLAlchemy

     app = Flask(__name__)
     app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///tasks.db'
     db = SQLAlchemy(app)

     class Task(db.Model):
         id = db.Column(db.Integer, primary_key=True)
         description = db.Column(db.String(200), nullable=False)

     db.create_all()

     @app.route('/')
     def index():
         tasks = Task.query.all()
         return render_template('index.html', tasks=tasks)

     @app.route('/add', methods=['POST'])
     def add_task():
         new_task = Task(description=request.form['description'])
         db.session.add(new_task)
         db.session.commit()
         return redirect(url_for('index'))

     @app.route('/delete/<int:id>')
     def delete_task(id):
         task = Task.query.get(id)
         db.session.delete(task)
         db.session.commit()
         return redirect(url_for('index'))

     if __name__ == '__main__':
         app.run()
     ```

2. **Задача:** Создать API с использованием Flask и SQLAlchemy для управления списком пользователей. Реализовать эндпоинты для создания, удаления, редактирования и получения информации о пользователях. Реализовать также авторизацию с использованием токенов JWT для обеспечения доступа к маршрутам.
   - **Решение**:
     - Настроить Flask и SQLAlchemy для работы с базой данных пользователей.
     - Создать эндпоинты для CRUD-операций.
     - Использовать библиотеку `flask-jwt-extended` для реализации JWT-авторизации.

     ```python
     from flask import Flask, jsonify, request
     from flask_sqlalchemy import SQLAlchemy
     from flask_jwt_extended import JWTManager, create_access_token, jwt_required

     app = Flask(__name__)
     app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///users.db'
     app.config['JWT_SECRET_KEY'] = 'super-secret'
     db = SQLAlchemy(app)
     jwt = JWTManager(app)

     class User(db.Model):
         id = db.Column(db.Integer, primary_key=True)
         username = db.Column(db.String(80), unique=True, nullable=False)
         password = db.Column(db.String(120), nullable=False)

     db.create_all()

     @app.route('/login', methods=['POST'])
     def login():
         username = request.json.get('username')
         password = request.json.get('password')
         user = User.query.filter_by(username=username, password=password).first()
         if user:
             access_token = create_access_token(identity=username)
             return jsonify(access_token=access_token)
         return jsonify({"msg": "Bad credentials"}), 401

     @app.route('/user', methods=['POST'])
     @jwt_required()
     def create_user():
         username = request.json['username']
         password = request.json['password']
         user = User(username=username, password=password)
         db.session.add(user)
         db.session.commit()
         return jsonify({"msg": "User created successfully"}), 201

     if __name__ == '__main__':
         app.run()
     ```
