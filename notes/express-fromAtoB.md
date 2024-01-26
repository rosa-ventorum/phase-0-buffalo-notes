# ВНИМАНИЕ! Данная страница пока в разработке. Автор пока только накидал общую структуру, еще сам не пробовал по ней же работать. Используйте с умом.

---
---
---
---
---

# ИНИЦИАЛИЗАЦИЯ ПРОЕКТА, КОР, НАСТРОЙКИ ЛИНТА И ПРОЧЕЕ

## Зависимости

1. `npm init -y` - инициализация package.json
2. `npm i pg pg-hstore sequelize` - инициализация sequelize
3. `npm i -D sequelize-cli` - инициализация CLI библиотеки для sequelize
4. `npm i express` - его величество Экспресс
5. `npm i morgan` - логгер событий в консоли при определенных событиях (в эндпоинт постучались и тп)
6. `npm i -D nodemon` - вотчер, который автоматом перезагружает сервер при внесении и сохранении изменений в файлах js и jsx
7. `npm i @babel/core @babel/preset-env @babel/preset-react @babel/register` - какая-то штука чтобы все работало
8. `npm i react react-dom` - его величество реакт
9. `npm i dotenv` - библиотека, которая позволяет использовать файл окружения, который будет игнорироваться гитом. В нем будут лежать всевозможные пароли, апи-ключи и коды доступа к БД
10. `npx create-gitignore node` - создаем собсна гитигнор файл. Важно создать его после .env, иначе он не попадет в игнор. Возможно.
11. `npx eslint --init` - инициализируем пакет ESLint (опять же, после инициализации реакта, иначе будет страдание)

### Быстрые зависимости (весь кор проекта в строчку, для винды)

```
npm init -y ; npm i pg pg-hstore sequelize express morgan @babel/core @babel/preset-env @babel/preset-react @babel/register react react-dom dotenv ; npm i -D sequelize-cli nodemon ; npx create-gitignore node ; npx eslint --init
```

---

### Быстрая настройка ESLint

1. <kbd>↓</kbd> + <kbd>Enter</kbd>
2. <kbd>↓</kbd> + <kbd>Enter</kbd>
3. <kbd>Enter</kbd>
4. <kbd>Enter</kbd>
5. <kbd>↓</kbd> + <kbd>Space</kbd> + <kbd>Enter</kbd>
6. <kbd>Enter</kbd>
7. <kbd>Enter</kbd>
8. <kbd>↓</kbd> + <kbd>↓</kbd> + <kbd>Enter</kbd>

- Timeout...

9. <kbd>Enter</kbd>
10. <kbd>Enter</kbd>

---

В файле `eslintrc.json`:

```json
"rules": {
    "react/prop-types": 0,
    "react/jsx-one-expression-per-line": 0,
    "react/no-array-index-key": 0,
    "jsx-a11y/control-has-associated-label": 0,
    "jsx-a11y/anchor-has-content": 0,
    "jsx-a11y/anchor-is-valid": 0,
    "linebreak-style": 0,
    "strict": 0,
    "no-console": 0,
    "quotes": ["error", "single"],
    "global-require": 0,
    "import/no-dynamic-require": 0,
    "max-len": 0,
    "no-alert": 0
}
```

# СКЕЛЕТ

В терминале:

```
md public\styles ; md public\scripts ; md public\media ; md src\lib ; md src\routers ; md src\views\components ; md src\views\pages
```

Файлы пока не работают... НЕ ПИШИТЕ ЭТУ СТРОЧКУ
    echo '' > .env ; echo '' > .sequelizerc ; echo '' > .babelrc ; echo '' > public\scripts\index.js ; echo '' > src\app.js ; echo '' > src\lib\renderTemplate.js ; echo '' > src\routers\index.router.js ; echo '' > src\views\Layout.jsx

# База данных

В файле `/.sequelizerc`:

```js
const path = require('path');
require('dotenv').config();

module.exports = {
    'config': path.resolve('db', 'config', 'database.json'),
    'models-path': path.resolve('db', 'models'),
    'seeders-path': path.resolve('db', 'seeders'),
    'migrations-path': path.resolve('db', 'migrations')
};
```

- В терминале: `npx sequelize-cli init`

В файле `/.env`:

```
PORT=3005
DATABASE=postgres://admin:123@localhost:5432/dbname
```

В файле `/db/config/database.json`:

```json
{
  "development": {
    "use_env_variable": "DATABASE"
  }
}
```

В файле `/.babelrc`:

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

# EXPRESS

В файле `/src/app.js`:

