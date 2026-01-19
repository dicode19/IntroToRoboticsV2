Відстеження відстані 
=================

Тепер, коли ми розглянули управління вмиканням-вимиканням, давайте використаємо цю інформацію для відстеження об'єкта з певної відстані. 

Процес
-----------

По суті, ми хочемо, щоб наш робот рухався до об'єкта, якщо він занадто далеко, і віддалявся від об'єкта, якщо він занадто близько. 

Ми можемо це зробити, використовуючи датчик відстані, щоб визначити, як далеко знаходиться об'єкт, а потім використовуючи цю інформацію, щоб визначити, в якому напрямку повинен рухатися робот.

Для цього завдання візьмемо цільову відстань 30 см. 

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            from XRPLib.rangefinder import Rangefinder

            rangefinder = Rangefinder.get_default_rangefinder()

            distance = 30
            while True:
                if rangefinder.distance() < distance:
                    drivetrain.set_speed(-20, -20)
                elif rangefinder.distance() > distance:
                    drivetrain.set_speed(20, 20)


    .. tab-item:: Blockly

        .. image:: media/SimpleStandoff.png
            :width: 500

Ви помітите, що цей код змушує робота рухатися вперед і назад, або коливатися, оскільки відстань сонара постійно змінюється від більшої до меншої за 30 см.
А що буде, якщо ми додамо третій випадок, який змусить двигуни робота зупинитися, коли відстань сонара дорівнюватиме 30 см?

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            from XRPLib.rangefinder import Rangefinder

            rangefinder = Rangefinder.get_default_rangefinder()

            distance = 30
            while True:
                if rangefinder.distance() < distance:
                    drivetrain.set_speed(-20, -20)
                elif rangefinder.distance() > distance:
                    drivetrain.set_speed(20, 20)
                else:
                    drivetrain.stop()


    .. tab-item:: Blockly

        .. image:: media/SimpleStandoffStop.png
            :width: 500

На жаль, навіть з цим кодом наш робот все одно не зупиняється! Проблема в тому, що датчик відстані настільки точний, що ніколи не показує точно 30 см. Ми можемо вирішити цю проблему, змусивши нашого робота зупинятися, коли він *наближається* до 30 см, а не *точно* досягає 30 см.
Ми можемо це зробити, створивши діапазон, в якому наш робот зупиняється, який називається «мертвою зоною». Використовуючи діапазон +- 2,5 см, наш новий код буде виглядати так:

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            from XRPLib.rangefinder import Rangefinder

            rangefinder = Rangefinder.get_default_rangefinder()

            distance = 30
            tolerance = 2.5
            while True:
                if rangefinder.distance() < distance - tolerance:
                    drivetrain.set_speed(-20, -20)
                elif rangefinder.distance() > distance + tolerance:
                    drivetrain.set_speed(20, 20)
                else:
                    drivetrain.stop()


    .. tab-item:: Blockly

        .. image:: media/deadband.png
            :width: 550

.. note:: 
    Зверніть увагу, що замість жорсткого кодування чисел, таких як 27,5 і 32,5, ми використовували        змінні. Це дає нам дві переваги:
    
    1. Ми можемо легко змінити бажану відстань і допуск без необхідності змінювати сам код.
    
    2. Набагато простіше розшифрувати, що робить код, використовуючи «магічні» числа, такі як 27,5 і         32,5, які можуть заплутати читача, оскільки користувач повинен з'ясувати, що означають ці             числа.

Цей код повинен дозволити роботу зупинитися, коли він відчуває відстань сонара ~30 см. Наша проблема зараз полягає в тому, що
існує потенційна похибка 2,5 см від бажаної відстані слідування. На щастя, в наступному розділі ми дізнаємося про таке поняття, як «пропорційне регулювання»...
