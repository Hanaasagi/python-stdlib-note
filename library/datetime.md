# [8.1. datetime — Basic date and time types](https://docs.python.org/3.5/library/datetime.html)


The `datetime` module supplies classes for manipulating dates and times in both simple and complex ways. While date and time arithmetic is supported, the focus of the implementation is on efficient attribute extraction for output formatting and manipulation. For related functionality, see also the `time` and `calendar` modules.

### datetime.MINYEAR
The smallest year number allowed in a `date` or `datetime` object. MINYEAR is 1.

### datetime.MAXYEAR
The largest year number allowed in a `date` or `datetime` object. MAXYEAR is 9999.

## 8.1.1. Available Types
几种类型间的继承关系  

    object
        timedelta
        tzinfo
            timezone
        time
        date
            datetime


## 8.1.2. timedelta Objects
A timedelta object represents a duration, the difference between two dates or times.

### class datetime.timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours=0, weeks=0)

All arguments are optional and default to 0. Arguments may be integers or floats, and may be positive or negative.

**Only days, seconds and microseconds are stored internally**. Arguments are converted to those units:

- A millisecond is converted to 1000 microseconds.
- A minute is converted to 60 seconds.
- An hour is converted to 3600 seconds.
- A week is converted to 7 days.

#### timedelta.min
The most negative timedelta object, `timedelta(-999999999)`.

#### timedelta.max
The most positive timedelta object, `timedelta(days=999999999, hours=23, minutes=59, seconds=59, microseconds=999999)`.

#### timedelta.resolution
The smallest possible difference between non-equal timedelta objects, `timedelta(microseconds=1)`.

Example usage:

    >>> from datetime import timedelta
    >>> year = timedelta(days=365)
    >>> another_year = timedelta(weeks=40, days=84, hours=23,
    ...                          minutes=50, seconds=600)  # adds up to 365 days
    >>> year.total_seconds()
    31536000.0
    >>> year == another_year
    True
    >>> ten_years = 10 * year
    >>> ten_years, ten_years.days // 365
    (datetime.timedelta(3650), 10)
    >>> nine_years = ten_years - year
    >>> nine_years, nine_years.days // 365
    (datetime.timedelta(3285), 9)
    >>> three_years = nine_years // 3;
    >>> three_years, three_years.days // 365
    (datetime.timedelta(1095), 3)
    >>> abs(three_years - ten_years) == 2 * three_years + year
    True




In addition to the operations listed above `timedelta` objects support certain additions and subtractions with `date` and `datetime` objects (see below).

**`timedelta` objects are hashable (usable as dictionary keys), support efficient pickling**, and in Boolean contexts, a timedelta object is considered to be true if and only if it isn’t equal to timedelta(0).


## 8.1.3. date Objects

### class datetime.date(year, month, day)
All arguments are required. Arguments may be integers, in the following ranges:

- MINYEAR <= year <= MAXYEAR
- 1 <= month <= 12
- 1 <= day <= number of days in the given month and year

### classmethod date.today()
Return the current local date. This is equivalent to `date.fromtimestamp(time.time())`.

### classmethod date.fromtimestamp(timestamp)
Return the local date corresponding to the POSIX timestamp, such as is returned by `time.time()`. This may raise `OverflowError`, if the timestamp is out of the range of values supported by the platform C `localtime()` function, and `OSError` on `localtime()` failure.
**It’s common for this to be restricted to years from 1970 through 2038.** Note that on non-POSIX systems that include leap seconds in their notion of a timestamp, leap seconds are ignored by `fromtimestamp()`.

### classmethod date.fromordinal(ordinal)
Return the date corresponding to the proleptic Gregorian ordinal, where January 1 of year 1 has ordinal 1. `ValueError` is raised unless `1 <= ordinal <= date.max.toordinal()`. For any date d, `date.fromordinal(d.toordinal()) == d`.

    In [41]: datetime.date.toordinal(datetime.date.today())
    Out[41]: 736389

    In [42]: datetime.date.fromordinal(736389)
    Out[42]: datetime.date(2017, 3, 1)


Class attributes:

### date.min
The earliest representable date, `date(MINYEAR, 1, 1)`.

### date.max
The latest representable date, `date(MAXYEAR, 12, 31)`.

### date.resolution
The smallest possible difference between non-equal date objects, `timedelta(days=1)`.

Instance attributes (read-only):

### date.year
Between MINYEAR and MAXYEAR inclusive.

### date.month
Between 1 and 12 inclusive.

### date.day
Between 1 and the number of days in the given month of the given year.



**Dates can be used as dictionary keys. In Boolean contexts, all date objects are considered to be true.**

Instance methods:

### date.replace(year, month, day)
Return a date with the same value, except for those parameters given new values by whichever keyword arguments are specified. For example, if `d == date(2002, 12, 31)`, then `d.replace(day=26) == date(2002, 12, 26)`.


