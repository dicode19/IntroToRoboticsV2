Пропорційне управління за допомогою IMU
=================================

Вступ:
-------------

На друкованій платі XRP міститься дуже корисний датчик, який називається інерційним вимірювальним блоком (IMU). Цей датчик використовує кілька внутрішніх датчиків для визначення напрямку, в якому рухається робот, а також прискорення в основних напрямках.

.. image ::
    media/6dof.jpg

.. note:: 
    IMU вимірює прискорення в сантиметрах на секунду в квадраті в 3 основних напрямках:     
    вперед і назад (z), з боку в бік (x) і вгору та вниз (y).    
    Він також вимірює кут в градусах з відхиленням (поворот з боку в бік), нахилом (направлення вгору     або вниз) і креном (нахил вбік).

Отримати ці значення дуже просто. Функції для отримання прискорення по будь-якій осі наведені нижче:


.. tab-set:: 

    .. tab-item:: Python

        .. code-block:: python

            from XRPLib.imu import IMU

            imu = IMU.get_default_imu()
            imu.calibrate(1)


            imu.get_acc_x()

            imu.get_acc_y()

            imu.get_acc_z()

    .. tab-item:: Blockly

        .. image:: media/acceleration-blockly.png
            :width: 600

Отримати кути в кожній осі так само просто, функції для цього наведені нижче:

.. tab-set:: 

    .. tab-item:: Python

        .. code-block:: python

            from XRPLib.imu import IMU

            imu = IMU.get_default_imu()
            imu.calibrate(1)


            imu.get_yaw()

            imu.get_roll()

            imu.get_pitch()

    .. tab-item:: Blockly

        .. image:: media/gyro-blockly.png
            :width: 500

Ви можете використовувати ці значення, щоб визначити напрямок, в якому спрямований ваш робот, крутизну поверхні, по якій він рухається, або навіть бічний нахил, якщо він рухається по нерівній поверхні.

Поворот з IMU
--------------------

Поворот під кутом є найпоширенішим застосуванням IMU. Якщо ви знаєте, в якому напрямку ви хочете, щоб ваш робот повернув, ви можете налаштувати пропорційний контур управління, щоб перемістити виміряний напрямок у бажаний напрямок.

.. tab-set::

    .. tab-item:: Python

        .. code-block:: python

            import math
            from XRPLib.imu import IMU
            from XRPLib.encoded_motor import EncodedMotor
            from XRPLib.differential_drive import DifferentialDrive

            targetAngle = None
            kP = None

            imu = IMU.get_default_imu()
            imu.calibrate(1)

            motor1 = EncodedMotor.get_default_encoded_motor(1)

            differentialDrive = DifferentialDrive.get_default_differential_drive()

            def turn(targetAngle):
                global kP
                kP = 0.015
                while not (math.fabs(targetAngle - (imu.get_yaw())) < 3 and (motor1.get_speed()) < 5):
                    differentialDrive.set_effort((((targetAngle - (imu.get_yaw())) * kP) * -1), ((targetAngle - (imu.get_yaw())) * kP))
                differentialDrive.stop()


            turn(90)

    .. tab-item:: Blockly
        
        .. image:: media/gyroturn-blockly.png
            :width: 600


