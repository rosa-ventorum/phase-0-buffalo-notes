# Чуть более продвинутая работа с гитом

## Работа с гитом в процессе решения задачи:

1. `git checkout -b yourBranchName` – переходим в, или создаем новую (-b) ветку "yourBranchName"
2. Пишем код, решающий задачу
3. `git commit -m 'message'` - коммитим все свои изменения
4. `git checkout main` - переходим в другую ветку, в данном случае в main
5. `git pull` – подтягиванием данные с удаленной main-ветки в нашу локальную main-ветку
6. `git checkout yourBranchName` - возвращаемся в свою ветку 
7. `git merge main` - выполняем слияние нашей ветки и ветки main
8. Если есть конфликты – решаем их
9. `git push –set-upstream origin yourBranchName` - пушим репозиторий с привязкой к main в общий доступ

(P.S – можно сперва прописать git push, появится ошибка, предложит выше написанный путь; следующей командой можем спокойно его скопировать, если трудно запомнить на первое время)

10. Создаём пулл реквест в самом гите, пишем что было сделано и отправляем на код ревью.

## Базовые команды:

- Работа с коммитами и удалёнными репозиториями:
    - `git status` - отображает измененные или добавленные файлы и папки с момента последнего коммита
    - `git add filenames` – добавляет файлы для коммита
    - `git add -A` - добавляет все файлы
    - `git comit -m 'message'` – коммит добавленные файлы с сообщением
    - `git fetch` – запрашивает информацию о ветках с удалённого репозитория
    - `git push` – отправляет данные на удалённый репозиторий
    - `git pull` – получает данные с удаленного репозитория

- Работа с ветками:
    - `git checkout branchName` – переключаемся на существующую ветку
    - `git checkout -b branchName` – создаем новую ветку (если не было) с именем branchName и переключаемся на неё
    - `git branch` – получаем список доступных веток
    - `git merge branchName` – запускает процесс слияния двух веток (чаще всего, слияние будет с main или dev)
    - `git merge --continue` – продолжает процесс слияния после разрешения мердж-конфликтов
    - `git merge --abort` – сбрасывает слияние клеток

Большое спасибо @Kirasamie за создание данной статьи!