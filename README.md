[Yandex Prilavok chek-list](https://docs.google.com/spreadsheets/d/1PW2svQP87FNOJBGYqrKUbZXmw52_EChXFNTQU2PYL8s/edit?usp=sharing)

[Yandex Metro 01](https://app.qase.io/public/report/ffea3a93a97e2a4b594a2679c80b7d7b7a7e36d5)

[Yandex Metro 02](https://app.qase.io/public/report/229641cbf9e0cc8168b3f54d140ccbf776174135)

[Aero Taxi](https://app.qase.io/public/report/512a4f0f4c1de559700ada99fbb564f08e8fb7a6)

[Car-sharing | Mozila Firefox 1920x1080](https://app.qase.io/public/report/537fa082f5a4e60f5dd7493466e67c03d8698592)

[Car-sharing | Yandex Browser 800x600](https://app.qase.io/public/report/8ed493303ac295c261dfc505eab15a02cb6b97b3)

[Yandex Maps](https://app.qase.io/public/report/9858e8107818964c6b008e9e4b308e6006e5aafa)

[Car-sharing](https://app.qase.io/public/report/5cef8856071e81143bf995ec13e6994be04ae068)

# CV Dmitry Volkov

[Dmitry Volkov Fullstack QA Engineer.pdf](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Dmitry_Volkov_Fullstack_QA_Engineer.pdf)

https://github.com/GorgeousTV

![Dmitry Volkov Fullstack QA Engineer.jpg](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Dmitry_Volkov_Fullstack_QA_Engineer.jpg)

# Portfolio

### Яндекс.Прилавок (Automation testing) | Pycharm, Python, Pytest

<aside>
💡 Тесты на проверку параметра 'name' при создании набора пользователя в Яндекс.Прилавок с помощью API Яндекс.Прилавка (Apidoc)

</aside>

> Конфигурация
> 

[configuration.py](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/configuration.py)

```python
URL_SERVICE = "https://55324992-6e7b-4b1e-927e-dbb3f5a6e059.serverhub.praktikum-services.ru"
DOC_PATH = "/docs/"
CREATE_USER_PATH = "/api/v1/users/"
CREATE_KIT_PATH = "/api/v1/kits"
```

> Данные для тела и заголовка запросов
> 

[data.py](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/data.py)

```python
headers = {
    "Content-Type": "application/json"
}

HEADERS_KIT = {
    "Content-Type": "application/json",
    "Authorization": "Bearer jknnFApafP4awfAIFfafam2fma"
}

USER_BODY = {
    "firstName": "Анатолий",
    "phone": "+79995553322",
    "address": "г. Москва, ул. Пушкина, д. 10"
}

KIT_BODY = {
       "name": "Мой набор"
}
```

> Отправка запросов
> 

[send_request.py](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/send_request.py)

```python
import requests
import configuration
import data

def create_token():
    response = requests.post(configuration.URL_SERVICE + configuration.CREATE_USER_PATH, json=data.USER_BODY, headers=data.headers)
    authToken = 'Bearer ' + response.json()["authToken"]
    return authToken

def create_headers(key, value):
    token = data.HEADERS_KIT.copy()
    token[key] = value
    return token

def create_kit(body):
    return requests.post(configuration.URL_SERVICE + configuration.CREATE_KIT_PATH,
                             headers=create_headers('Authorization', create_token()),
                             json=body)

def get_kit_body():
    response = requests.post(configuration.URL_SERVICE + configuration.CREATE_KIT_PATH, headers = data.HEADERS_KIT, json=data.KIT_BODY)
    return response.json()
```

> Тесты с проверкой кода и тела ответа
> 

[test_create_new_kits.py](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/test_create_new_kits.py)

```python
import data
import send_request

def modify_create_name_kit(key, value):
    body = data.KIT_BODY.copy()
    body[key] = value
    return body

def positive_assert(key, value):
    kit_body = modify_create_name_kit(key, value)
    kit_response = send_request.create_kit(kit_body)

    assert kit_response.status_code == 201
    assert kit_response.json()['name'] == value

def negative_assert(key, value):
    kit_body = modify_create_name_kit(key, value)
    response = send_request.create_kit(kit_body)
    assert response.status_code == 400
    assert response.json()['code'] == 400
    assert response.json()['message'] == "Не все необходимые параметры были переданы"

def no_name_assert():
    body = data.KIT_BODY.copy()
    body.pop('name')
    return body

def negative_assert_no_name_kit():
    kit_body = no_name_assert()
    response = send_request.create_kit(kit_body)
    assert response.status_code == 400
    assert response.json()['message'] == "Не все необходимые параметры были переданы"

def test_create_kit_with_name_one_symbol():
    positive_assert('name', 'N')

def test_create_kit_with_name_max_symbol():
    positive_assert('name', 'AbcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdAbcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabC')

def test_create_kit_empty_name_symbol():
    negative_assert('name', '')

def test_create_kit_max_plus_one_more_name_symbol():
    negative_assert('name', 'AbcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdAbcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcD')

def test_create_kit_eng_symbol():
    positive_assert('name', 'QWErty')

def test_create_kit_rus_symbol():
    positive_assert('name', 'Мария')

def test_create_kit_with_spec_symbol():
    positive_assert('name', '"№%@",')

def test_create_kit_with_spaces():
    positive_assert('name', ' Человек и КО ')

def test_create_kit_with_numbers():
    positive_assert('name', '123')

def test_create_kit_with_type_int_key_value():
    negative_assert('name', 123)

def test_create_kit_no_name():
    negative_assert_no_name_kit()
```

### Яндекс.Прилавок (API testing) | JSON-file (Postman collection), Cheklist, Bug-reports

<aside>
💡 В этом проекте я тестировал API веб-приложения Яндекс.Прилавок. 
Я визуализировал схему взаимодействия клиент-сервер-БД, написал чек-лист, создал коллекцию проверок в Postman. По ходу проверок также пользовался встроенными автотестами.

</aside>

> Схема клиент-сервер-БД
> 

![2POST API создание набора.png](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/2POST_API_%25D1%2581%25D0%25BE%25D0%25B7%25D0%25B4%25D0%25B0%25D0%25BD%25D0%25B8%25D0%25B5_%25D0%25BD%25D0%25B0%25D0%25B1%25D0%25BE%25D1%2580%25D0%25B0.png)

> JSON-файл с коллекцией в Postman
> 

[PRILAVKA.json](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/PRILAVKA.json)

> Примеры чек-листа для тестирования API
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled.png)

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%201.png)

> Список багов
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%202.png)

> Пример баг-репорта #1
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%203.png)

> Пример баг-репорта #2
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%204.png)

> Диаграмма найденных багов по приоритету
> 

![chart (8).png](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/chart_(8).png)

### Сарафан доставка | Cheklist, Bug-reports, Equivalence coefficients & Boundary values

<aside>
💡 Командный проект. Мы с командой из 5 тестировщиков работали над приложением доставки посылок (от продуктов и документов до бандеролей и лекарств). В мои обязанности входило составить матрицу поддерживаемых окружений, составление Mind-map, написание чек-листа на функциональность сайта: Хедер (до и после авторизации), футер, раздел с отслеживанием заказа и поле с формой для ввода трек-номера отслеживаемого заказа.
Чек-лист разрабатывался в условии малого количества документации от заказчика и без четко структурированных требований.

</aside>

> МПО
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%205.png)

> Mind-map приложения
> 

https://www.figma.com/board/VffIgd9s12GlNVhCHyQJ39/%D0%A1%D0%B0%D1%80%D0%B0%D1%84%D0%B0%D0%BD.-%D0%94%D0%BE%D1%81%D1%82%D0%B0%D0%B2%D0%BA%D0%B0-%D0%BF%D0%BE%D1%81%D1%8B%D0%BB%D0%BE%D0%BA?node-id=0-1&t=R3uwuNsVy7ngD3G8-0

[https://www.figma.com/board/VffIgd9s12GlNVhCHyQJ39/Untitled?node-id=0-1&t=R3uwuNsVy7ngD3G8-0](https://www.figma.com/board/VffIgd9s12GlNVhCHyQJ39/Untitled?node-id=0-1&t=R3uwuNsVy7ngD3G8-0)

> Чек-лист проверок
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%206.png)

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%207.png)

> КЭ/ГЗ
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%208.png)