### date.timetuple()
Return a `time.struct_time` such as returned by `time.localtime()`. The hours, minutes and seconds are 0, and the DST flag is -1. `d.timetuple()` is equivalent to `time.struct_time((d.year, d.month, d.day, 0, 0, 0, d.weekday(), yday, -1))`, where `yday = d.toordinal() - date(d.year, 1, 1).toordinal() + 1` is the day number within the current year starting with 1 for January 1st.

### date.toordinal()
Return the proleptic Gregorian ordinal of the date, where January 1 of year 1 has ordinal 1. For any date object d, `date.fromordinal(d.toordinal()) == d`.

### date.weekday()
Return the day of the week as an integer, where Monday is 0 and Sunday is 6. For example, `date(2002, 12, 4).weekday() == 2`, a Wednesday.

### date.isoweekday()
Return the day of the week as an integer, where Monday is 1 and Sunday is 7. For example, `date(2002, 12, 4).isoweekday() == 3`, a Wednesday.

### date.isocalendar()
Return a 3-tuple, (ISO year, ISO week number, ISO weekday).


The ISO year consists of 52 or 53 full weeks, and where a week starts on a Monday and ends on a Sunday. The first week of an ISO year is the first (Gregorian) calendar week of a year containing a Thursday. This is called week number 1, and the ISO year of that Thursday is the same as its Gregorian year.

For example, **2004 begins on a Thursday, so the first week of ISO year 2004 begins on Monday,** 29 Dec 2003 and ends on Sunday, 4 Jan 2004, so that `date(2003, 12, 29).isocalendar() == (2004, 1, 1)` and `date(2004, 1, 4).isocalendar() == (2004, 1, 7)`.

2003.12.29 Monday
2004.1.1   Thursday


### date.isoformat()
Return a string representing the date in ISO 8601 format, ‘YYYY-MM-DD’. For example, `date(2002, 12, 4).isoformat() == '2002-12-04'`.

### `date.__str__()`
For a date d, str(d) is equivalent to `d.isoformat()`.

