Створення власних кнопок веб-сервера
==========================================

На минулому уроці ви дізналися, як прив'язати прості команди диска до кнопок зі стрілками.
Але що робити, якщо ви хочете виконувати більш складні завдання з кодом? Або якщо у вас є більше 5 команд, які ви хочете запустити з веб-сервера?

XRPLib надає інструменти для цього! 
За допомогою методу :code:`webserver.add_button(label, func)` ви можете додати до веб-сервера будь-яку функцію, а також корисну мітку, яка описує, що робить ваша функція!

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            from XRPLib.defaults import *

            def raiseArm():
                servo_one.set_angle(100)

            def lowerArm():
                servo_one.set_angle(0)

            def led_blink():
                board.led_blink(5)

            def led_stop():
                board.led_off()
            
            def square():
                # Drives in a square with 20cm sides
                for side in range(4):
                    drivetrain.straight(20)
                    drivetrain.turn(90)

            webserver.add_button("Raise Arm", raiseArm)
            webserver.add_button("Lower Arm", lowerArm)
            webserver.add_button("Blink LED", led_blink)
            webserver.add_button("Stop LED", led_stop)
            webserver.add_button("20cm Square", square)

            webserver.start_network()
            webserver.start_server()

    .. tab-item:: Blockly

        .. image:: 
            media/custom-buttons.png


Після запуску цього коду ви повинні побачити нові кнопки в розділі «Custom Buttons» (Настроювані кнопки) веб-сторінки!
Примітка: ми рекомендуємо залишити робота підключеним до мережі під час першого запуску нової функції на веб-сервері, щоб переконатися, що він не має жодних помилок.

Ось і все! Це всі основи використання веб-сервера!
