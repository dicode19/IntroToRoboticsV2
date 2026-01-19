Використання веб-сервера як панелі інструментів 
===================================

Найбільш корисним застосуванням веб-сервера є його використання в якості інформаційної панелі. Веб-сервер може відображати дані, що оновлюються в режимі реального часу від робота, що може бути чудовим інструментом для налагодження. Виведення значень, які обчислює або вимірює ваш робот, може допомогти вам зрозуміти, чому виникає помилка, оскільки ці значення можуть не відповідати вашим очікуванням, а єдиний спосіб їх визначити — це зчитати їх під час виконання програми.

Ви можете надіслати значення на веб-сервер лише одним рядком.
Функція :code:`webserver.log_data(label, data)` створить мітку на веб-сервері з відповідними даними.
Якщо ви викличете цю функцію кілька разів, вона оновить мітку на веб-сервері найновішим значенням, і саме так відбувається оновлення в режимі реального часу.

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            from XRPLib.defaults import *

            webserver = Webserver.get_default_webserver()

            rangefinder = Rangefinder.get_default_rangefinder()

            webserver.log_data("Distance", rangefinder.distance())

            # Uses defaults from secrets.json  
            webserver.start_network()  
            webserver.start_server()

    .. tab-item:: Blockly

        .. image:: media/dashboard.png
            :width: 600

Тепер, після запуску цього коду, ви, можливо, помітили, що він не оновлюється в режимі реального часу. Це тому, що функція log_data викликається тільки один раз.
Вашим першим інстинктом може бути помістити це в цикл, але це не спрацює, оскільки веб-сервер бере під контроль основний цикл подій.
Щоб ваші дані оновлювалися автоматично, ми можемо використовувати клас MicroPython :code:`Timer`.

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            from XRPLib.defaults import *
            from machine import Timer

            webserver = Webserver.get_default_webserver()

            rangefinder = Rangefinder.get_default_rangefinder()

            def update_distance(timer):
                # We move the log_data call into this function so that it is called every time the timer is triggered
                webserver.log_data("Distance", rangefinder.distance())

            # Timer(-1) creates a timer that is not attached to any hardware, so it is purely a software timer
            timer = Timer(-1)
            # The timer is set to trigger the update_distance function every 1000 milliseconds (1 second)
            timer.init(period=1000, mode=Timer.PERIODIC, callback=update_distance)

            # Uses defaults from secrets.json  
            webserver.start_network()  
            webserver.start_server()

    .. tab-item:: Blockly

        Це ще не підтримується в Blockly.

Тепер відстань буде оновлюватися кожну секунду. Ви можете змінити період на будь-який інший. 
Слід звернути увагу на те, що функція, яка викликається таймером, повинна приймати один параметр, яким є сам таймер.
У цьому прикладі ми не використовуємо цей параметр, але він повинен бути присутнім.

Тепер ви можете використовувати це для налагодження свого робота. Ви можете додавати скільки завгодно міток, а в майбутньому з'являться додаткові опції реєстрації.
