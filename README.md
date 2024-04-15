# ГГИС Паноптикум
Скрипты для упрощения жизни при работе с Kaiten, Gitlab и пр.

## Cодержание  

- [Установка зависимостей](#установка-зависимостей)  
- [Использование skird.py](#использование-skirdpy)  
   - [Изменение конфигурации](#изменение-конфигурации)  
- [Использование time_management.py](#использование-time_managementpy)  
- [Пожелания и предложения](#пожелания-и-предложения)

## Установка зависимостей 

Python3 предуставновлен на Ubuntu, но если что
```bash
sudo apt-get update
sudo apt-get install python3.6
```

Необходимо поставить расширение для стандатрного модуля по работе с датой и временем:
```
pip install python-dateutil
```

Также стоит поставить питону модуль `requests`:
```bash
sudo apt -y install python3-pip
pip3 install requests
```

## Использование skird.py

Скрипт создает и оформляет карточки по корпоративному стандарту

Оформить файл с необхобимыми задачами можно двумя путями:

1.1 Поместить номера сторей в текстовый файл `data/tasks.txt` (или поменяйте путь в скрипте). 
На каждой новой строчке - через `Tab` описание того, что вы нужно сделать

```txt
49.9
	Перенести инструменты проектирования на новую архитектуру
	Перевести линейку на VIPER
65.10
	Переход в вид разреза после построения разреза в разрезе
65.11
	Логика перехода в вид разреза внутри диспетчера инструментов
	Взаимодействие инструментов внутри разреза с самим разрезом
```

1.2 Создать json-файл следующего формата:
~~~json5
{
  "BUG": {
    "81.23465": [
      {
        "config": "delivery", //можно опустить, используется "delivery" по-умолчанию
        "name": "Починить всё это",
        "size": 16 //(время в часах) можно опустить, используется занчение из выбранной конфигурации
      }
    ]
  },
  "US-EN": {
    "49.9": [
      {
        "config": "delivery",
        "name": "Что-то там интересное делать",
      }
    ],
    "94.3": [
      {
        "config": "discovery",
        "name": "Декомпозиция задач по адаптации алгоритмов буфера обмена и сессий",
        "size": 4
      },
      {
        "config": "delivery",
        "name": "Реализация всего этого",
        "size": 16
      }
    ]
  }
}
~~~
Данный файл так же можно создать автоматически, вызовом менеджера задач:
```bash
python3 task_manager.py
```
Следуя его инструкциям, вы получите файл `data/tasks.json`

2. Добавить файл `env/env.json`

```json
{
 "kaiten_host": "https://kaiten.iccdev.ru", 
 "kaiten_token": "ваш-токен"
}
```

3. Запустить скрипт: 

```bash
python3 skird.py
```
Обратите внимание, что теперь скрипт может парсить командные аргументы. Запустите
```bash
python3 skird.py -h
```
чтобы получить детальную информацию.

Если Вы хотите запустить скрипт с использованием задач в виде json-файла (например, который создал `task_manager.py`),
то запустите
```bash
python3 skird.py -p "data/tasks.json"
```

4. ??????
5. Profit. Ваши задачи конвертируются в задачи delivery в бэклоге спринта с нужными:
   - названием (включает id карточки as well)
   - меткой "ГГИС"
   - типом
   - пользователем(вы) в роли "ответственного"
   - Ролью C++

### Изменение конфигурации

Поскольку теперь нам нужно заводить карточки разных типов, были добавлены конфигурационные файлы следующей структуры:

```json
{
    "board_id": 192,
    "column_id": 776,
    "lane_id": 1275,
    "size_text": "16 ч",
    "type_id": 6,
    
    "properties": { 
        "id_19": "1"
    }
}
```

`board_id` - ID доски, на которой нужно создать карточку  
`column_id` - ID колонки  
`lane_id` - Даже не спрашивайте - не помню  
`size_text` - Длительность с единицами измерения  
`type_id` - Тип карточки, очередное магическое число. 11 - Delivery, 6 - Discovery.  
`properties.id_19` - Роль. "1" - С++. Остальные не знаю, реверсите через запросы сами.  

Файл с конфигурацией должен находиться в `env/skird_config/`.  
**Чтобы поменять конфиг**, замените значение переменной в `skird.py` на имя вашей конфигурации:
```python
   config_name = 'delivery'
```

## Использование time_management.py

Скрипт автоматически списывает одинаковое время в административную карточку в течение указанного времени

1. Заполнить под свою карточку файл с настройками `env/auto_time_log.json`

```json
{
    "start_date": "2024-04-01",
    "days_count": 3,
    "time_spent": 20,
    "role_id": 3,
    "card_id": 24411
}
```
`start_date` - Дата начала списаний YYYY-MM-DD  
`days_count` - Количество дней в течение которых списывать, включая выходные.
В сами выходные время списано не будет, просто оффсет по дням так работает  
`time_spent` - Время списаний в минутах  
`role_id` - Идентификатор роли, 3 - разработчик, остальные потом пронумерую  
`card_id` - ID карточки, в которую списать время. В данном случае - это дейлики  

2. Запустить скрипт:

```bash
python3 time_management.py
```

3. Принять всю ответственность на себя и ввести любой бред, начинающийся на `y` или `Y`.

4. ????????

5. Profit

## Пожелания и предложения

Оформляйте пожелания в `Issues`, отправляйте предложения через `Pull requests`
