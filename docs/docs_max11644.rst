.. module:: max11644

*********************
MAX11644/11645 Module
*********************

.. _datasheet: https://datasheets.maximintegrated.com/en/ds/MAX11644-MAX11645.pdf

This module contains the Zerynth driver for Maxim MAX1164X low-power Analog-to-Digital Converters with I2C interface (datasheet_).

Example: ::
        
        from maxim.max11644 import MAX11644
        
        ...
        
        my_adc = max11644.MAX11644(I2C1)
        
        # set the internal reference ON and use it as reference voltage 
        my_adc.setup(ref = 5)
        
        # configure the adc to read Ain1 - Ain0 differential input
        my_adc.config(3, 1, 0)
        
        # get the raw converted value from the adc
        value = my_adc.get_raw_data()
    
===============
MAX11644 class
===============

.. class:: MAX11644(i2cdrv, clk = 100000)

    Creates an instance of the MAX11644 class. This class allows the control of both MAX11644 and MAX11645 devices.
    The I2C slave address of these devices is factory programmed to 0x36.
    
    :param i2cdrv: I2C Bus used '(I2C0, ...)'
    :param clk: Clock speed. Allowed values are 100000, 400000, 1000000
    
    
.. _setup:

.. method:: setup(ref = 0, clk = 0, mode = 0, rst = 1)

    Writes the device's setup register.

    **Parameters:**

    * **ref** : select the reference voltage.
    
        +-----+-------------------+------------------+--------------------------+
        | ref | Reference Voltage | REF pin          | Internal reference state |
        +=====+===================+==================+==========================+
        | 0-1 | Vdd               | Not connected    | Always on                |
        +-----+-------------------+------------------+--------------------------+
        | 2-3 | External          | Reference input  | Always on                |
        +-----+-------------------+------------------+--------------------------+
        | 4   | Internal          | Not connected    | Always on                |
        +-----+-------------------+------------------+--------------------------+
        | 5   | Internal          | Not connected    | Always on                |
        +-----+-------------------+------------------+--------------------------+
        | 6   | Internal          | Reference output | Always off               |
        +-----+-------------------+------------------+--------------------------+
        | 7   | Internal          | Reference output | Always on                |
        +-----+-------------------+------------------+--------------------------+
    
    * **clk** : set the clock source:
        
        * ``0`` : internal clock
        
        * ``1`` : external clock
    
    * **mode** : select the operating mode:
    
        * ``0`` : unipolar, differential input from 0 to Vref, digital output code is binary
        
        * ``1`` : bipolar, differential input from -Vref/2 to Vref/2, digital output is two's complement
    
    * **rst** : reset the configuration register to default:
    
        * ``0`` : reset the configuration 
        
        * ``1`` : no action
    
    
.. method:: config(scan = 0, ch = 0, in_mode = 0)

    Writes the device's configuration register.

    **Parameters:**

    * **scan** : select the scanning configuration.
    
        +------+----------------------------------------------------+
        | scan | Scanning configuration                             |
        +======+====================================================+
        | 0    | Scans up from Ain0 to the input selected by **ch** |
        +------+----------------------------------------------------+
        | 1    | Converts the input selected by **ch** 8 times      |
        +------+----------------------------------------------------+
        | 2    | do not use                                         |
        +------+----------------------------------------------------+
        | 3    | Converts the input selected by **ch**              |
        +------+----------------------------------------------------+
    
    * **ch** : select input channels to be used for conversion.
        
        +----+-------------------------------------+-------------------------------------+
        | ch | Single-ended mode                   | Differential mode                   |
        |    +------------------+------------------+------------------+------------------+
        |    | positive channel | negative channel | positive channel | negative channel |
        +----+------------------+------------------+------------------+------------------+
        | 0  | Ain0             | GND              | Ain0             | Ain1             |
        +----+------------------+------------------+------------------+------------------+
        | 1  | Ain1             | GND              | Ain1             | Ain0             |
        +----+------------------+------------------+------------------+------------------+
    
    * **in_mode** : select the input mode:
    
        * ``0`` : differential mode, digital output depends on **mode** (see :ref:`setup() <setup>`).
        
        * ``1`` : single-ended mode, digital output is binary
    
    
.. method:: get_raw_data(n = 1)

    Return a list containing the conversion results. If **n** is ``1``, return a single value.
    If the current  operating mode is bipolar and the input mode is differential, the values are returned as signed integers, otherwise as unsigned integers.

    