### date.ctime()
Return a string representing the date, for example ``date(2002, 12, 4).ctime() == 'Wed Dec 4 00:00:00 2002'`. `d.ctime()` is equivalent to `time.ctime(time.mktime(d.timetuple()))` on platforms where the native C ctime() function (which `time.ctime()` invokes, but which `date.ctime()` does not invoke) conforms to the C standard.

### date.strftime(format)
Return a string representing the date, controlled by an explicit format string. Format codes referring to hours, minutes or seconds will see 0 values.

### `date.__format__(format)`
Same as `date.strftime()`. This makes it possible to specify a format string for a date object when using `str.format()`.

Example of counting days to an event:

    >>> import time
    >>> from datetime import date
    >>> today = date.today()
    >>> today
    datetime.date(2007, 12, 5)
    >>> today == date.fromtimestamp(time.time())
    True
    >>> my_birthday = date(today.year, 6, 24)
    >>> if my_birthday < today:
    ...     my_birthday = my_birthday.replace(year=today.year + 1)
    >>> my_birthday
    datetime.date(2008, 6, 24)
    >>> time_to_birthday = abs(my_birthday - today)
    >>> time_to_birthday.days
    202

Example of working with date:

    >>> from datetime import date
    >>> d = date.fromordinal(730920) # 730920th day after 1. 1. 0001
    >>> d
    datetime.date(2002, 3, 11)
    >>> t = d.timetuple()
    >>> for i in t:     
    ...     print(i)
    2002                # year
    3                   # month
    11                  # day
    0
    0
    0
    0                   # weekday (0 = Monday)
    70                  # 70th day in the year
    -1
    >>> ic = d.isocalendar()
    >>> for i in ic:    
    ...     print(i)
    2002                # ISO year
    11                  # ISO week number
    1                   # ISO day number ( 1 = Monday )
    >>> d.isoformat()
    '2002-03-11'
    >>> d.strftime("%d/%m/%y")
    '11/03/02'
    >>> d.strftime("%A %d. %B %Y")
    'Monday 11. March 2002'
    >>> 'The {1} is {0:%d}, the {2} is {0:%B}.'.format(d, "day", "month")
    'The day is 11, the month is March.'

## 8.1.4. datetime Objects
A `datetime` object is a single object containing all the information from a `date` object and a `time` object.

### class datetime.datetime(year, month, day, hour=0, minute=0, second=0, microsecond=0, tzinfo=None)

The year, month and day arguments are required. tzinfo may be `None`, or an instance of a tzinfo subclass. The remaining arguments may be integers, in the following ranges:

- MINYEAR <= year <= MAXYEAR
- 1 <= month <= 12
- 1 <= day <= number of days in the given month and year
- 0 <= hour < 24
- 0 <= minute < 60
- 0 <= second < 60
- 0 <= microsecond < 1000000

If an argument outside those ranges is given, ValueError is raised.

### classmethod datetime.today()
Return the current local datetime, with tzinfo `None`. This is equivalent to `datetime.fromtimestamp(time.time())`.

### classmethod datetime.now(tz=None)
Return the current local date and time. If optional argument tz is None or not specified, this is like `today()`
If tz is not `None`, it must be an instance of a tzinfo subclass, and the current date and time are converted to tz’s time zone. In this case the result is equivalent to `tz.fromutc(datetime.utcnow().replace(tzinfo=tz))`.

### classmethod datetime.utcnow()
Return the current UTC date and time, with tzinfo `None`. This is like `now()`, but returns the current UTC date and time, as a naive datetime object. An aware current UTC datetime can be obtained by calling `datetime.now(timezone.utc)`.

### classmethod datetime.fromtimestamp(timestamp, tz=None)
Return the local date and time corresponding to the POSIX timestamp, such as is returned by `time.time()`. If optional argument tz is `None` or not specified, the timestamp is converted to the platform’s local date and time, and the returned `datetime` object is naive.

If tz is not `None`, it must be an instance of a tzinfo subclass, and the timestamp is converted to tz’s time zone. In this case the result is equivalent to `tz.fromutc(datetime.utcfromtimestamp(timestamp).replace(tzinfo=tz))`.

### classmethod datetime.utcfromtimestamp(timestamp)
Return the UTC datetime corresponding to the POSIX timestamp, with tzinfo `None`.

### classmethod datetime.fromordinal(ordinal)
Return the datetime corresponding to the proleptic Gregorian ordinal, where January 1 of year 1 has ordinal 1.

### classmethod datetime.combine(date, time)
Return a new `datetime` object whose date components are equal to the given `date` object’s, and whose time components and tzinfo attributes are equal to the given `time` object’s. For any `datetime` object d, `d == datetime.combine(d.date(), d.timetz())`. If date is a `datetime` object, its time components and tzinfo attributes are ignored.

### classmethod datetime.strptime(date_string, format)
Return a datetime corresponding to date_string, parsed according to format. This is equivalent to `datetime(*(time.strptime(date_string, format)[0:6]))`.

Class attributes:

### datetime.min
The earliest representable datetime, `datetime(MINYEAR, 1, 1, tzinfo=None)`.

### datetime.max
The latest representable datetime, `datetime(MAXYEAR, 12, 31, 23, 59, 59, 999999, tzinfo=None)`.

### datetime.resolution
The smallest possible difference between non-equal datetime objects, `timedelta(microseconds=1)`.

Instance attributes (read-only):

### datetime.year
Between MINYEAR and MAXYEAR inclusive.

### datetime.month
Between 1 and 12 inclusive.

### datetime.day
Between 1 and the number of days in the given month of the given year.

### datetime.hour
In range(24).

### datetime.minute
In range(60).

### datetime.second
In range(60).

### datetime.microsecond
In range(1000000).

### datetime.tzinfo
The object passed as the tzinfo argument to the datetime constructor, or None if none was passed.

Instance methods:

### datetime.date()
Return `date` object with same year, month and day.

### datetime.time()
Return `time` object with same hour, minute, second and microsecond. tzinfo is None.

### datetime.timetz()
Return `time` object with same hour, minute, second, microsecond, and tzinfo attributes.

### datetime.replace([year[, month[, day[, hour[, minute[, second[, microsecond[, tzinfo]]]]]]]])
Return a `datetime` with the same attributes, except for those attributes given new values by whichever keyword arguments are specified.

### datetime.astimezone(tz=None)
Return a `datetime` object with new tzinfo attribute tz, adjusting the date and time data so the result is the same UTC time as self, but in tz‘s local time.

If called without arguments (or with tz=None) the system local timezone is assumed.

### datetime.utcoffset()
If tzinfo is `None`, returns `None`, else returns `self.tzinfo.utcoffset(self)`, and raises an exception if the latter doesn’t return `None`, or a timedelta object representing a whole number of minutes with magnitude less than one day.


### datetime.dst()
If tzinfo is `None`, returns `None`, else returns `self.tzinfo.dst(self)`, and raises an exception if the latter doesn’t return `None`, or a timedelta object representing a whole number of minutes with magnitude less than one day.

### datetime.tzname()
If tzinfo is `None`, returns `None`, else returns `self.tzinfo.tzname(self)`, raises an exception if the latter doesn’t return None or a string object,

### datetime.timetuple()
Return a `time.struct_time` such as returned by `time.localtime(). d.timetuple()` is equivalent to `time.struct_time((d.year, d.month, d.day, d.hour, d.minute, d.second, d.weekday(), yday, dst))`, where `yday = d.toordinal() - date(d.year, 1, 1).toordinal() + 1` is the day number within the current year starting with 1 for January 1st.


### datetime.utctimetuple()
If datetime instance d is naive, this is the same as `d.timetuple()` except that tm_isdst is forced to 0 regardless of what `d.dst()` returns. DST is never in effect for a UTC time.
