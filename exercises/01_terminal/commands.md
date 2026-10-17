1. Сколько всего строк в логе?
```bash 
❯ wc -l app.log
31 app.log
```
2. Первые 5 и последние 5 строк лога.
```bash
❯ head -n 5 app.log
2025-10-13 08:01:12 INFO  db: pool initialized
2025-10-13 08:02:03 INFO  auth: user=ann login ok
2025-10-13 08:02:55 WARN  db: slow query 840ms
2025-10-13 08:03:21 INFO  auth: user=bob login ok
2025-10-13 08:04:44 ERROR auth: login failed user=carol
❯ tail -n 5 app.log
2025-10-13 08:40:32 INFO  auth: user=ann login ok
2025-10-13 08:42:50 INFO  tasks: task 19 created by user=carol
2025-10-13 08:45:11 ERROR auth: login failed user=dave
2025-10-13 08:47:26 INFO  tasks: task 20 created by user=dave
2025-10-13 08:49:38 WARN  db: slow query 700ms
```
3. Все ERROR-строки с номерами строк.
```bash 
❯ grep -En --color=auto 'ERROR' app.log
5:2025-10-13 08:04:44 ERROR auth: login failed user=carol
8:2025-10-13 08:09:02 ERROR db: connection lost
11:2025-10-13 08:12:30 ERROR auth: login failed user=bob
15:2025-10-13 08:18:41 ERROR auth: login failed user=bob
19:2025-10-13 08:25:08 ERROR tasks: task 16 not found
24:2025-10-13 08:34:51 ERROR db: connection lost
29:2025-10-13 08:45:11 ERROR auth: login failed user=dave
```
4. Количество строк каждого уровня: INFO, WARN, ERROR (три команды).
```bash
❯ grep -c "INFO" app.log
17
❯ grep -c "ERROR" app.log
7
❯ grep -c "WARN" app.log
7
```
5. ERROR-строки, которые не относятся к подсистеме auth.
```bash
❯ grep -E "ERROR" app.log | grep -v "auth"
2025-10-13 08:09:02 ERROR db: connection lost
2025-10-13 08:25:08 ERROR tasks: task 16 not found
2025-10-13 08:34:51 ERROR db: connection lost
```
6. Все вхождения user=имя из лога (только user=ann, user=bob…, не всю строку) и посчитай, сколько записей у каждого пользователя. Отсортируй по убыванию, покажи топ-3. Подумай: почему обязательно нужен sort перед uniq? ОТВЕТ: из uniq --help: 'uniq' does not detect repeated lines unless they are adjacent. You may want to sort the input first, or use 'sort -u' without 'uniq'. т.е. он сравнивает соседние строки и если строки не отсортированы то одни и те же строки в разных частях файла/вывода будут считаться другими и считаться заново
```bash
❯ grep -oE "user=[a-zA-Z0-9]+\b" app.log | sort -r | uniq -c | sort -nr | head -n 3
      7 user=ann
      5 user=dave
      5 user=carol
```
7. Найти рекурсивно все вхождения слова login в папке exercises/ с номерами строк; затем той же командой с ключом «только имена файлов».
```bash
❯ grep -rn "login" ..
../01_terminal/app.log:2:2025-10-13 08:02:03 INFO  auth: user=ann login ok
../01_terminal/app.log:4:2025-10-13 08:03:21 INFO  auth: user=bob login ok
../01_terminal/app.log:5:2025-10-13 08:04:44 ERROR auth: login failed user=carol
../01_terminal/app.log:9:2025-10-13 08:10:15 INFO  auth: user=dave login ok
../01_terminal/app.log:11:2025-10-13 08:12:30 ERROR auth: login failed user=bob
../01_terminal/app.log:14:2025-10-13 08:16:20 INFO  auth: user=ann login ok
../01_terminal/app.log:15:2025-10-13 08:18:41 ERROR auth: login failed user=bob
../01_terminal/app.log:18:2025-10-13 08:23:55 INFO  auth: user=carol login ok
../01_terminal/app.log:23:2025-10-13 08:32:30 INFO  auth: user=dave login ok
../01_terminal/app.log:27:2025-10-13 08:40:32 INFO  auth: user=ann login ok
../01_terminal/app.log:29:2025-10-13 08:45:11 ERROR auth: login failed user=dave
❯ grep -rl "login" ..
../01_terminal/app.log
../01_terminal/errors.txt
../01_terminal/commands.md
```
8. Найди в ~/dev/taskflow все .md-файлы; затем все директории внутри exercises/.
```bash
❯ find ~/dev/taskflow/ -name "*.md" -not -path '*/.venv/*'
/home/din/dev/taskflow/README.md
/home/din/dev/taskflow/exercises/README.md
/home/din/dev/taskflow/exercises/01_terminal/commands.md
/home/din/dev/taskflow/progress.md
❯ find .. -mindepth 1 -type d
../01_terminal
```
9. Найди в ~/dev/taskflow файлы, изменённые за последние 7 суток. Объясни (в commands.md), почему нашлось столько.
НАШЛОСЬ ДОФИГА ТАК КАК Я ПРОЭКТ СОЗДАЛ МЕНЬШЕ НЕДЕЛИ НАЗАД - это же элементарно Ватсон
```bash
❯ find ~/dev/taskflow/ -mtime -7
/home/din/dev/taskflow/
/home/din/dev/taskflow/.git
/home/din/dev/taskflow/.git/hooks
... 
/home/din/dev/taskflow/.venv/pyvenv.cfg
...
/home/din/dev/taskflow/exercises/01_terminal/app.log
/home/din/dev/taskflow/exercises/01_terminal/commands.md
/home/din/dev/taskflow/progress.md
/home/din/dev/taskflow/requirements.txt
```
ищет файлы и папки рекурсивно внутри указанной папки. я нашел что можно ограничить рекурсию глобальным параметром -maxdepth n
```bash
❯ find ~/dev/taskflow/ -maxdepth 1 -mtime -7
/home/din/dev/taskflow/
/home/din/dev/taskflow/.git
/home/din/dev/taskflow/.venv
/home/din/dev/taskflow/LICENSE
/home/din/dev/taskflow/.gitignore
/home/din/dev/taskflow/README.md
/home/din/dev/taskflow/exercises
/home/din/dev/taskflow/progress.md
/home/din/dev/taskflow/requirements.txt
❯ find ~/dev/taskflow/ -maxdepth 0 -mtime -7
/home/din/dev/taskflow/
❯ find ~/dev/taskflow/ -maxdepth 2 -mtime -7
/home/din/dev/taskflow/
/home/din/dev/taskflow/.git
...
/home/din/dev/taskflow/.git/index
/home/din/dev/taskflow/.venv
...
/home/din/dev/taskflow/progress.md
/home/din/dev/taskflow/requirements.txt
```
10. Запиши все ERROR-строки в файл errors.txt (проверь количество строк в нём), затем допиши в этот же файл все WARN-строки (проверь итоговое количество).

```bash
❯ grep "ERROR" app.log > errors.txt
❯ wc -l errors.txt
7 errors.txt
❯ grep "WARN" app.log  >> errors.txt 
❯ wc -l errors.txt
14 errors.txt
```
11. Расследование. Сколько ERROR-пришлось на каждого пользователя? После ответа объясни в commands.md: почему сумма по пользователям не совпадает с общим числом ERROR-строк? Куда «пропала» разница?

```
на dave, carol по 1 ERROR, а на bob пришлось по 2 ERRORs
ответ: Кроме ERRORS при auth user еще есть ERRORs другого типа: потеря соединения с БД и "ERROR tasks: task 16 not found"
```
12. Бонус (по желанию): найди самый медленный slow query одним конвейером. Подсказка была в теории, в строке про sort -n. 
```bash
❯ grep -Eo "[0-9]+ms" app.log|sort -rn|head -n 1
1540ms
```