> Баг-репорты
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%209.png)

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2010.png)

### Test-Lead project | Task Table, Cheklist, Bug-reports, Test Artifacts

<aside>
💡 В этом проекте мне представилась возможность побыть в роли тест-лида, что позволило мне глубже понять специфику работы и лучше погрузиться в рабочую атмосферу. 
Нашей командой за короткий срок было протестировано 3 веб-приложения: «Stellar Burger» (заказ еды), «Продуктовый помощник» (сайт с рецептами) и «Kittygramm» (соцсеть для котов).
Весь проект мы сдали раньше дедлайна благодаря грамотному распределению задач и эффективной коммуникации между участниками. Если кто-то обращался ко мне с вопросом, то мы легко решали возникшую проблему.
Из трех проектов себе в работу я взял Kittygramm, составил для него mind-map, чек-лист с проверками и таблицу КЭ/ГЗ. Также помогал участникам команды составлять таблицы принятия решений и другие артефакты тестирования.

</aside>

> Mind-map для Kittygramm
> 

[Mind-map приложения Kittygramm](https://viewer.diagrams.net/?tags=%7B%7D&highlight=0000ff&edit=_blank&layers=1&nav=1&title=Киттиграмм.drawio#R7T3ZcuO4td%2BSh67KPMhFAFwfJS%2BTVKaTqTupuneeUmybbSsjS44sd7fz9ZcUSfBgk0FtBwQ1U6W2SHDR2Tec84ldP%2F%2F4eZ2%2FPH1ePRSLTzR4%2BPGJ3XyiNI6jrPynOvJeHwmzOKmPPK7nD%2FUx0h34bf7fojkYNEff5g%2FFq7Bws1otNvMX8eD9arks7jfCsXy9Xn0Xl31dLcSnvuSPhXLgt%2Ft8oR793%2FnD5qk5SuKsO%2FGXYv741Dw6pc3ve87bxc0veX3KH1bfwSF2%2B4ldr1erTf3X84%2FrYlFBr4VLfd2d4Sx%2FsXWx3NhcsPrXr7PFzc%2Bvn38J%2FvE5evvl94fXv09YfZdv%2BeKt%2BcHNy27eWwgUDyVAmq%2Br9eZp9bha5ovb7uhsvXpbPhTVY4LyW7fml9XqpTxIyoP%2FLjab9wa7%2BdtmVR562jwvmrP3b%2Btv2%2BurL%2FULVE81%2FtDm0OvqbX1f7Ph1LcHk68dis2Md5egoCblYPReb9Xt53bpY5Jv5N%2FE98oagHvm6DublHw3Ye6AgcgIFCFAPMaFOFKj%2F7a%2F%2F%2FOfvP%2F%2FP9PNnBf4ddCtQfX%2Bab4rfXvItEL6Xck%2BE5Nf5YnG9WqzW22tZQR6iIimPv27Wqz8KcKYUhSyPyzP5%2Br7BSxRwVHwr1pvix25kqMBrLoiCRui0Yrf9%2Fh3IsFYwPQHxFQcngnfiG5VTSyqPUak8cEPAI8CdBKhSndB4rJDHpXh6kTQYcC9VzFjhblDFZ4K7CvUSdNms%2Bpzdbj%2FZ9jPdfk63n4GCmcFZOMw5C4dGGlwMmwNCSw6gES4PROF4Ic9wIe%2BdlWMP%2BQgX8ul4IZ%2BgQj72ztKxh3yGCXkWjJbmWYBK8xr9WlmZtU1586mESro9Mou2fwfbz7A9Un5m2yNEwdYHlqZolh5idx7J7CTuRdYy33gituSJFJMjNHZPCbopaXmh5oKtr9XwSO2Dlcfj1geDCyhgoltwec1iSbuSX542n8dkqIe8SL%2Fe6xgqvk%2BLL19P47lxzkDjIKImAwbOQqklC5FAj6pzxeoucEeBu3fusjXcUb1l4p2zbA13VF%2BZeOc2WMMd1WvQgL2ykULFyLkWLaJ6wWy74M7aTAKWVWcyDddMIs6ZSaF3ueS2yOzjUhWKyUehfyVC1oBHLRIKvcsl2wMeNZscehfUsAc8aliDaGLbVaTvDuSTZ01cr1I%2B8X%2FeqvrVGQgHXrd6m587SPkeQZVOQlGVcrWJlyv2zvMlzJK%2BTbg6U%2FLGPxtmIID3LuYwFMD7ZzwOBPDeRXuGAnjvwj32gEeN97DxWjUMleLZeJUrQ43os%2FHKeIYa0mfjlfEMtxLIu%2BI3e8CjFr9F4%2FVc68J6NMCP14GKUKPvkX9BYGvA4waBNaLGOgh8B0rVAvGC8si1w2Fh%2FFJO5p9yjSwpnqIqV6KR8RUxT0HxZk3Gd%2BI2Opn6KViagVqF1CG6lysL8Ok%2B8q4AkyS2kh63ElCT4Lak%2B%2B5gBGR9%2FXcELgzFS27A1%2B2OAKt66fKTtZfwB8GtraZbHbde%2BgTM50AuUsN8FayIRnlXnymAZ9SidQYATsGCmxY1nHikC0mD3G5BjUF8MVkyh4gqxtBRpWdXXj8HDbMO%2Btdm6I8BbWGCjjaNI2MvPjWCk0u%2BGUA8BZhj28%2FbvaRsnD9XOFp%2BeX1x0VqJ0K0VphOYUNHUe9%2F4Pjh8GE6UbgnoPMH0PmZTtnsDiLSVP53NwSXPTbuAUzdzVvFPQuYcDnReD5f7UDpBUHLNwVXLTUPmnYbgN%2BElYVxAQYPCQc7AV%2FJsp5JPRcGtKn%2BIFc4KvIqel9ZLt6r0SmqNOv636qahs5UDCDSoe4ioqQHQR0AjCwaJxyh2DY8h1eMR2k5MQUQGQD900zlCR4Gu8YDbppYMQ4pur4b6veozGM6vgZgoDgmkYJX601YqYQOdEdcIV7%2F37fRKnIfJbIS%2F2gcOHXP47JJ4l%2B9tueHDYHSCmu9Nxwv4FDfRrt%2F007uZCW5wvv8u3a47EIlOE6yaoEf3dU0pu2jvraJwps1xCndRm3AbGXCbKtl%2F1XZQgwa1HwKjPLAFDnRXYvAmNyIFQQ2543HYak%2F2dvCDQZF3e10j2%2BZqEepeV13zUpkFIBOp5nmgcKtk3c8AI0hcA%2B1KXX1P7dKi80ucOccvZq%2BqL9rUnLi0Xr2DAyJMRkmE7nTF3pWRRLaNXmLUMpLYu9pke8BTVN1hCDP0qt%2B5FU5BPSIsM5V0OhD%2BkQo5oxBdEnlnTMW2rmyMakzpejN3%2FspMoHGNi2MKgMKqChWT548xS95DjE%2Fw3sVuYmpL8Kixm%2FY1exB86wcMl%2F7lpL0D9G924naozmvR5J8KmSxHYZ2k6LDemc%2FaDWsLo6emd3S4yzIeH%2B6Jd0ZNYhshSlCNmsS7jXD2gEfdCJeNF%2FAZKuATvTr9oHXsufNP%2FTNOopoA%2BSd6ovwT1yJoWqMd%2BuERD1kPqEANyiV6MwmxleMp6F0kd%2Fwmyal3odDENhRaeyto5K4PhTq1af0EDJC5xwGGEFwKMvNwexoFahru%2FuC7l0xKX41gHCXt2Z6yFIhS4QLiNJ%2FTi1f8neJtREQQr%2FGiQsWXdfnXY42U%2BsjrS74U4Nli7b42zaaVcHr88ufyZavoQPvPTzVev66Wm8nX%2FHm%2BeK%2BX%2FqVYfCs28%2FscnH%2FdArw6S%2BjLD3iifmh1ZrlaP%2BcLcO5bvp7n5b%2BL%2BWO%2BeVsXrx%2Bsu89fTEu%2BN0CvTlbY2Z5ZlKqhWE%2FKn38%2FXz6qV67WL0%2F5srllTSpBSSSbSV6%2B0bI%2BfF%2BSSLEG5%2BYlpS2bJwXtT92e2azLm30t798%2BaVnUZ0sqWX0XH%2FN9tX4QX4zfq%2FwtX%2F6Yl7er7lmb0JOGzoR1X%2FL7Px63lD%2BR8LgdQ1uhEP7xE3jTh%2BJ%2BtS510Wo52TzN7%2F9YFq%2FN682X8828hY%2B8FuBy5zrwOsK6r4tVvpGB8zB%2FfVnk7%2B3yxbw8QYM%2FzZ9fSnWfLxs65mKmoezhC9Ieqva4orWWCaXUqcWCKCoe5t%2FkQ5U8uZMPloeEpY7Ja9kacEBgG0ZHOtthwhkdfqSS1Il7Nakk8C4mkNlPfkMNCmT7F4yIYnx3YbAp6LajMJivvAbXOhx6UPkKP9bGX0nvBO%2FRaWLIAvRYiJYzoE4g2rvMBAmotQjF7Y6om5Fzgf2ZYO9fp3N72CNPL%2FVvjGYf2KN23eYvKidEYUsWyWLrp0WPmMs8kuMiq138ql7i38DqRp5bcYABXefiAEN%2BZwoiVGnDAfqWaXAHIQNWKUxP3B23%2F9kJdphKIR780seGMI7odASnbG%2FnNjoT9E1UxL8p5cS67zpBHlRu6rxuL%2BR2BHJaD10M5IRgKVH4LRUCM8IzoPAMThCnORKHhcQ9gWnoNn7OKA0vOr8F0Rh127yU4eH10rxOHYZ3ErAm3UksbpOIA0KY%2BhdjsG6JTgjuLACqsWcusD8T7L0rn%2BsB%2B%2Fq34hkf%2Bw8DUIZg8B1Mku5y1UqQgw1p22od0UrQtxHdBV0xv9Koeabs7KMKQrP2WrUCJAW2gnPJbSqhLcM37lqBdUnBHZE7M%2BfQrGsCP3BFRa1DgQy1hoHoesePB%2Fa4RoKuZ%2Fx4YI%2B61Z%2FoOsOPB%2FaoW3CJrhP1aGAf4iaedQ2sxwN73Ig0NaTddiRouE1519N%2BPTw22D%2BLfQTrNJKcEPwtWEQ3cQqBY%2B7f1t84boof883%2FVfe6SqLm6%2B%2FNrau%2Fb37AL%2B%2FNlyPyHLXlOHpoiVNz6a%2BrefmOnRMTiWRSOjVXAfiPijesf09zj44elNuGaXjFooz%2Fl4i0mNArEncPYeJDamAoD9kSHf%2Flh1gs%2BnrU45WtHL3vCktFLBH8XAAzRBS8HE4bU%2BfgH%2Frn6jPbNiAkRHb1998%2B5HKa%2BxiMIs29wjc6dHOvhs4ntq0%2BCMOti2aGZh%2F7525UHaFmFzgXTZXqAD6g0UnmYYlzzLOzfcUY0gYnQCvvRYFoPDiwI2u4tT4n0JMu0MTOKr8DIX6UgY5OxFEm%2BLPAmuibCVW3opLTdjTgfQzuAMPxXbJSxMwtcZrEDiLE6RyIhT%2BVWmPhwHiTjD2aSUgxRJiOFvwxzEC0HSvGeWervnZomOrz2hGWyRyUYZEb8QtDMJjCYHB7Sh8MLnGwfq%2Bvitqvv8Nz3WXbb5hB5OjQlJmeqSdt7bjcFbpv2FjpL00CKt3KEByertf5O1j2Ui14Nb%2ByPOEzbB5sejNllusH65WhxT3XZ9Hu9QqkpAvKP2qQHFd6Rv4VWkTW23Ai3EIL%2Fxpu94A9bstt%2FqJHGEZ6aqe3n63h3g7rCZWTLVwHIForThv4%2B%2FGedbYFdxIl0Y0Tu8D%2BPLDXTa4aD%2Bxxdx35NzhyQHRPL7BHg71%2FhfTDgb2H%2Fp017HFn5hH%2FxnP2gT2yjbnXaB%2Fuu81Ef42CT203YNXjuxH2Tw6nS8FEzm674K2lHkox65qfFFeKpR5KMXvY40qx1MMIoT3scSOEmYfemjXsM9x63MxDj8Ee9hTXcjriQLcBNvKaxIbEHZ75Q%2F0bZNCE4izYgeJOMqC6lrgX2J8J9h6qgYHAnr%2FoXmqACZ4yVccwxcBr3jHqadiag%2FAaEDzP2b9p6k1Uz6rGADn2athVBje%2FEEDhvKIgBuGmSCT%2B4cSRIuKaHUUS%2F%2Byo2NqtSHD1SWxwKyAzwNp1HlHdtuCuPjNQtZ61rELAhalSjsPbF8OwLwFskwJtJOmh%2BimOMxUh%2BIW%2FiX%2FB2VZvW3AVanCW6prijwX2zdxgPNj75530gD1qkIrqepuPB%2FbIMse%2FPllDgT1%2FUcGKgn13odEjWkKd6aPud9rDPNq66V3%2B3LlmrqqpxOfMI5pKeo9QqCxIAFRhsORGhyPYSERyK2EBu7qYT9nmFjPca9pOyYbHXcNvLDW6IiHDx68qGitFGYjY4EhWa0R2OP98x%2FVwQwApfkPl1EOD2brJY4obAkg9NJjtYY9qMPMXPXQi9xQGLCNFrkHzgGscZwP2ysYkB0YupwYL70O4c4OsVRr99prB9jxD0C3yKA3iQHellOqZ7DKi0AKhvIeRSwjV2eucjxiAObSh0y4t2tniDCxoLXXXUJBJTYxS%2FCZGqT5t0LVY5GIP9gvj1rPky0A9x%2FGjzWg7hhkSuFiVbbApoMyB4qtNqnjDLwpWHGCYTGc%2FAG2%2BLWgSoz7lIe59EoCRayU2AWcbq7Y7Njpk8eUCj2T%2BBaqz5hdYVLLiBksz%2FwLVw4G9f3VHw4G9f7tGesAeedeIzgUs9WooKOFW7w7G25ZLG1P8XEpmyKXM2pIhbkrOdFYmbDIMF0O32Zz4cg0%2FqTxb2gH8GLwDmHnipuht6yzsCFwkOmwEYKroHeArbb7lWozDwM7FWv50DMkyE%2BIjmbedlMWd5DSoyUSY64J%2BOigp73AOC%2FvaSJhryEnldCR%2B8TdPeJ8k3QzDJqni07uHIKUeAD9fzH%2BGP5ZaUxhmU0pDkcv3dJbasZhjjzkwMDYJ3yFS7tmrhtm9WKbSYMIBRiQGYxIaBFNgN8wAmmBGhwLs8F1M0C7hAhIapTz95himInlKGUOvNqdEE1joWDUYKaZCOXnmAqbcCEN0PcAH1QK8T01qcqAibS6VO1IzFl0FYiNwQhOJXqw7gYeBejdOpWeaFEmpdyVIvIbFwuCiqAYX9S4V0gf2uDX71LtwfB%2FYo24Dpo5MSUaCPWo4nr%2BovnxELH78wA9j4rJ9t0y4Zjkq3liEH7dqGWSPYlXB%2FA%2FADgcJQXyTTH%2B%2FQQhZSyQU588VApdfXl9cLHhVkO1ACJkaauxgDddUwCLf77KrgEvc5rJ3mkGfWoCUdCcmEoZcQBOHoXuywFAAKGxXmgiA3gcTnD4aCuO10F2RVBulcghhSkw7Rt9hQ5l%2FMW1qu8uDMtyYNvPQvbWHPbJ7u%2F8Om05a9dpX45gwkieeuaA9mMH%2B3zuXYoksZTOOEGx2HXEOWIXM4AIcMtD6RvEO2mUyWgMXJnmdGu9ytbQL7OpfmIrZdqujDDdMxfwLU%2FWAPW6YihmmVVymEZ4gVercJhHK3MiM7Jv1FEYu2%2FOvBVcemjTRpyLlBt%2BhhFjblCYx7jc6VzqT6YMjUygroK26I5wpWkxyhIQpATIYNbffeaw1op2QVGeXO8QBuWNwWaWAKsQADJercVde2829oJkYR4O45d1vAtBESizO3wftpk0VqXArx6iDyIE8B7RS6EYgT9AuXTFP1n7dVvNcBUG6u6Kn%2FPJrsZ6XsCnWn5QqnzAKocabBFdBGH6g9rbf5HseU%2F2Fhwa09GqLpeyKBhn%2FLxUoL5QJylYbJiy7agdVNPeKaHYVUSuVWM%2B275a9VAted%2F4I8bWbjbamtwuz4KD1UZPFN62Po3DX%2BvKP%2BhceV%2F%2BHGr9h4D5bSK3Zgx3IHgeKR%2B92F%2FeBfYQK%2B0gT1h0P7DNcutdHZjN7Iw0WaiiuyeADDLHUmMqBsGqoSQtWbiJTLPOZaMBLmY%2FepvifgacJt%2B%2BlrQ94o2yI5ivhffdwAIOf%2BpLSqQlD6YaJPhCDhhoHcOhyNLKWo4eWzR8Ie03QryNf%2B46MGYzZQPc8dLfzYuqgjDTsK1K3A0pBLtJOJtGG1UbazU9GsAMZ5VCT3xKyyJIqnIqxI74SVgZL%2BDiDovRbRcapcypSN8Rr4Coyso5Axbgutm5m1Hhgj%2BtiJxo3bzSwTwxi6lyw96%2BMM7EuxcCGvRu5eCS6x92l2L6oHKdIlaSjKY1XPmX%2B8lphoF%2FcqCAPUZHo4kZZnLA83mH7BMcxfUhERduHamyfVGP6pCczfXQd1obOCsyWFTJc08e%2F3qV9YI9r%2Bug6140H9qhRMaZrKDce2KNmdlgwYnnPAlR5z4IRy3sWIGeTDemxPaqvYTAR1kxqb8UvT9tYnzfWLA2kZFek2Zh6VmuW6fr9DZ3DbHNdjKA61UzXXm88sMfVLLqGeeOBPe6eqkif4%2B2tWWC2UEroiAXTI9QsujFh542T6GYrDp3DEksOowluFYVuXOJ4YI9bCagbdzga2Ke4aQrdZMLxwB43NqubSTge2OP66rqZg%2BOBPa6u1Y0WHA%2FscXWtbq7UaGCf4eraSF8aeqodD8P1zZQcNnrUj8Ye8k1myzcxctTPv44y1rBnBLWjDPOw0b097Ovfigd7D0vI7GGPWkLGdF0YL7A%2FE%2Bwv8h4N9rpGlxfYnwn2I7YxGUP2zXS7utzsnLdfY6LheoOd99d4gyzDrgHR9VkcOKfGtsX9jOHWIeh61Y0H9qhRc6Zr9DYe2ONGzdsXlaOH7owgGrCKSRPHVAxvLuoTq1kXsMe4SZJYX8ibqZ1MVfuL23S0%2B6M7G4qb%2B6%2FBTVRetLT7Du11hM9%2BhMUi%2BxH8Sc6xh6rOuqVNjKzq9OWO2a3BDYMqijfUUP2rHr1w9pi7rmXMfRvnuMeisToZGp1FDflUAshhBpoDZ62HvmMknG9omuA3zIkNoRU%2BII%2Fz9I2IrKHjQtZqDuAiMTgRqSjCuLCUyg5oiyBlUo6wTGq7DeUjjHQxgHRvTJkocA%2Fp%2FkVMztiKorlUntkQSczN2yz0nv4g30m%2B0amHPyT6oTH9ZMIOaUD7yQHvJIADllKit5Sm0NtUB1oj9%2BB1AJVMqkNzYCxqoremzuUbHd7oEHpOHrlHqXOUkh5m612DVqJw9o8aWpbCydKdJX8rA5eLIy8VQjO1lU69JSLJEkgcICJdKFTbiCACGDLoCSHGMrPqNxznzxXall9eX7xAMQvdQ7Eh2pYCMXCnMDdU8wHQNKkoJMBkpib4AhfUf4eK%2FdF7KLGDiJZ42YEWtKnOCuTSPQLW3n4okwZ0SRQi2QMxWKxNLnpsVspBOhdoQ29W7p8b9hh9TLL1MoqOvkxv601jYHtLHeW9RE1M3UONwYL6MPwNTV7LnLOXOE3dc60yncm0O7EkZxhhjxW121ckClxJ%2B8KwWSY%2BmF8l6WNp%2FKZWrPN73omGXfX2f5tvNu%2BP6%2Fz5%2BU8eeVzKvEwXaMuQ1bwD4iACqCdK%2FHUmmlbAqpMjsroBrfXdGosN0gkMC4BbdZ9S9HfgtEHkmjB8V40FVKUNGc4Dy%2BRk1jsEAtwdApkh8KqO4kla7rFU8SZ2DJpKMVFxtMJZdqhmajys1RmGe%2FDArKkVGFAswhtaPXzg7E%2BJPHrIAfY3RHRNJBiACkPorUE3gICUvlTRBE0UlZR53oBHiWlHcd378MLFG%2FHpvpFHiu5pNK2HMcvmdhu1pha2dsmi7to7ScZtT3TkHOhqv3gsy2yid48ffvixSzq35KkpaD83ee7jNAnI5WTEKRGatwR8zoAEgvkornOly%2FkLcPcIHucxUm1LTMhScBOgROXafYNb2SmURxyrM%2BfQKJbwIOguig3PSrGtc%2BiPuc0C22ZJLEDdg8FfVN8sCWqjVGGyNiYyeJ5IMpEnmANGhuoGdbGh8nj5P%2FEP8BS9hqtpvW5K00qJNKkQJxEMuX4qirt%2FvWw6L1VUJlEFQa%2FtbZrC794DwZ1BXbjvEhvYx7qm7hGCpsj7Yl031vVFdKXyDkt8iqUahTZw65qkttZ1DWo065podrdqNAQU77vziIFunyRMVqnlIVJ6SWpQILFj6BU70lAKz1D88AzVOLsNSQC9vz%2Fe4eUqxokiy2PwdwSENDmMAmE0U5vmbN%2FKOxrD34fCWgmPn9s2CTRYiKwKwxuFnKR6N14zqyyjWg6CIW4ezL6USTYELFnZkQM2i3dDsJpmn1Y2C%2BoQLP6isoLim5K5BmhdVQpNebEIsnMnfOWeWJ6c6AD3qD4q4X4o1P08XkW63eYfILMNWHiHtxgfb8zceuw8ieUp5OvI8FTjtrBLilhPaIFzPgjTRzMbLEgRIxjj3q7RmHD8QgJqXFTzSe2hN3wFwOQBh2GAj9%2B9SgCCY%2B8tk0Pepj6JkqvQaxdMfbk%2FIW85PU%2Fxy%2BGYPkU8O5ta6tcm7KKQepIYb4xyAhIrv65XFTr4uZ9LCD19Xj0U1Yr%2FBw%3D%3D#%7B"pageId"%3A"izSGmy-AS5io3GQ7_Egs"%7D)

> Таблица распределения задач
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2011.png)

