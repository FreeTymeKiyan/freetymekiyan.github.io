title: 'Java SE 8 For the Really Impatient, Note 8'
date: 2016-03-25 16:17:35
categories:
  - Coding
tags:
  - Java
  - Notes
---

# Chapter 5 The New Date and Time API

## Date Adjusters

Compute dates such as "the first Tuesday of every month". 

`TemporalAdjusters`: provides a number of static methods for common adjustments. You pass the result of an adjustment method to the `with` method. 
`with`: returns a new `LocalDate` object without modifying the original. 
```Java
LocalDate firstTuesday = LocalDate.of(year, month, 1).with(TemporalAdjusters.nextOrSame(DayOfWeek.TUESDAY));
```

**Available Adjusters in the TemporalAdjusters Class**
<br>

| Method | Description |
| :----- | :---------- |
| next(weekday), previous(weekday) | Next or previous date that falls on the given weekday |
| nextOrSame(weekday), previousOrSame(weekday) | Next or previous date that falls on the given weekday, starting from the given date |
| dayOfWeekInMonth(m, weekday) | The nth weekday in the month |
| lastInMonth(weekday) | The last weekday in the month |
| firstDayOfMonth(), firstDayOfNextMonth(),</br>firstDayOfNextYear(), lastDayOfMonth(),</br>lastDayOfPreviousMonth(), lastDayOfYear() | The date described in the method name |

Customize your own adjuster by implementing the `TemporalAdjuster` interface. 
```Java
// computing the next weekday
TemporalAdjuster NEXT_WORKDAY = w -> {
  LocalDate result = (LocalDate) w;
  do {
    result = result.plusDays(1);
  } while (result.getDayOfWeek().getValue() >= 6);
  return result;
};

LocalDate backToWork = today.with(NEXT_WORKDAY);
```

Note that the parameter of the lambda expression has type `Temporal`, and it must be cast to `LocalDate`. You can avoid this cast with the `ofDateAdjuster` method that expects a lambda of type `UnaryOperator<LocalDate>`. 
```Java
TemporalAdjuster NEXT_WORKDAY = TemporalAdjusters.ofDateAdjuster(w -> {
  LocalDate result = w; // No cast
  do {
    result = result.plusDays(1);
  } while (reuslt.getDayOfWeek().getValue() >= 6);
  return result;
});
```

## Local Time

`LocalTime`: represents a time of day, such as 15:30:00. Create an instance with `now` or `of` methods.
```Java
LocalTime rightNow = LocalTime.now();
LocalTime bedtime = LocalTime.of(22, 30);
LocalTime.of(22, 30, 0);
```

**Common LocalTime Methods**
<br>

| Method | Description |
| :----- | :---------- |
| now, of | These static methods construct a LocalTime, either from the curreqnt time, or from the given hours, minutes, and optionally, seconds and nanoseconds |
| plusHours, plusMinutes,</br>plusSeconds, plusNanos | Adds a number of horus, minutes, seconds, or nanoseconds to the LocalTime |
| minusHours, minusMinutes,</br>minusSeconds, minusNanos | Subtracts a number of hours, minutes, seconds, or nanoseconds to the LocalTime |
| plus, minus | Adds or subtracts a Duration |
| withHour, withMinute, withSecond, withNano | Returns a new LocalTime with the hour, minute, second or nanosecond changed to the given value |
| getHour, getMinute, getSecond, getNano | Gets the hour, minute, second, or nanosecond of this LocalTime |
| toSecondOfDay, toNanoOfDay | Returns the number of seconds or nanoseconds between midnight and this LocalTime |

`plus` and `minus` operations wrap around a 24-hour day. 
```Java
LocalTime wakeup = bedtime.plusHours(8);
```

`LocalTime` doesn't concern itself with AM/PM. Deal by `Formatter`. 

`LocalDateTime`: represents a date and time. Suitable for storing points in time in a fixed time zone. E.g., for a schedule of classes or events. 

## Zoned Time

Use `ZonedDateTime` if you need to make calculations that span the daylight savings time, or if you need to deal with users in different time zones. 

`ZoneId.getAvailableIds`: find out all available time zones. 

`ZoneId.of(id)`: yields a `ZoneId` object you can use to turn a `LocalDateTime` object into a `ZonedDateTime` object by calling `local.atZone(zoneId)`, or you can construct a `ZonedDateTime` by calling the static method `ZonedDateTime.of(year, month, day, hour, minute, second, nano, zoneId)`. 
```Java
ZonedDateTime apollo1Launch = ZonedDateTime.of(1969, 7, 16, 9, 32, 0, 0, ZoneId.of("America/New_York"));
```

Call `apollo1Launch.toInstant` to get the `Instant`. 

Call `Instant.atZone(ZoneId.of("UTC"))` to get the `ZonedDateTime` at the Greenwich Royal Observatory. Or use another `ZoneId` to get it elsewhere on the planet. 

**ZonedDateTime Methods**
</br>

