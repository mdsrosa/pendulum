Instantiation
=============

There are several different methods available to create a new instance of Pendulum.
First there is a constructor. It accepts the same parameters as the standard class.

.. code-block:: python

    from pendulum import Pendulum

    dt = Pendulum(2015, 2, 5, tzinfo='America/Vancouver')
    isinstance(dt, datetime)
    True

    dt = Pendulum.now(-5)

You'll notice above that the timezone (2nd) parameter was passed as a string and an integer
rather than a ``tzinfo`` instance. All timezone parameters have been augmented
so you can pass a ``tzinfo`` instance, string or integer offset to GMT
and the timezone will be created for you.
This is again shown in the next example which also introduces the ``now()`` function.

.. code-block:: python

    import pendulum

    now = pendulum.now()

    tz = pendulum.timezone('Europe/London')
    now_in_london_tz = pendulum.now(tz)

    # or just pass the timezone as a string
    now_in_london_tz = pendulum.now('Europe/London')
    print(now_in_london_tz.timezone_name)
    'Europe/London'

    # or to create a date with a timezone of +1 to GMT
    # during DST then just pass an integer
    print(pendulum.now(1).timezone_name)
    '+01:00'

To accompany ``now()``, a few other static instantiation helpers exist to create widely known instances.
The only thing to really notice here is that ``today()``, ``tomorrow()`` and ``yesterday()``,
besides behaving as expected, all accept a timezone parameter and each has their time value set to ``00:00:00``.

.. code-block:: python

    now = pendulum.now()
    print(now)
    '2016-06-28T16:51:45.978473-05:00'

    today = pendulum.today()
    print(today)
    '2016-06-28T00:00:00-05:00'

    tomorrow = pendulum.tomorrow('Europe/London')
    print(tomorrow)
    '2016-06-29T00:00:00+01:00'

    yesterday = pendulum.yesterday()
    print(yesterday)
    '2016-06-27T00:00:00-05:00'

The next group of static helpers are the ``from_xxx()`` and ``create()`` helpers.
Most of the static ``create`` functions allow you to provide
as many or as few arguments as you want and will provide default values for all others.
Generally default values are the current date, time set to ``00:00:00`` and ``UTC`` timezone.

.. code-block:: python

    pendulum.from_date(year, month, day, tz)
    pendulum.from_time(hour, minute, second, microsecond, tz)
    pendulum.create(year, month, day, hour, minute, second, microsecond, tz)

``from_date()`` will default the time to ``00:00:00``. ``from_time()`` will default the date to today.
``create()`` will default any null parameter to the current date for the date part and to ``00:00:00`` for time.
As before, the ``tz`` defaults to the ``UTC`` timezone and otherwise can be a ``TimezoneInfo`` instance
or simply a string timezone value.

.. code-block:: python

    xmas_this_year = pendulum.from_date(None, 12, 25)
    # Year defaults to current year
    y2k = pendulum.create(2000, 1, 1, 0, 0, 0)
    noon_london_tz = pendulum.from_time(12, 0, 0, tz='Europe/London')

.. code-block:: python

    pendulum.from_format(time, format, tz)

``from_format()`` is mostly a wrapper for the base Python function ``datetime.strptime()``.
The difference being the addition the ``tz`` argument that can be a ``tzinfo`` instance or a string timezone value
(defaults to ``UTC``).

.. code-block:: python

    pendulum.from_format('1975-05-21 22', '%Y-%m-%d %H').to_datetime_string()
    '1975-05-21 22:00:00'
    pendulum.from_format('1975-05-21 22', '%Y-%m-%d %H', 'Europe/London').isoformat()
    '1975-05-21T22:00:00+01:00'

    # Using strptime is also possible (the timezone will be UTC)
    pendulum.strptime('1975-05-21 22', '%Y-%m-%d %H').isoformat()

The final ``create`` function is for working with unix timestamps.
``from_timestamp()`` will create a ``Pendulum`` instance equal to the given timestamp
and will set the timezone as well or default it to ``UTC``.

.. code-block:: python

    pendulum.from_timestamp(-1).to_datetime_string()
    '1969-12-31 23:59:59'

    pendulum.from_timestamp(-1, 'Europe/London').to_datetime_string()
    '1970-01-01 00:59:59'

    # Using the standard fromtimestamp is also possible
    pendulum.fromtimestamp(-1).to_datetime_string()
    '1969-12-31 23:59:59'

You can also create a ``copy()`` of an existing ``Pendulum`` instance.
As expected the date, time and timezone values are all copied to the new instance.

.. code-block:: python

    dt = pendulum.now()
    print(dt.diff(dt.copy().add(years=1)).in_years())
    1

    # dt was unchanged and still holds the value of pendulum.now()

Finally, if you find yourself inheriting a ``datetime`` instance,
you can create a ``Pendulum`` instance via the ``instance()`` function.

.. code-block:: python

    dt = datetime(2008, 1, 1)
    p = pendulum.instance(dt)
    print(p.to_datetime_string())
    '2008-01-01 00:00:00'