> Таблица КЭ/ГЗ
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2012.png)

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2013.png)

> Часть чек-листа
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2014.png)

> Баг-репорты
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2015.png)

> Пример баг-репорта
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2016.png)

> Дополнительные артефакты тестирования
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2017.png)

### Яндекс.Метро (Android mobile app) | Cheklist, Test-Cases, Bug-reports, Pairwise testing

<aside>
💡 Яндекс.Метро - приложение для составления маршрута метро от точки А до точки Б в разных городах.
В этом проекте мне удалось протестировать мобильное приложение Яндекс.Метро. Тестирование проводилось в следующем тестовом окружении: **Android Studio**, эмулированное устройство - **Honor 8, ОС Android 9.0 Pie**, разрешение экрана: **1080х1920
Номер тестируемой версии приложения:** Яндекс.Метро v3.6

</aside>

> Регрессионный чек-лист и проверки особенности работы мобильных приложений
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2018.png)

> Таблица принятия решений
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2019.png)

> Тест-кейсы на проверку приложения
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2020.png)

> Пример тест-кейса
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2021.png)

> Чек-лист для проверки особенностей мобильного приложения
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2022.png)

> Баг-репорты
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2023.png)

> Пример баг-репорта #1
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2024.png)

> Пример баг-репорта #2
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2025.png)