| Method | Description |
| :----- | :---------- |
| now, of, ofInstant | These static methods construct a ZonedDateTime from the current time, or from a year, month, day, hour, minute, second, nanosecond(or a LocalDate and LocalTime), and ZoneId, or from an Instant and ZoneId |
| plusDays, plusWeeks,</br>plusMonths, plusYears,</br>plusHours, plusMinutes,</br>plusSeconds, plusNanos | Adds a number of temporal units to this `ZonedDateTime` |
| minusDays, minusWeeks,</br>minusMonths, minusYears,</br>minusHours, minusMinutes,</br>minusSeconds, minusNanos | Subtracts a number of temporal units to this `ZonedDateTime` |
| plus, minus | Adds or subtracts a `Duration` or `Period` |
| withDayOfMonth, withDayOfYear,</br>withMonth, withYear, withHour,</br>withMinute, withSecond,</br>withNano | Returns a new `ZonedDateTime`, with one temporal unit changed to the given value |
| withZoneSameInstant, withZoneSameLocal | Returns a new `ZonedDateTime` in the given time zone, either representing the same instant or the same local time. |
| getDayOfMonth | Gets the day of the month (between 1 and 31). |
| getDayOfYear | Gets the day of the year (between 1 and 366). |
| getDayOfWeek | Gets the day of the week, returning a value of the `DayOfWeek` enumeration. |
| getMonth, getMonthValue | Gets the month as a value of the `Month` enumeration, or as a number between 1 and 12. |
| getYear | Gets the year, betweem -999,999,999 and 999,999,999 |
| getHour, getMinute, getSecond, getNano | Gets the hour, minute, second, or nanosecond of this `ZonedDateTime` |
| getOffset | Gets the offset from UTC, as a `ZoneOffset` instance. Offsets can vary from -12:00 to 14:00. Some time zones have fractional offsets. Offsets change with daylight savings time. |
| toLocalDate, toLocalTime, toInstant | Yields the local date or local time, or the corresponding instant. |
| isBefore, isAfter | Comapres this `ZonedDateTime` with another |

When daylight time starts, clocks are set forward by an hour.
```Java
ZonedDateTime skipped = ZonedDateTime.of(
  LocalDate.of(2013, 3, 31),
  LocalTime.of(2, 30),
  ZonedId.of("Europe/Berlin")
); // constructs March 31 3:30
```

When daylight time ends, clocks are set back by an hour, and there are 2 instants with the same local time!
```Java
ZonedDateTime ambiguous = ZonedDateTime.of(
  LocalDate.of(2013, 10, 27),
  LocalTime.of(2, 30),
  ZoneId.of("Europe/Berlin")
); // 2013-10-27T02:30+02:00[Europe/Berlin]

ZonedDateTime anHourLater = ambiguous.plusHours(1); // 2013-10-27T02:30+01:00[Europe/Berlin]
```
An hour later, the time has the same hours and minutes, but the zone offset has chagned. 

Pay attentionwhen adjusting a date across daylight savings time boundaries. If you set a meeting for next week, don't add a duration of 7 days:
```Java
ZonedDateTime nextMeeting = meeting.plus(Duration.ofDays(7)); // won't work with daylight savings time
```
Instead use the `Period` class:
```Java
ZonedDateTime nextMeeting = meeting.plus(Period.ofDays(7)); // OK
```

`OffsetDateTime`: represents times with an offset from UTC, but without time zone rules. Intended for specialized applications that specifically require the absence of those rules, such as certain network protocols. For human time, use `ZonedDateTime`. 

## Formatting and Parsing

`DateTimeFormatter`: provides 3 kinds of formatters to print a date/time value:
* Predefined standard formatters
* Locale-specific formatters
* Formatters with custom patterns

**Predefined Formatters**
</br>

| Formatter | Description | Example |
| :-------- | :---------- | :------ |
| BASIC_ISO_DATE | Year, month, day,</br>zone offset</br>without separators | 19690716-0500 |
| ISO_LOCAL_DATE,</br>ISO_LOCAL_TIME,</br>ISO_LOCAL_DATE_TIME | Separators -, :, T | 1969-07-16, 09:32:00,</br>1969-07-16T09:32:00 |
| ISO_OFFSET_DATE,</br>ISO_OFFSET_TIME,</br>ISO_OFFSET_DATE_TIME | Like ISO_LOCAL_XXX,</br>but with zone</br>offset | 1969-07-16-05:00,</br>09:32:00-05:00,</br>1969-07-16T09:32:00-05:00 |
| ISO_ZONED_DATE_TIME | With zone offset</br>and zone ID | 1969-07-16T09:32:00-05:00[America/New_York] |
| ISO_INSTANT | In UTC, denoted by the Z zone ID | 1969-07-16T14:32:00Z |
| ISO_DATE, ISO_TIME,</br>ISO_DATE_TIME | Like ISO_OFFSET_DATE,</br>ISO_OFFSET_TIME,</br>ISO_ZONED_DATE_TIME,</br>but the zone</br>information is optional | 1969-07-16-05:00, 09:32:00-05:00,</br>1969-07-16T09:32:00-05:00[America/New_York] |
| ISO_ORDINAL_DATE | The year and day of year, for LocalDate | 1969-197 |
| ISO_WEEK_DATE | The year, week, and day of week, for LocalDate | 1969-W29-3 |
| RFC_1123_DATE_TIME | The standard for</br>email timestamps,</br>codified in</br>RFC 822 and</br>updated to four digits for the year</br> in RFC 1123 | Wed, 16, Jul 1969 09:32:00 -0500 |

