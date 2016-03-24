title: 'Java SE 8 For the Really Impatient, Note 7'
date: 2016-03-24 15:29:00
categories:
  - Coding
  - Reading Note
tags:
  - Java
---

Chapter 4 JavaFx is skipped for now. 

# Chapter 5 The New Date and Time API

Learn what makes time computations so vexing, and how the new Data and Time API solves these issues. 

Key points:
* All `java.time` objects are immutable
* All `Instant` is a point on the time line (similar to `Date`)
* In Java time, each day has exactly 86,400 seconds (i.e, no leap seconds)
* A `Duration` is the difference between two instants
* `LocalDateTime` has no time zone information
* `TemporalAdjuster` methods handle common calendar computations, such as finding the first Tuesday of a month
* `ZonedDateTime` is a point in time in a given time zone (similar to `GregorianCalendar`)
* Use a `Period`, not a `Duration`, when advancing zoned time, in order to account for daylight savings time changes
* Use `DateTimeFormatter` to format and parse dates and times

## The Time Line

The Java Date and Time API specification requires that Java uses a time scale that 
* Has 86,400 seconds per day
* Exactly matches the official time at noon each day
* Closely matches it elsewhere, in a precisely defined way

`Instant`: represents a point on the time line.
`Instant.MIN`: go back as far as a billion years.
`Instant.MAX`: December 31 of the year 1,000,000,000
`Instant.now()`: gives the current instant
You can compare two instants with the `equals` and `compareTo` methods in the usual way, so you can use instants as timestamps. 

`Duration.between`: find out the difference between two instants
```Java
// measure the running time of an algorithm
Instant start = Instant.now();
runAlgorithm();
Instant end = Instant.now();
Duration timeElapsed = Duration.between(start, end);
long millis = timeElapsed.toMills();
```

`Duration`: the amount of time between 2 instants. 
You can use `toNanos`, `toMillis`, `toSeconds`, `toMinutes`, `toHours`, `toDays` to get the length of a Duration. 

Durations require more than a `long` for their internal storage. The number of seconds is stored in a `long`, and the number of `nanoseconds` in an additional `int`. 
</br>

| Method | Description |
| :----- | :---------- |
| plus, minus | Adds a duration to, or subtracts a duration from, this `Instant` or `Duration` |
| plusNanos, plusMillis, plusSeconds</br>plusMinutes, plusHours, plusDays | Adds a number of the given time units to this `Instant` or `Duration` |
| minusNanos, minusMillis, minusSeconds</br>minusMinutes, minusHours, minusDays | Subtracts a number of the given time units from this `Instant` or `Duration` |
| multipliedBy, dividedBy, negated | Returns a duration that is obtained by multiplying or dividing this Duration by a given long, or by -1. Note that you can scale only durations, not instants |
| isZero, isNegative | Checks whether this Duration is zero or negative. |

It takes almost 300 years of nanoseconds to overflow a `long`. 

If you want to check whether an algorithm is ten times faster than another
```Java
Duration timeElapsed2 = Duration.between(start2, end2);
boolean overTenTimesFaster = timeElapsed.multipliedBy(10).minus(timeElapsed2).isNegative();
// or timeElapsed.toNanos() * 10 < timeElapsed2.toNanos()
```

The `Instant` and `Duration` classes are immutable, and all methods, such as `multipliedBy` or `minus`, return new instance. 

## Local Dates

Human time: local date/time and zoned time. 

Local date/time has a date and/or time of day, but no associated time zone information. 

Do not use zoned time unless you really want to represent absolute time instances. Birthdays, holidays, shecdule times and so on are best represented as local dates or times. 

`LocalDate`: is a date, with a year, month, and day of the month. Construct one with `now` or `of` static methods.
```Java
LocalDate today = LocalDate.now();
LocalDate alonzosBirthday = LocalDate.of(1903, 6, 14);
alonzosBirthday  = LocalDate.of(1903, Month.JUNE, 14);
```

Months are not zero-based but usual numbers for the month of year. Or you can use the `Month` enumeration. 

**Most useful methods for working with `LocalDate` objects**
</br>

| Method | Description |
| :----- | :---------- |
| now, of | Construct a `LocalDate`, either from the current time or from a given year, month and day |
| plusDays, plusWeeks,</br>plusMonths, plusYears | Adds a number of days, weeks, months or years to this `LocalDate` |
| minusDays, minusWeeks,</br>minusMonths, minusYears | Subtracts a number of days, weeks, months or years to this `LocalDate` |
| plus, minus | Adds or subtracts a `Duration` or `Period` |
| withDayOfMonth, withDayOfyear,</br>withMonth, withYear | Returns a new `LocalDate` with the day of month, day of year, month, or year changed to the given value |
| getDayOfMonth | Gets the day of the month (between 1 and 31) |
| getDayOfYear | Gets the day of the year (between 1 and 366) |
| getDayOfWeek | Gets the day of the week, returning a value of `DayOfWeek` enumeration |
| getMonth, getMonthValue | Gets the month as a value the `Month` enumeration, or as a number between 1 and 12 |
| getYear | Gets the year, between -999,999,999 and 999,999,999 |
| until | Gets the `Period`, or the number of the given `ChronoUnits`, between two dates |
| isBefore, isAfter | Compares the `LocalDate` with another |
| isLeapYear | Returns true if the year is a leap year - if it's divisible by 4 but not by 100, or divisible by 400 |

```Java
// 256th day of the year - Programmer's day
LocalDate programmersDay = LocalDate.of(2014, 1, 1).plusDays(255);
```
`Period`: the difference between two local dates, which expresses a number of elapsed years, months, or days. You can call `birthday.plus(Period.ofYears(1))` to get the birthday of next year. Or `birthday.plusYears(1)`. But `birthday.plus(Duration.ofDays(365))` won't be correct in a leap year. 

`until`: yields the difference between 2 local dates. 
```Java
independenceDay.until(christmas); // 5 months and 21 days
```

To find the number of days, use
```Java
independenceDay.until(christmas, ChronoUnit.DAYS); // 174 days
```

Some methods could potentially create nonexistent dates. Adding one month to January 31 should not yield February 31. Instead of throwing an exception, these meethods return the last valid day of month. 
```Java
// yield Feb. 29, 2016
LocalDate.of(2016, 1, 31).plusMonths(1);
LocalDate.of(2016, 3, 31).minusMonths(1);
```

`getDayOfWeek`: yields the weekday, as a value of the `DayOfWeek` enumeration. `DayOfWeek.MONDAY` has the numerical value 1, and `DayOfWeek.SUNDAY` has the value 7.
```Java
LocalDate.of(1900, 1, 1).getDayOfWeek().getValue(); // 1
```

`DayOfWeek` enumeration has convenience methods `plus` and `minus` to compute weekdays modulo 7. `DayOfWeek.SATURDAY.plus(3)` yields `DayOfWeek.TUESDAY`. 

Different from `LocalDate`, Sunday has value 1 and Saturday has value 7 in `java.util.Calendar`.

`MonthDay`, `YearMonth` and `Year` are to describe partial dates in addition to `LocalDate`. December 25 can be represented as a `MonthDay`. 