### Яндекс.Маршруты | Equivalence coefficients & Boundary values, Test-cases, Bug-reports

<aside>
💡 Яндекс.Маршруты - приложение для составления маршрута и навигации на карте мира, выбора транспортного средства и вызова такси.
Для этой части приложения я сделал проверки для режима Аэротакси и формы добавления банковской карты. Также завел несколько баг-репортов.

</aside>

> Таблица коэффициентов эквивалентности и граничных значений
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2026.png)

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2027.png)

> Таблица состояний и переходов
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2028.png)

> Тест-кейсы для функционала Аэротакси
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2029.png)

> Пример тест-кейса
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2030.png)

> Баг репорты
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2031.png)

> Пример баг-репорта #1
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2032.png)

> Пример баг-репорта #2
> 

![Untitled](CV%20Dmitry%20Volkov%20e889542de64146aa93160a7fce6cdfd2/Untitled%2033.png)

### Яндекс.Маршруты | Mind-map, State diagram, Block-diagram

<aside>
💡 Яндекс.Маршруты - приложение для составления маршрута и навигации на карте мира, выбора транспортного средства и вызова такси.
В этом проекте я проводил анализ требований веб-приложения Яндекс Маршруты. Мною была сделана Mind-map для большей части сайта и отдельная для фичи курьерского режима. Также я сделал Block-diagram расчета средней скорости такси в зависимости от начала поездки и State diagram для всего приложения.