To use one of the standard formatters, simply call its `format` method.
```Java
String formatted = DateTimeFormatter.ISO_DATE_TIME.format(apollo1Launch);
```

The standard formatters are mostly intended for machine-readable timestamps. To present date and times to human readers, use a local-specific formatter. 4 style: `SHORT`, `MEDIUM`, `LONG`, and `FULL`, for both date and time.

**Locale-Specific Formatting Styles**
</br>

| Style | Date | Time |
| :---- | :--- | :--- |
| SHORT | 7/16/69 | 9:32 AM |
| MEDIUM | Jul 16, 1969 | 9:32:00 AM |
| LONG | July 16, 1969 | 9:32:00 AM EDT |
| FULL | Wednesday, July 16, 1969 | 9:32:00 AM EDT|

The static mehods `ofLocalizedDate`, `ofLocalizedTime`, and `ofLocalizedDateTime` craete such a formatter.
```Java
DateTimeFormatter formatter = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.LONG);
String formatted = formatter.format(apollo1Launch); // July 16, 1969 9:32:00 AM EDT
```

The `java.time.format.DateTimeFormatter` class is intended as a replacement for `java.util.DateFormat`. If you need an instance of the latter for backwards compatibility, call `formatter.toFormat()`. 

Finally, you can roll your own date format by specifying a pattern. 
```Java
formatter = DateTimeFormatter.ofPattern("E yyyy-MM-dd HH:mm");
```
Formats a date in the form `Wed 1969-07-16 09:32`. Each letter denotes a different time field. 

**Commonly Used Formatting Symbols for Date/Time Formats**
</br>

| ChronoField or Purpose | Examples |
| :--------------------- | :------- |
| ERA | G: AD, GGGG: Anno Domini, GGGGG: A |
| YEAR_OF_ERA | yy:69, yyyy:1969 |
| MONTH_OF_YEAR | M:7, MM: 07, MMM: Jul, MMMM: July, MMMMM: J |
| DAY_OF_MONTH | d: 6, dd: 06 |
| DAY_OF_WEEK | e: 3, E: Wed, EEEE: Wednesday, EEEEE: W |
| HOUR_OF_DAY | H: 9, HH: 09 |
| CLOCK_HOUR_OF_AM_PM | K: 9, KK: 09 |
| AMPM_OF_DAY | a: AM |
| MINUTE_OF_HOUR | mm: 02 |
| SECOND_OF_MINUTE | ss: 00 |
| NANO_OF_SECOND | nnnnnn: 000000 |
| Time zone ID | W: America/New_York |
| Time zone name | z: EDT, zzzz: Eastern Daylight Time |
| Zone offset | x: -04, xx: -0400, xxx: -04:00, XXX: same, but use Z for zero |
| Localized zone offset | O: GMT-4, OOOO: GMT-04:00|

To parse a date/time value from a string, use one of the static `parse` method. 
```Java
LocalDate churchsBirthday = LocalDate.parse("1903-06-14");
ZonedDateTime apollo1Launch = ZonedDateTime.parse("1969-07-16 03:32:00-0400"), DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ssxx"));
```
The first call uses the standard `ISO_LOCAL_DATE` formatter, the second one a custom formatter. 

## Interoperating with Legacy Code

`Instant` is a close analog to `java.util.Date`. `Date` has 2 added methods: 1. `toInstant`: converts a `Date` to an `Instant` and 2. `from`: converts in the other direction. 

`ZonedDateTime` is a close analog to `java.util.GregorianCalendar`. `GregorianCalendar` has gained conversion methods too: 1. `toZonedDateTime`: converts a `GregorianCalendar` to a `ZonedDateTime` and 2. `from`: converts in the other direction. 

**Conversions between java.time Classes and Legacy Classes**
</br>

| Classes | To Legacy Class | From Legacy Class |
| :------ | :-------------- | :---------------- |
| Instant <-> java.util.Date | Date.from(instant) | date.toInstant() |
| ZonedDateTime <-> java.util.GregorianCalendar | GregorianCalendar.from(zonedDateTime) | cal.toZonedDateTime() |
| Instant <-> java.sql.Timestamp | TimeStamp.from(instant) | timestamp.toInstant() |
| LocalDateTime <-> java.sql.Timestamp | Timestamp.valueOf(localDateTime) | timestamp.toLocalDateTime() |
| LocalDate <-> java.sql.Date | Date.valueOf(localDate) | date.toLocalDate() |
| LocalTime <-> java.sql.Time | Time.valueOf(localTime) | time.toLocalTime() |
| DateTimeFormatter <-> java.text.DateFormat | formatter.toFormat() | None |
| java.util.TimeZone <-> ZoneId | Timezone.getTimezone(id) | timeZone.toZoneId() |