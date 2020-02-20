# Temporal.MonthDay

A `Temporal.MonthDay` represents a particular day on the calendar, but without a year.
For example, it could be used to represent a yearly recurring event, like "Bastille Day is on the 14th of July."

If you need to refer to a certain instance of a calendar event, in a particular year, use `Temporal.Date` or even `Temporal.DateTime`.
A `Temporal.MonthDay` can be converted into a `Temporal.Date` by combining it with a year, using the `withYear()` method.

## Constructor

### **new Temporal.MonthDay**(_isoMonth_: number, _isoDay_: number, _disambiguation_: 'constrain' | 'balance' | 'reject' = 'constrain') : Temporal.MonthDay

**Parameters:**
- `isoMonth` (number): A month, ranging between 1 and 12 inclusive.
- `isoDay` (number): A day of the month, ranging between 1 and 31 inclusive.
- `disambiguation` (optional string): How to deal with out-of-range values of the other parameters.
  Allowed values are `constrain`, `balance`, and `reject`.
  The default is `constrain`.

**Returns:** a new `Temporal.MonthDay` object.

Use this constructor if you have the correct parameters for the date already as individual number values, or you need the disambiguation behaviour.
Otherwise, `Temporal.MonthDay.from()`, which accepts more kinds of input, is probably more convenient.