```js
require('@babel/register');
require('dotenv').config();

const express = require('express');
const morgan = require('morgan');
const path = require('path');

const app = express();
const { PORT } = process.env;

app.use(express.urlencoded({ extended: true }));
app.use(express.json());
app.use(morgan('dev'));
app.use(express.static(path.join(process.cwd(), 'public')));

const indexRouter = require('./routers/index-router');
const apiRouter = require('./routers/api-router');

app.use('/', indexRouter);
app.use('/api/v1', apiRouter);

app.listen(PORT, () => console.log(`Сервер запущен: http://localhost:${PORT}`));
```

В файле `/src/lib/renderTemplate.js`:

```js
const React = require('react');
const ReactDOMServer = require('react-dom/server');

module.exports = function renderTemplate(reactElement, properties, response) {
  const reactEl = React.createElement(reactElement, properties);
  const html = ReactDOMServer.renderToStaticMarkup(reactEl);
  response.send(`<!DOCTYPE html>${html}`);
  response.end();
};
```

Примерная структура index файлов-роутеров (`/src/routers/index-router.js`):

```js
const indexRouter = require('express').Router();
const renderTemplate = require('../lib/renderTemplate');
const MainPage = require('../views/pages/MainPage');

indexRouter.get('/', async (req, res) => {
  try {
    renderTemplate(MainPage, {}, res);
  } catch (error) {
    console.log(error);
    res.sendStatus(500);
  }
});

module.exports = indexRouter;
```

Примерная структура api файлов-роутеров (`/src/routers/api-router.js`):

```js
const apiRouter = require('express').Router();
const { Post } = require('../../db/models');

apiRouter.get('/posts', async (req, res) => {
  try {
    const posts = await Post.findAll();
    res.json(posts);
  } catch (error) {
    console.log(error);
    res.sendStatus(500);
  }
});

module.exports = apiRouter;
```

- `req.body` - eсли хотим получить тело `fetch` с фронта
- `req.params` - eсли хотим получить `id` из пути `"/posts/:id"`
- `req.query` - eсли хотим получить тело из пути `"/posts/?title=123"`

# REACT

В файле `/src/views/Layout.jsx`:

```js
const React = require('react');

module.exports = function Layout({ children }) {
  return (
    <html lang="ru">
      <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>React-SSR Document</title>
        {/* тут даже комментарии работают. Можно подключать через defer скрипты и стили относительно /public */}
      </head>
      <body>{children}</body>
    </html>
  );
};
```

В реакте весь JS код выполняется внутри усов - `{ someCode() }`

Хотим сделать цикл - пожалуйста! Прокидываем пропсом в страницу массив и колдуем

В файле `/src/views/pages/HomePage.jsx`:

```js
const React = require('react');
const Layout = require('../Layout');

module.exports = function HomePage({ posts }) {
  return (
    <Layout>
      <script defer src="/js/index.js" />
      <div className="post-container">
        { posts.map((post) => (
          <article id={post.id} key={post.id}>
            <span className="points">{post.votes}</span>
            <span className="username">{post.username}</span>
            <span className="comment-count">{post.commentCount}</span>
            <a id={post.id} className="delete-btn delete" href={`/posts/${post.id}`} />
          </article>
        )) }
      </div>
    </Layout>
  );
};
```

# ФРОНТ

В файле `/public/scripts/index.js`:

```js
const button = document.querySelector('.some-button');

button.addEventListener('click', async (event) => {
  if (event.target.tagName === 'BUTTON') {
    event.preventDefault(); // если обрабатываем событие отправки формы или ссылки или еще чего
    try {
      const response = await fetch('/api/v1/somepath', {
        method: 'POST', // GET POST PUT DELETE
        headers: {
          'Content-type': 'application/json',
        },
        body: JSON.stringify(someObjWithData), // если ничего не нужно то можно и не передавать
      });

      if (response.status === 200) {
        const dataFromApi = await response.json(); // формируем объект из ответа бэка - dataFromApi
        doSomethingWithData(dataFromApi);
      } else {
        alert('Какая-нибудь ошибка...');
        throw new Error('Syntax error');
      }
    } catch (error) {
      console.log(error);
    }
  }
});
```

Также мы можем на фронте формировать некоторый HTML-код используя данные из `dataFromApi`

- ```const someHTMLWithInterpolation = `<span>${dataFromApi.text}</span>`;```

А потом внедрять его в дом через:

- `someDOMNode.insertAdjacentHTML('afterbegin', someHTMLWithInterpolation);`

Может пригодиться:

- `event.target.closest('article').remove()`
- `event.target.classList.value.includes('delete-btn')`
- `const { id } = event.target`

```js
const form = document.querySelector('#some-form');

form.addEventListener('submit', async (event) => {
  event.preventDefault();
  const data = new FormData(form);
  const inputs = Object.fromEntries(data);
  console.log(inputs);
  // тратата с фетчами, можем отправить инпуты на бэк и он их будет обрабатывать
});
```
