# Базовые команды по инициализации различных фреймворков

В процессе

## Начало работы

- `npm init -y` - инициализировать зависимости, если нет файла package.json в автоматическом режиме (-y)
- `npm install` или `npm i` - установить все зависимости в рабочую папку

Далее нужно создать файл .gitignore

- `npx create-gitignore node` - создает один раз файл гитигнора в директории проекта

## ESLint

ESLint - это инструмент, помогающий:

- анализировать код, находить синтаксические ошибки и автоматически их исправлять;
- писать аккуратный код в едином стиле по определенным правилам.

- `npm i -D eslint` - установить пакет ESLint в проект. Флаг -D указывает на установку линтера только во время разработки, поэтому устанавливаем его в т.н. devDependencies

- `npx eslint --init` или `npm init @eslint/config` - инициализация пакета ESLint

После инициализации будет задан ряд вопросов

1. 3
2. 3
3. 3
4. 1
5. 1, 2 (space)
6. 1
7. 1
8. 3
9. 2
10. 1

Далее нужно в расширениях найти ESLint в расширениях ВСКода и установить его

Далее нужно зайти в настройки VSCode и выбрать пункт Параметры

В поиске нужно найти строку default formatter и выбрать "нет (none)"

Далее ищем html format и можем поставить галочки в различных пунктах под себя

В правом верхнем углу ищем кнопку "открыть параметры json"

Открываем, добавляем настройки:

```
"eslint.alwaysShowStatus": true,
    "eslint.format.enable": true,
    "eslint.validate" : [
        "javascript",
        "javascriptreact",
        "typescript",
        "typescriptreact",
    ],
    "files.autoSave": "afterDelay",
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true,
    },
    "editor.formatOnSave": true,
```

В файл .eslintrc.json вставляем следующий код:

```
{
    "env": {
      "browser": true,
      "es2021": true
    },
    "extends": "airbnb-base",
    "parserOptions": {
      "ecmaVersion": "latest"
    },
    "rules": {
      "quotes": ["error", "double"],
      "linebreak-style": ["error", "windows"],
      "no-console": "off",
      "no-restricted-syntax": [
        "error",
        {
          "selector": "CallExpression[callee.object.name='console'][callee.property.name!=/^(log|warn|error|info|trace)$/]",
          "message": "Unexpected property on console object was called"
        }
      ],
      "no-alert": "off"
    }
  }
```

Вроде как можно работать...

## Jest

Jest - фреймворк для тестирования приложений.

TDD - Test Driven Development - методология разработки ПО, которая основывается на повторении коротких циклов разработки: изначально пишется тест, покрывающий желаемое изменение, затем пишется программный код, который реализует желаемое поведение системы и позволит пройти написанный тест. Затем проводится рефакторинг написанного кода с постоянной проверкой прохождения тестов.

`npm install -D jest` - установка jest в devDependencies

В корне проекта нужно создать папку test или spec

В `package.json` добавить следующий раздел:

```
"scripts": {
  "test": "jest"
}
```

`npm test` или `npx jest` - запуск тестов из корневой папки проекта

Для начала стоит сделать todo - описать программу(функцию) - что она должна делать и возвращать
