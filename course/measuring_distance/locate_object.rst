Пошук найближчого об'єкта
===========================

Інший спосіб використання ультразвукового далекоміра – це пошук об'єктів, що знаходяться поблизу. 

.. admonition:: Спробуйте

    Який найефективніший спосіб знайти предмет? Спробуйте!

Повернути і виявити
~~~~~~~~~~~~~~~~~~~~

Щоб спочатку виявити об'єкт, робот може повільно обертатися по колу, постійно опитуючи дальномір.
Потім, коли об'єкт виявлено, він може припинити обертання і рухатися до об'єкта.

Як ми можемо визначити, коли об'єкт виявлено? Уявіть, що робот знаходиться в центрі порожньої кімнати, а десь поблизу нього розміщено випадковий об'єкт. Далекомір буде показувати великі значення відстані, поки не досягне об'єкта, після чого значення відстані зменшиться. Саме *зміна* значень відстані вказує на те, що об'єкт виявлено.

Як ми можемо знайти зміну показань відстані за кожну ітерацію циклу? Ми можемо зберегти попереднє показання відстані у змінній і порівняти його з поточним показанням відстані. Якщо ця зміна перевищує певний поріг, то ми можемо припустити, що об'єкт був виявлений.

Щоб запрограмувати це, ми можемо почати з налаштування швидкості приводних двигунів на обертання в протилежних напрямках, щоб почати обертання робота на місці. Потім, коли зміна показань відстані перевищить поріг, робот може припинити обертання і рухатися до об'єкта.

У наведеному нижче прикладі коду ми використовуємо поріг зміни 30 см.

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            changeThreshold = 30 # distance change in cm needed to trigger detection

            # store initial value for current distance
            currentDistance = rangefinder.distance()

            # start spinning in place until an object is detected
            drivetrain.set_speed(5, -5)

            while True: # doesn't actually repeat forever. loop will be broken if an object is detected
                
                # update previous and current distance
                previousDistance = currentDistance
                currentDistance = rangefinder.distance()

                # if sudden decrease in distance, then an object has been detected
                if previousDistance - currentDistance > changeThreshold:
                    break # break out of the while loop

                time.sleep(0.1)

            # stop spinning drive motors
            drivetrain.stop()


    .. tab-item:: Blockly

        .. image:: media/detection.png
            :width: 900

Покращення точності
~~~~~~~~~~~~~~~~~~

Чи бачите ви якісь проблеми в цьому рішенні?

Коли відстань дальноміра опускається нижче порогу, це не означає, що робот знайшов об'єкт,
а скоріше, що робот знайшов його *край*. Отже, робот буде націлюватися на край об'єкта, а не на його центр.

Натомість робот повинен запам'ятати напрямок, в якому він знаходиться, коли виявляє *обидва* краї. Потім робот може націлитися на центр між цими краями, а отже, і на центр об'єкта. Ми можемо зберегти кожен кут краю у змінних, назвавши їх :code:`firstAngle` і :code:`secondAngle`.

Ми вже добре знайомі з поворотом до виявлення краю. Тепер нам потрібно виявити *обидва* краї. Однак просто скопіювати код виявлення краю було б досить незручно і схильно до помилок, тому давайте створимо функцію для узагальнення цього. Зверніть увагу, що існуючий код виявляє раптове *зменшення* відстані, але ми хочемо обробляти і раптові *збільшення* відстані.

Як ми можемо підтримати обидві поведінки в одній функції? Ми можемо передати параметр, щоб вказати, чи хочемо ми виявити збільшення
або зменшення відстані! Ми можемо назвати цей параметр :code:`isIncrease` і передати в нього булеве значення (true або false).

Якщо :code:`increase` дорівнює :code:`True`, то ми хочемо виявити збільшення відстані, яке відбувається, коли :code:`currentDistance - previousDistance > changeThreshold`.

Якщо :code:`increase` дорівнює :code:`False`, то ми хочемо виявити зменшення відстані, яке відбувається, коли :code:`previousDistance - currentDistance > changeThreshold`.

Для більшої гнучкості додамо параметр для порогу зміни.

Ось визначення функції:

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            def turnUntilEdge(isIncrease, changeThreshold):

                # store initial value for current distance
                currentDistance = rangefinder.distance()

                # start spinning in place until an object is detected
                drivetrain.set_speed(5, -5)

                while True: # doesn't actually repeat forever. loop will be broken if an object is detected
                    
                    # update previous and current distance
                    previousDistance = currentDistance
                    currentDistance = rangefinder.distance()

                    if isIncrease and currentDistance - previousDistance > changeThreshold:
                        # if sudden increase in distance, then an object has been detected
                        break
                    elif not isIncrease and previousDistance - currentDistance > changeThreshold:
                        # if sudden decrease in distance, then an object has been detected
                        break

                    time.sleep(0.1)

                # stop spinning drive motors
                drivetrain.stop()


    .. tab-item:: Blockly

        .. image:: media/detectiondefinition.png
            :width: 900

Ось еквівалентний виклик функції до коду повороту та виявлення в попередньому розділі:

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            turnUntilEdge(False, 30)

    .. tab-item:: Blockly

        .. image:: media/detectioncall.png
            :width: 200

Тепер настав час написати повну програму для виявлення обох країв і повороту до центру.

Впровадження подвійного виявлення країв
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Давайте пройдемося по кожному кроку процесу в коді.

Спочатку робот повинен обертатися на місці, поки не виявить перший край, а потім зупинитися. Це просто виклик функції, який ми бачили раніше.

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            turnUntilEdge(False, 30)

    .. tab-item:: Blockly

        .. image:: media/detectioncall.png
            :width: 200

Далі ми хочемо записати напрямок руху робота до цього першого краю і зберегти його в :code:`firstAngle`.

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            firstAngle = imu.get_yaw()

    .. tab-item:: Blockly

        .. image:: media/firstangle.png
            :width: 200

Потім робот повинен знову обертатися на місці, поки не виявить другий край, коли відбудеться раптове збільшення відстані.

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            turnUntilEdge(True, 30)

    .. tab-item:: Blockly

        .. image:: media/turntoedgetrue.png
            :width: 200

Як тільки робот виявить другий край, він повинен записати його напрямок і зберегти його в :code:`secondAngle`. Тепер потрібно з'ясувати, на скільки робот повинен відступити, щоб націлитися на центр об'єкта. Ми можемо це зробити, знайшовши різницю між двома кутами і розділивши її на два. Це половина кута між двома краями, і якщо робот відступить на цю відстань, він буде спрямований до центру об'єкта. Давайте збережемо це в змінній під назвою :code:`angleToTurn`.

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            secondAngle = imu.get_yaw()
            angleToTurn = (firstAngle - secondAngle) / 2

    .. tab-item:: Blockly

        .. image:: media/angletoturn.png
            :width: 400

Нарешті, робот може повернутись настільки, щоб опинитися обличчям до центру об'єкта, і рухатись у його напрямку.

Ось повний код. Зверніть увагу, що для кращої наочності дій робота додано півсекундні паузи:

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            # turn to first edge
            turnUntilEdge(False, 30)

            # store angle at first edge
            firstAngle = imu.get_yaw()

            time.sleep(0.5)

            # turn to second edge
            turnUntilEdge(True, 30)

            # store angle at second edge and calculate angle to turn
            secondAngle = imu.get_yaw()
            angleToTurn = (firstAngle - secondAngle) / 2

            time.sleep(0.5)

            # turn to center of object
            drivetrain.turn(angleToTurn)

    .. tab-item:: Blockly

        .. image:: media/fulldualedge.png
            :width: 400
