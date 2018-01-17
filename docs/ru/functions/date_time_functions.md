# Функции для работы с датами и временем

Поддержка часовых поясов

Все функции по работе с датой и временем, для которых это имеет смысл, могут принимать второй, необязательный аргумент - имя часового пояса. Пример: Asia/Yekaterinburg. В этом случае, они используют не локальный часовой пояс (по умолчанию), а указанный.

```sql
SELECT
    toDateTime('2016-06-15 23:00:00') AS time,
    toDate(time) AS date_local,
    toDate(time, 'Asia/Yekaterinburg') AS date_yekat,
    toString(time, 'US/Samoa') AS time_samoa
```

```text
┌────────────────time─┬─date_local─┬─date_yekat─┬─time_samoa──────────┐
│ 2016-06-15 23:00:00 │ 2016-06-15 │ 2016-06-16 │ 2016-06-15 09:00:00 │
└─────────────────────┴────────────┴────────────┴─────────────────────┘
```

Поддерживаются только часовые пояса, отличающиеся от UTC на целое число часов.

## toYear
Переводит дату или дату-с-временем в число типа UInt16, содержащее номер года (AD).

## toMonth
Переводит дату или дату-с-временем в число типа UInt8, содержащее номер месяца (1-12).

## toDayOfMonth
Переводит дату или дату-с-временем в число типа UInt8, содержащее номер дня в месяце (1-31).

## toDayOfWeek
Переводит дату или дату-с-временем в число типа UInt8, содержащее номер дня в неделе (понедельник - 1, воскресенье - 7).

## toHour
Переводит дату-с-временем в число типа UInt8, содержащее номер часа в сутках (0-23).
Функция исходит из допущения, что перевод стрелок вперёд, если осуществляется, то на час, в два часа ночи, а перевод стрелок назад, если осуществляется, то на час, в три часа ночи (что, в общем, не верно - даже в Москве два раза перевод стрелок был осуществлён в другое время).

## toMinute
Переводит дату-с-временем в число типа UInt8, содержащее номер минуты в часе (0-59).

## toSecond
Переводит дату-с-временем в число типа UInt8, содержащее номер секунды в минуте (0-59).
Секунды координации не учитываются.

## toMonday
Округляет дату или дату-с-временем вниз до ближайшего понедельника.
Возвращается дата.

## toStartOfMonth
Округляет дату или дату-с-временем вниз до первого дня месяца.
Возвращается дата.

## toStartOfQuarter
Округляет дату или дату-с-временем вниз до первого дня квартала.
Первый день квартала - это одно из 1 января, 1 апреля, 1 июля, 1 октября.
Возвращается дата.

## toStartOfYear
Округляет дату или дату-с-временем вниз до первого дня года.
Возвращается дата.

## toStartOfMinute
Округляет дату-с-временем вниз до начала минуты.

## toStartOfFiveMinute
Округляет дату-с-временем вниз до начала пятиминутного интервала.

## toStartOfFifteenMinutes
Округляет дату-с-временем вниз до начала пятнадцатиминутного интервала.

Замечание: если вам нужно округлить дату-с-временем до какого-либо другого количества секунд, минут или часов, вы можете перевести её в число с помощью функции toUInt32, затем округлить число с помощью функции intDiv и умножения, а затем перевести обратно, с помощью функции toDateTime.

## toStartOfHour
Округляет дату-с-временем вниз до начала часа.

## toTime
Переводит дату-с-временем на некоторую фиксированную дату, сохраняя при этом время.

## toRelativeYearNum
Переводит дату-с-временем или дату в номер года, начиная с некоторого фиксированного момента в прошлом.

## toRelativeMonthNum
Переводит дату-с-временем или дату в номер месяца, начиная с некоторого фиксированного момента в прошлом.

## toRelativeWeekNum
Переводит дату-с-временем или дату в номер недели, начиная с некоторого фиксированного момента в прошлом.

## toRelativeDayNum
Переводит дату-с-временем или дату в номер дня, начиная с некоторого фиксированного момента в прошлом.

## toRelativeHourNum
Переводит дату-с-временем в номер часа, начиная с некоторого фиксированного момента в прошлом.

## toRelativeMinuteNum
Переводит дату-с-временем в номер минуты, начиная с некоторого фиксированного момента в прошлом.

## toRelativeSecondNum
Переводит дату-с-временем в номер секунды, начиная с некоторого фиксированного момента в прошлом.

## now

Принимает ноль аргументов и возвращает текущее время на один из моментов выполнения запроса.
Функция возвращает константу, даже если запрос выполнялся долго.

## today
Принимает ноль аргументов и возвращает текущую дату на один из моментов выполнения запроса.
То же самое, что toDate(now())

## yesterday
Принимает ноль аргументов и возвращает вчерашнюю дату на один из моментов выполнения запроса.
Делает то же самое, что today() - 1.

## timeSlot
Округляет время до получаса.
Эта функция является специфичной для Яндекс.Метрики, так как пол часа - минимальное время, для которого, если соседние по времени хиты одного посетителя на одном счётчике отстоят друг от друга строго более, чем на это время, визит может быть разбит на два визита. То есть, кортежи (номер счётчика, идентификатор посетителя, тайм-слот) могут использоваться для поиска хитов, входящий в соответствующий визит.

## timeSlots(StartTime, Duration)
Для интервала времени, начинающегося в StartTime и продолжающегося Duration секунд, возвращает массив моментов времени, состоящий из округлений вниз до получаса точек из этого интервала.
Например, `timeSlots(toDateTime('2012-01-01 12:20:00'), toUInt32(600)) = [toDateTime('2012-01-01 12:00:00'), toDateTime('2012-01-01 12:30:00')]`.
Это нужно для поиска хитов, входящих в соответствующий визит.