</aside>

> Диаграмма состояний и переходов приложения
> 

[https://miro.com/app/live-embed/uXjVNBvXQjI=/?moveToViewport=-1470,-690,2292,1141&embedId=849115218492](https://miro.com/app/live-embed/uXjVNBvXQjI=/?moveToViewport=-1470,-690,2292,1141&embedId=849115218492)

> Mind-map для курьерского режима
> 

[https://miro.com/app/live-embed/uXjVNIWA47w=/?moveToViewport=-737,-871,2562,1276&embedId=351974143665](https://miro.com/app/live-embed/uXjVNIWA47w=/?moveToViewport=-737,-871,2562,1276&embedId=351974143665)

> Блок-схема для расчета средней скорости такси в зависимости от начала поездки
> 

[https://miro.com/app/live-embed/uXjVNLzkxbQ=/?moveToViewport=-905,-607,3333,1660&embedId=959301215389](https://miro.com/app/live-embed/uXjVNLzkxbQ=/?moveToViewport=-905,-607,3333,1660&embedId=959301215389)

> Общая Mind-Map приложения Яндекс Маршруты
> 

[https://miro.com/app/live-embed/uXjVNKVrOP8=/?moveToViewport=-6027,-1966,9119,4542&embedId=730673447140](https://miro.com/app/live-embed/uXjVNKVrOP8=/?moveToViewport=-6027,-1966,9119,4542&embedId=730673447140)