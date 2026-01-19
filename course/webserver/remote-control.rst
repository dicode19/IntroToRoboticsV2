Дистанційне керування вашим XRP
=============================

За допомогою веб-сервера ви також можете дистанційно керувати своїм XRP.
Клас веб-сервера має кілька методів, які дозволяють реєструвати функції, що викликаються при натисканні кнопки зі стрілкою, які ми можемо використовувати для керування XRP. 

Нижче наведено приклад того, як зареєструвати деякі основні функції приводу, які будуть викликатися при натисканні кнопок зі стрілками:

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            from XRPLib.defaults import *

            def forward():
                differentialDrive.set_effort(0.5, 0.5)

            webserver.registerForwardButton(forward)

            def back():
                differentialDrive.set_effort(-0.5, -0.5)

            webserver.registerBackwardButton(back)

            def left():
                differentialDrive.set_effort(-0.5, 0.5)

            webserver.registerLeftButton(left)

            def right():
                differentialDrive.set_effort(0.5, -0.5)

            webserver.registerRightButton(right)

            def stop():
                differentialDrive.stop()

            webserver.registerStopButton(stop)

    .. tab-item:: Blockly

        .. image:: media/rc-blockly.png
            :width: 600

Ці функції потім реєструються на веб-сервері, і при натисканні кнопок зі стрілками викликається відповідна функція.
Стрілки з'являться, якщо будь-яка з цих функцій зареєстрована, і будуть вимкнені, якщо вони не зареєстровані.

.. note::
    Ви також можете використовувати лямбда-функції для реєстрації функцій на веб-сервері,     
    що може бути корисно для простих функцій, але виходить за рамки цього уроку.