All values are given as reckoned in the [ISO 8601 calendar](https://en.wikipedia.org/wiki/ISO_8601#Dates).

The `disambiguation` parameter works as follows:
- In `constrain` mode (the default), any out-of-range values are clamped to the nearest in-range value.
- In `balance` mode, any out-of-range values are resolved by balancing them with the next highest unit.
- In `reject` mode, the presence of out-of-range values will cause the constructor to throw a `RangeError`.

> **NOTE**: February 29 (Leap day in the ISO 8601 calendar) is a valid value for `Temporal.MonthDay`, even though that date does not occur every year.

Usage examples:
```javascript
// Pi day
md = new Temporal.MonthDay(3, 14)  // => 03-14
// Leap day
md = new Temporal.MonthDay(2, 29)  // => 02-29

// Different disambiguation modes
md = new Temporal.MonthDay(13, 1, 'constrain')  // => 12-01
md = new Temporal.MonthDay(-1, 1, 'constrain')  // => 01-01
md = new Temporal.MonthDay(13, 1, 'balance')  // => 01-01
md = new Temporal.MonthDay(-1, 1, 'balance')  // => 11-01
md = new Temporal.MonthDay(13, 1, 'reject')  // throws
md = new Temporal.MonthDay(-1, 1, 'reject')  // throws
```

## Static methods

### Temporal.MonthDay.**from**(_thing_: string | object) : Temporal.MonthDay

**Parameters:**
- `thing` (string or object): The value representing the desired date.

**Returns:** a new `Temporal.MonthDay` object (or the same object if `thing` was a `Temporal.MonthDay` object.)

This static method creates a new `Temporal.MonthDay` object from another value.
If the value is a string, it must be in ISO 8601 format.
If the value is another `Temporal.MonthDay` object, the same object is returned.
If the value is any other object, it must have `month` and `day` properties, and a `Temporal.MonthDay` will be constructed from them.

Note that any year, time, or time zone part of an ISO 8601 string passed to this function is optional, and will be ignored.

Example usage:
```javascript
md = Temporal.MonthDay.from('08-24');  // => 08-24
md = Temporal.MonthDay.from('2006-08-24');  // => 08-24
md = Temporal.MonthDay.from('2006-08-24T15:43:27');  // => 08-24
md = Temporal.MonthDay.from('2006-08-24T15:43:27Z');  // => 08-24
md = Temporal.MonthDay.from('2006-08-24T15:43:27+01:00[Europe/Brussels]');
  // => 08-24
md === Temporal.MonthDay.from(md)  // => true

md = Temporal.MonthDay.from({month: 8, day: 24});  // => 08-24
md = Temporal.MonthDay.from(Temporal.Date.from('2006-08-24'));
  // => same as above; Temporal.Date has month and day properties
```

### Temporal.MonthDay.**compare**(_one_: Temporal.MonthDay, _two_: Temporal.MonthDay) : number

**Parameters:**
- `one` (`Temporal.MonthDay`): First date to compare.
- `two` (`Temporal.MonthDay`): Second date to compare.

**Returns:** &minus;1, 0, or 1.

Compares two `Temporal.MonthDay` objects.
Returns an integer indicating whether `one` comes before or after or is equal to `two`, assuming they occur in the same year..
- &minus;1 if `one` comes before `two`;
- 0 if `one` and `two` are the same;
- 1 if `one` comes after `two`.

This function can be used to sort arrays of `Temporal.MonthDay` objects.
For example:
```javascript
one = Temporal.MonthDay.from('08-24');
two = Temporal.MonthDay.from('07-14');
three = Temporal.MonthDay.from('02-18');
sorted = [one, two, three].sort(Temporal.MonthDay.compare);
sorted.join(' ');  // => 02-18 07-14 08-24
```

## Properties

### monthDay.**month** : number

### monthDay.**day** : number

The above read-only properties allow accessing each component of the date individually.

Usage examples:
```javascript
md = Temporal.MonthDay.from('08-24');
md.month  // => 8
md.day    // => 24
```

## Methods

### monthDay.**with**(_monthDayLike_: object, _disambiguation_: 'constrain' | 'balance' | 'reject' = 'constrain') : Temporal.MonthDay

**Parameters:**
- `monthDayLike` (object): an object with some or all of the properties of a `Temporal.MonthDay`.
- `disambiguation` (optional string): How to deal with out-of-range values.
  Allowed values are `constrain`, `balance`, and `reject`.
  The default is `constrain`.

**Returns:** a new `Temporal.MonthDay` object.

This method creates a new `Temporal.MonthDay` which is a copy of `monthDay`, but any properties present on `monthDayLike` override the ones already present on `monthDay`.

The disambiguation parameter tells what should happen when out-of-range values are given or when the result would be an invalid month-day combination, such as "June 31":
- In `constrain` mode (the default), any out-of-range values are clamped to the nearest in-range value, so June 31 would become June 30.
- In `balance` mode, an out-of-range value for the day is resolved by balancing them with the next highest unit, so June 31 would become July 1; and an out-of-range value for the month wraps around, so `{month: 13}` would end up as January.
- In `reject` mode, the presence of out-of-range values will cause the constructor to throw a `RangeError`.

> **NOTE:** For the purpose of this method, February is treated as having 29 days, so that it remains possible to construct a `Temporal.MonthDay` for February 29.

Since `Temporal.MonthDay` objects are immutable, use this method instead of modifying one.

Usage example:
```javascript
md = Temporal.MonthDay.from('11-15');
// What's the last day of that month?
md.with({day: 31}, 'constrain')  // => 11-30
Temporal.MonthDay.from('02-01').with({day: 31}, 'constrain');  // => 02-29
```

### monthDay.**toString**() : string

**Returns:** a string in the ISO 8601 date format representing `monthDay`.

This method overrides the `Object.prototype.toString()` method and provides a convenient, unambiguous string representation of `monthDay`.
The string can be passed to `Temporal.MonthDay.from()` to create a new `Temporal.MonthDay` object.

Example usage:
```js
md = Temporal.MonthDay.from('08-24');
md.toString();  // => 08-24
```

### monthDay.**toLocaleString**(_locale_?: string, _options_?: object) : string

**Parameters:**
- `locales` (optional string or array of strings): A string with a BCP 47 language tag with an optional Unicode extension key, or an array of such strings.
- `options` (optional object): An object with properties influencing the formatting.

**Returns:** a language-sensitive representation of `monthDay`.

This method overrides `Object.prototype.toLocaleString()` to provide a human-readable, language-sensitive representation of `monthDay`.

The `locales` and `options` arguments are the same as in the constructor to [`Intl.DateTimeFormat`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/DateTimeFormat).

Example usage:
```js
md = Temporal.MonthDay.from('08-24');
md.toLocaleString();  // => example output: 08-24
md.toLocaleString('de-DE');  // => example output: 24.8.
md.toLocaleString('de-DE', {month: 'long', day: 'numeric'});  // => 24. August
md.toLocaleString('en-US-u-nu-fullwide');  // => ８/２４
```

### monthDay.**withYear**(_year_: number, _disambiguation_: 'constrain' | 'balance' | 'reject' = 'constrain') : Temporal.Date

**Parameters:**
- `year` (number): A year.
- `disambiguation` (optional string): How to deal with out-of-range values.
  Allowed values are `constrain`, `balance`, and `reject`.
  The default is `constrain`.

**Returns:** a `Temporal.Date` object that represents the calendar date of `monthDay` in `year`.

This method can be used to convert `Temporal.MonthDay` into a `Temporal.Date`, by supplying a year to use.
The converted object carries a copy of all the relevant fields of `monthDay`.

The disambiguation parameter tells what should happen when translating a `Temporal.MonthDay` representing February 29 into a non-leap year:
- In `constrain` mode (the default), the result will be February 28 of that year.
- In `balance` mode, the result will be March 1.
- In `reject` mode, the method will throw a `RangeError`.

Usage example:
```javascript
md = Temporal.MonthDay.from('08-24');
md.withYear(2017)  // => 2017-08-24

md = Temporal.MonthDay.from('02-29');
md.withYear(2017, 'constrain')  // => 2017-02-28
md.withYear(2017, 'balance')    // => 2017-03-01
md.withYear(2017, 'reject')     // throws
```