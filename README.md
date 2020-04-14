# Adhan JavaScript

[![badge-version][]][npm] [![badge-travis][]][travis] [![badge-cov][]][codecov]

Adhan JavaScript is a well tested and well documented library for calculating Islamic prayer times in JavaScript using Node or a web browser.

All astronomical calculations are high precision equations directly from the book [“Astronomical Algorithms” by Jean Meeus](http://www.willbell.com/math/mc1.htm). This book is recommended by the Astronomical Applications Department of the U.S. Naval Observatory and the Earth System Research Laboratory of the National Oceanic and Atmospheric Administration.

Implementations of Adhan in other languages can be found in the parent repo [Adhan](https://github.com/batoulapps/Adhan).

## Installation

Adhan was designed to work in the browser and in Node.js

### Browser

Simply include Adhan.js in your HTML page

```
<script src="Adhan.js"></script>
<script>
    var prayerTimes = new adhan.PrayerTimes(coordinates, date, params);
</script>
```

### Node

```
npm install adhan
```

```
var adhan = require('adhan')
var prayerTimes = new adhan.PrayerTimes(coordinates, date, params);
```

## Migration

Migrating from version 3.x? Read the [migration guide](MIGRATION.md)

## Usage

To get prayer times initialize a new `PrayerTimes` object passing in coordinates,
date, and calculation parameters.

```js
var prayerTimes = new adhan.PrayerTimes(coordinates, date, params);
```

### Initialization parameters

#### Coordinates

Create a `Coordinates` object with the latitude and longitude for the location
you want prayer times for.

```js
var coordinates = new adhan.Coordinates(35.78056, -78.6389);
```

#### Date

The date parameter passed in should be an instance of the JavaScript `Date`
object. The year, month, and day values need to be populated. All other
values will be ignored. The year, month and day values should be for the local date
that you want prayer times for. These date values are expected to be for the Gregorian calendar.

```js
var date = new Date();
var date = new Date(2015, 11, 1);
```

#### Calculation parameters

The rest of the needed information is contained within the `CalculationParameters` object.
Instead of manually initializing this object it is recommended to use one of the pre-populated
objects in the `CalculationMethod` object. You can then further
customize the calculation parameters if needed.

```js
var params = adhan.CalculationMethod.MuslimWorldLeague();
params.madhab = adhan.Madhab.Hanafi;
params.adjustments.fajr = 2;
```

| Property | Description |
| --------- | ----------- |
| method    | CalculationMethod name |
| fajrAngle | Angle of the sun used to calculate Fajr |
| ishaAngle | Angle of the sun used to calculate Isha |
| ishaInterval | Minutes after Maghrib (if set, the time for Isha will be Maghrib plus ishaInterval) |
| madhab | Value from the Madhab object, used to calculate Asr |
| highLatitudeRule | Value from the HighLatitudeRule object, used to set a minimum time for Fajr and a max time for Isha |
| adjustments | Object with custom prayer time adjustments (in minutes) for each prayer time |

#### CalculationMethod

| Value | Description |
| ----- | ----------- |
| adhan.CalculationMethod.MuslimWorldLeague() | Muslim World League. Fajr angle: 18, Isha angle: 17 |
| adhan.CalculationMethod.Egyptian() | Egyptian General Authority of Survey. Fajr angle: 19.5, Isha angle: 17.5 |
| adhan.CalculationMethod.Karachi() | University of Islamic Sciences, Karachi. Fajr angle: 18, Isha angle: 18 |
| adhan.CalculationMethod.UmmAlQura() | Umm al-Qura University, Makkah. Fajr angle: 18, Isha interval: 90. *Note: you should add a +30 minute custom adjustment for Isha during Ramadan.* |
| adhan.CalculationMethod.Dubai() | Method used in UAE. Fajr angle: 18.2, Isha angle: 18.2. |
| adhan.CalculationMethod.Qatar() | Modified version of Umm al-Qura used in Qatar. Fajr angle: 18, Isha interval: 90. |
| adhan.CalculationMethod.Kuwait() | Method used by the country of Kuwait. Fajr angle: 18, Isha angle: 17.5 |
| adhan.CalculationMethod.MoonsightingCommittee() | Moonsighting Committee. Fajr angle: 18, Isha angle: 18. Also uses seasonal adjustment values. |
| adhan.CalculationMethod.Singapore() | Method used by Singapore. Fajr angle: 20, Isha angle: 18. |
| adhan.CalculationMethod.NorthAmerica() | Referred to as the ISNA method. This method is included for completeness but is not recommended. Fajr angle: 15, Isha angle: 15 |
| adhan.CalculationMethod.Other() | Fajr angle: 0, Isha angle: 0. This is the default value for `method` when initializing a `CalculationParameters` object. |

#### Madhab

| Value | Description |
| ----- | ----------- |
| adhan.Madhab.Shafi | Earlier Asr time |
| adhan.Madhab.Hanafi | Later Asr time |

#### HighLatitudeRule

| Value | Description |
| ----- | ----------- |
| adhan.HighLatitudeRule.MiddleOfTheNight | Fajr will never be earlier than the middle of the night and Isha will never be later than the middle of the night |
| adhan.HighLatitudeRule.SeventhOfTheNight | Fajr will never be earlier than the beginning of the last seventh of the night and Isha will never be later than the end of the first seventh of the night |
| adhan.HighLatitudeRule.TwilightAngle | Similar to SeventhOfTheNight, but instead of 1/7, the fraction of the night used is fajrAngle/60 and ishaAngle/60 |


### Prayer Times

Once the `PrayerTimes` object has been initialized it will contain values
for all five prayer times and the time for sunrise. The prayer times will be
Date object instances initialized with UTC values. You will then need to format
the times for the correct timezone. You can do that by using a timezone aware 
date formatting library like [moment](https://momentjs.com/docs/).

```js
moment(prayerTimes.fajr).tz('America/New_York').format('h:mm A');
```

### Full Example

```js
var date = new Date();
var coordinates = new adhan.Coordinates(35.78056, -78.6389);
var params = adhan.CalculationMethod.MuslimWorldLeague();
params.madhab = adhan.Madhab.Hanafi;
var prayerTimes = new adhan.PrayerTimes(coordinates, date, params);

var fajrTime = moment(prayerTimes.fajr).tz('America/New_York').format('h:mm A');
var sunriseTime = moment(prayerTimes.sunrise).tz('America/New_York').format('h:mm A');
var dhuhrTime = moment(prayerTimes.dhuhr).tz('America/New_York').format('h:mm A');
var asrTime = moment(prayerTimes.asr).tz('America/New_York').format('h:mm A');
var maghribTime = moment(prayerTimes.maghrib).tz('America/New_York').format('h:mm A');
var ishaTime = moment(prayerTimes.isha).tz('America/New_York').format('h:mm A');
```

### Convenience Utilities

The `PrayerTimes` object has functions for getting the current prayer and the next prayer. You can also get the time for a specified prayer, making it
easier to dynamically show countdowns until the next prayer.

```js
var prayerTimes = new adhan.PrayerTimes(coordinates, date, params);

var current = prayerTimes.currentPrayer();
var next = prayerTimes.nextPrayer();
var nextPrayerTime = prayerTimes.timeForPrayer(next);
```

### Sunnah Times

The Adhan library can also calulate Sunnah times. Given an instance of `PrayerTimes`, you can get a `SunnahTimes` object with the times for Qiyam.

```js
var sunnahTimes = new adhan.SunnahTimes(prayerTimes);
var middleOfTheNight = moment(sunnahTimes.middleOfTheNight).tz('America/New_York').format('h:mm A');
var lastThirdOfTheNight = moment(sunnahTimes.lastThirdOfTheNight).tz('America/New_York').format('h:mm A');
```

### Qibla Direction

Get the direction, in degrees from North, of the Qibla from a given set of coordinates.

```js
var coordinates = new adhan.Coordinates(35.78056, -78.6389);
var qiblaDirection = adhan.Qibla(coordinates);
```

## Contributing

Adhan is made publicly available to provide a well tested and well documented library for Islamic prayer times to all 
developers. We accept feature contributions provided that they are properly documented and include the appropriate 
unit tests. We are also looking for contributions in the form of unit tests of of prayer times for different 
locations, we do ask that the source of the comparison values be properly documented.

## License

Adhan is available under the MIT license. See the LICENSE file for more info.

[badge-version]: https://img.shields.io/npm/v/adhan.svg
[badge-travis]: https://travis-ci.org/batoulapps/adhan-js.svg?branch=master
[badge-cov]: https://codecov.io/gh/batoulapps/adhan-js/branch/master/graph/badge.svg
[travis]: https://travis-ci.org/batoulapps/adhan-js
[npm]: https://www.npmjs.org/package/adhan
[codecov]: https://codecov.io/gh/batoulapps/adhan-js
