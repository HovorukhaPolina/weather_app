# WEATHER APPLICATION

![](static/icon/big%20screen.png)

_Цей проект розроблено з метою ознайомлення із роботою API, принципом отримання даних від віддаленого серверу, вмінням їх обробляти, структурувати та застосовувати у своємо проєкті. А саме застосовувалось API такого веб-ресурсу як [OpenWeatherMap](https://openweathermap.org). Проєкт допоможе розібратися із роботою файлів JSON, як правильно отримувати та зберігати дані у файлах з типом .json. Та познайомити користувача з інтерфейсом застосунку розробленим за допомогою пакету [CustomTkinter](https://customtkinter.tomschimansky.com)_

---

### Зміст:
- [Основні модулі проєкту](#modules)
- [Розгортання проєкту](#download)
- [Створення віртуального оточення проєкту](#venv)
- [Завантаження модулей до віртуального оточення](#download-venv)
- [Старт проєкту](#start-project)
- [Основні механіки проєкту](#mechanics)
- [Кусочки нашого коду](#code-pieces)
- [Висновок по проєкту](#result)

### Modules:
Всі модулі
1. [customtkinter](https://customtkinter.tomschimansky.com)
2. [json](https://docs.python.org/3/library/json.html)
3. [colorama](https://pypi.org/project/colorama/)
4. [os](https://docs.python.org/uk/3.13/library/os.html)
5. [requests](https://pypi.org/project/requests/)
6. [pillow](https://pypi.org/project/pillow/)
7. [datetime](https://docs.python.org/3/library/datetime.html)


### Download:
Завантаження проєкту
- ##### Git clone:

    - Отримати посилання для клонування проєкту

    ![](static/icon/clone_link.png)

    - Відкрити VSCode --> у Explorer VSCode відкрити папку для збереження склонованого проєкту --> та у Terminal прописати команду: 
    - `git clone https://github.com/WorldIT-academy/WeatherApp.git`

    ![](static/icon/clone_command.png)

    - Відкрити каталог, який ви склонували у Explorer VSCode
    
- ##### Download ZIP

### Venv:
Створення віртуального оточення

---
### Windows:

1. >git clone 
2. >cd WeatherApp
3. >python -m venv venv
4. >open command prompt terminal (cmd) 
5. > venv\scripts\activate.bat
6. >pip install -r requirements.txt
7. > python main.py
  
### Mac:

1. >git clone 
2. >cd WeatherApp
3. >python3 -m venv venv
4. >open git-bash terminal 
5. > source venv/bin/activate
6. >pip3 install -r requirements.txt
7. > python3 main.py

---

### Download venv:
Завантаження модулів до venv

1. > pip install customtkinter
2. > pip install json
3. > pip install colorama
4. > pip install os
5. > pip install requests
6. > pip install pillow
7. > pip install datetime

### Start project:
Cтарт проєкту

1. >Запускаємо проект через main.py після того як встановили усе що треба для запуску
1. >Вводимо своє ім'я та назву свого міста
2. >Після того як ввели усі дані, можна заходити у нашу програму кожен день, коли ви цікавитесь який прогноз буде сьогодні!

### Mechanics:
Основні механіки проєкту

---

## Code Pieces:

1. __weather_data:__

```python
import requests
from .write_json import write
# requests - модуль для відправки запитів
# get - отримує дані за посиланням
api_key = "f45b384d06f8d6558d2f50d57cd3220f"

def get_info_weather(city_name: str, file_name: str, forecast: bool):
    if forecast:
        url = f'https://api.openweathermap.org/data/2.5/forecast?q={city_name}&appid={api_key}&lang=ua&units=metric'
    else:
        url = f'https://api.openweathermap.org/data/2.5/weather?q={city_name}&appid={api_key}&lang=ua&units=metric'

    print(city_name)
    data = requests.get(url)
    if data.status_code == 200:
        write(file_name, data.json())
    else:
        print("Error - ",data.status_code)

```
* >Цей код містить підключення API ключа, який буде надавати інформацію щодо погоди до нашої програми.

2. __city_weather:__

```python
import customtkinter as ctk
from .side_bar import menu
from ..read_json import *
from ..weather_data import get_info_weather
from datetime import datetime, timezone, timedelta

list_city = read('config.json')["list_city"]

class CityFrame(ctk.CTkFrame):
    def __init__(self, city_name: str, master_ch: ctk.CTkScrollableFrame):
        ctk.CTkFrame.__init__(
            self,
            master = master_ch,
            width = 236,
            height = 100,
            border_width = 2,
            border_color = "#b5d3d9",
            corner_radius = 20,
            fg_color = "#096C82"
        ) 
        self.pack(pady = 20)
        get_info_weather(city_name, "weather_data.json", False)
        self.DATA = read("weather_data.json")
        temp = round(self.DATA['main']['temp'])
        temp_min = round(self.DATA['main']['temp_min'])
        temp_max = round(self.DATA['main']['temp_max'])

        timez = timedelta(seconds=self.DATA['timezone'])
        time = (datetime.now(timezone.utc) + timez).strftime("%H:%M")
        
        description = self.DATA['weather'][0]['description'].capitalize()
        # CTkLabel - создает текст
        self.TEMP = ctk.CTkLabel(
            text = f"{temp}°",
            master = self,
            font = ("Roboto Slab", 50, "bold"),
            text_color = "#FFFFFF"
        )
        self.TEMP.place(x = 230, y = 5, anchor = "ne")
        self.CITY = ctk.CTkLabel(
            master = self,
            font = ("Roboto Slab", 16, "bold"),
            text = str(self.DATA["name"]),
            text_color = '#FFFFFF'
        )
        self.CITY.place(x = 15, y = 12)
        self.TIME = ctk.CTkLabel(
            master = self,
            font = ("Roboto Slab", 12, "bold"),
            text = time,
            text_color = '#FFFFFF',
            height = 18
        )
        self.TIME.place(x = 15, y = 33)
        self.DESC = ctk.CTkLabel(
            master = self,
            font = ("Roboto Slab", 12),
            text = description,
            text_color = '#b5d3d9'
        )
        self.DESC.place(x = 15, y = 65)
        self.TEMP_MIN_MAX = ctk.CTkLabel(
            master = self,
            font = ("Roboto Slab", 12),
            text = f"макс: {temp_max}°, мін: {temp_min}°",
            text_color = '#b5d3d9'
        )
        self.TEMP_MIN_MAX.place(x = 225, y = 65, anchor = "ne")

for city_name in list_city:
    city = CityFrame(city_name, menu)

```
* >Цей код відповідає за певну температуру по містам

3. __forecast_frame:__

```python
import customtkinter as ctk
from .main_screen import app


class FrameForecast(ctk.CTkScrollableFrame):
    def __init__(self, master: ctk.CTk):
        ctk.CTkScrollableFrame.__init__(
            self, 
            master,
            width = 800,
            height = 240,
            fg_color = "#5DA7B1",
            border_width = 5,
            border_color = "white",
            corner_radius = 20,
            orientation = "horizontal"
        )
        self.place(x = 325, y = 430)

forecast = FrameForecast(app)

```
* >Цей код відповідає за задній фрейм

4. __main_screen:__

```python
import customtkinter as ctk
from ..read_json import *

config = read('config.json')
#Створюємо клас для додатку
class App(ctk.CTk):
    def __init__(self, name: str, width: int, height: int):
        ctk.CTk.__init__(
            self = self,
            fg_color = config['color']
        )
        #Задаємо назву додатку
        self.title(string = name)

        # winfo_screenwidth, winfo_screenheight - отримують розмір екрану
        screen_width = self.winfo_screenwidth()
        screen_height = self.winfo_screenheight()

        x = int((screen_width - width) / 2)
        y = int((screen_height - height) / 2)

        #Задаєемо розмір вікна
        self.geometry(f'{width}x{height}+{x}+{y}')
        
app = App(name = config['name'], width = config['width'], height = config['height'])

```
* >Цей код відповідає за розміри вікна нашого додатку

5. __side_bar:__

```python

import customtkinter as ctk
from .main_screen import app

# master - параметр який відповідає за батьківський елемент (той до якого кріпиться)
# CTkScrollableFrame - створює елемент з прокруткою
class VerticalMenu(ctk.CTkScrollableFrame):
    def __init__ (self, master_ch: ctk.CTk, width_ch: int, height_ch: int, **kwargs):
        ctk.CTkScrollableFrame.__init__(
            self = self,
            master = master_ch,
            width = width_ch,
            height = height_ch,
            fg_color = "#096C82",
            **kwargs
        )
        self.grid(row=0, column=0)
        # grid - розміщює елемент за сіткою (row, column)
        # self.pack(padx=10, pady=10)
        # pack - розміщює елемент за отступами (padx, pady)
        # self.place(x=0, y=0)
        # place - розміщює елемент за координатами

menu = VerticalMenu(app, 275, 800)

# **kwargs - дозволяє приймати будь які параметри
# *args - дозволяє приймати параметри без імен

```
* >Цей код відповідає за прокрутку по вертикалі


### Result:
Висновок

__Коли ми працювали над створенням цієї програми, ми здобули багато нового досвіду і навчилися розробляти власну програму. Ми освоїли підключення API коду, створення її структури, механіки погоди та багато іншого. За останній навчальний рік ми реалізували чимало ідей та освоїли навички, яких раніше не мали. Ми доклали максимум зусиль, щоб створити справді круту, працюючу програму. Ми щиро вдячні Миколі, по-перше, за усі витрачені нерви на нас, особливо отриманні нам знання!__ 

/

__When creating this program we got many new expirience and leaned how to create own program and how to connect API code, how to make structure to our program, how to do a weather mechanics. Created really many things for the past year, and learnt many things too. Our team was trying our best to make a really cool program, which would work without any mistakes. Saying thanks to Mykola at first for all lost nerves on us, especially for the knowledge given to us!__