Очікування введення з кнопки
========================

Ви, мабуть, помітили, що ваш код запускається одразу після завантаження. 
Іноді це добре, але іноді ви пишете код не в тому місці, де будете його
виконувати, і раптова їзда робота зі столу не є ідеальним результатом. 
Щоб код запускався за командою, ми можемо використовувати вбудовані
кнопки, щоб вказати коду, коли його запускати.

XRP має кнопку, яку можна прочитати з коду. Для зручності **XRPLib**
має вбудовану функцію, яка буде чекати, поки ви натиснете кнопку.

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python
            
            from XRPLib.defaults import *
            from time import sleep

            board.wait_for_button()
            sleep(1)
            drivetrain.straight(20)

    .. tab-item:: Blockly

        .. image:: media/waitForButton.png
            :width: 300

Ця функція є частиною :code:`board`, оскільки кнопка знаходиться на головній 
платі контролера XRP.

Цей код буде чекати, поки не буде натиснута кнопка, а потім ще 1 секунду (щоб ви встигли прибрати палець) і тільки після цього почне рух.

Також є функція, яка дозволяє зчитати поточний стан кнопки,
не чекаючи на неї:

.. code-block:: python

    board.is_button_pressed()

You could use this function as the *condition* for a :code:`while` loop if you
wanted to do something more complicated with the button than just waiting for
it.
