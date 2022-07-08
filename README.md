<p align="center">
    <img align="center" src="./assets/icon.png" height="150px">
</p>

<h1 align="center">Open Timetable v1</h1>
<h3 align="center">The first revision of OTTF file format.</h3>
<br>

<div align="center">

[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![MIT License][license-shield]][license-url]

</div>

## About Open Timetable
OTTF file format was created in 2022 by [ClassCompanion Team](https://github.com/ClassCompanion) in order to standardize the way of providing school timetables for applications and websites. It's a JSON file format with strict guidelines in order to keep it as simple as possible.

## File structure in general
OTTF file consist of 2 major parts [`cues`](#cues) (define week periods and their duration) and [`days`](#days) (define what classes for each day are there in set periods).

```json
{
  "cues": {
    ...
  },
  "days": {
    ...
  }
}
```

### Objects
Objects defined by our standard.

**Please note that all timestamps must be in a 24-hour `HH.mm` format and all the dates must be in a `YYYY-MM-DD` (ISO-8601) format.**

#### Period
Period object is used to define a timespan of classes.

| Field | Datatype  | Description                      | Example |
| ----: | --------- | -------------------------------- | ------: |
|    no | `Integer` | Sequence number of a period.     |       1 |
|  from | `String`  | Start time of a class (`HH.mm`). |   06.40 |
|    to | `String`  | End time of a class (`HH.mm`).   |   07.20 |

```json
{
  "no": 1,
  "from": "06.40",
  "to": "07.25"
}
```

#### Recess
Recess object is used to define timespan of recesses.

| Field | Datatype | Description                       | Example |
| ----: | -------- | --------------------------------- | ------: |
|  from | `String` | Start time of a recess (`HH.mm`). |   10.45 |
|    to | `String` | End time of a recess (`HH.mm`).   |   11.05 |

```json
{
  "from": "10.45",
  "to": "11.05"
}
```

#### Class
Class object is used to define a school period in a specific day in a week.

|        Field | Datatype       | Description                                     |   Example |
| -----------: | -------------- | ----------------------------------------------- | --------: |
|           no | `Integer`      | Sequence number of a corresponding period.      |         3 |
| substitution | `Boolean`      | True if a substitute host is hosting the class. |     false |
|  examination | `Boolean`      | True if there is an examination planned.        |     false |
|         name | `String`       | Name of a class.                                | Chemistry |
| abbreviation | `String`       | Abbreviation of a class name.                   |       CHM |
|     location | `String`       | Where is the class hosted.                      |     Lab 2 |
|        hosts | `String Array` | String array of class hosts.                    |   NileRed |

```json
{
  "no": 3,
  "substitution": false,
  "examination": false,
  "name": "Chemistry",
  "abbreviation": "CHM",
  "location": "Lab 2",
  "hosts": [
    "NileRed"
  ]
}
```

#### Event
Speccial occasion during the day.

|    Field | Datatype       | Description                        |                Example |
| -------: | -------------- | ---------------------------------- | ---------------------: |
|     from | `String`       | Start time of the event (`HH.mm`). |                   8.05 |
|       to | `String`       | End time of the event (`HH.mm`).   |                   8.10 |
|    title | `Boolean`      | Title of the event.                |                    OEM |
| location | `String`       | Where the event is hosted.         |              Class 223 |
|    hosts | `String Array` | String array of event hosts.       | Joe Shmoe, Karen Shmoe |

```json
{
  "from": "08.05",
  "to": "08.10",
  "title": "OEM",
  "location": "Class 223",
  "hosts": [
    "Joe Shmoe",
    "Karen Shmoe"
  ]
}
```

#### Day event
Special kind of event that last the whole day.

|    Field | Datatype       | Description                  |    Example |
| -------: | -------------- | ---------------------------- | ---------: |
|    title | `Boolean`      | Title of the event.          |        IPO |
| location | `String`       | Where the event is hosted.   |  Class 124 |
|    hosts | `String Array` | String array of event hosts. | Alex Smith |

```json
{
  "title": "IPO",
  "location": "Class 124",
  "hosts": [
    "Alex Smith"
  ]
}
```

## Cues
`Cues` consists of [`Pre-periods`](#pre-periods), [`Class periods`](#class-periods) and [`Recesses`](#recesses).

```json
{
  "cues": {
    "preperiods": [
      {
        "no": 1,
        "from": "06.40",
        "to": "07.25"
      }
    ],
    "periods": [
      {
        "no": 1,
        "from": "07.30",
        "to": "08.15"
      },
      {
        "no": 2,
        "from": "08.20",
        "to": "09.05"
      },
      {
        "no": 3,
        "from": "09.10",
        "to": "09.55"
      },
      {
        "no": 4,
        "from": "10.00",
        "to": "10.45"
      },
      {
        "no": 5,
        "from": "11.05",
        "to": "11.50"
      }
    ],
    "recesses": [
      {
        "from": "10.45",
        "to": "11.05"
      }
    ]
  },
  ...
}
```

### Pre-periods
Pre-periods are timespans for given classes before the first class.

#### Structure
`preperiods` element is an array containing [`period objects`](#period).

```json
"preperiods": [
  {
    "no": 1,
    "from": "06.40",
    "to": "07.25"
  },
  ...
]
```

### Periods
Periods are timespans for given classes throughout the day.

#### Structure
`periods` element is an array containing [`period objects`](#period).

```json
"periods": [
  {
    "no": 2,
    "from": "08.20",
    "to": "09.05"
  },
  ...
]
```

### Recesses
Recesses throughout the day.

#### Structure
`recesses` element is an array containing [`recess objects`](#recess).

```json
"recesses": [
  {
    "from": "10.45",
    "to": "11.05"
  },
  ...
]
```

## Days
`Days` is a map with day abbreviations (MON, TUE, WED, THU, FRI, SAT, SUN) as keys and `Day` objects as values.

Day object consists of `date` (date in ISO-8601 standard), [`preclasses`](#pre-classes), [`classes`](#classes), [`events`](#events) and [`dayevents`](#day-events).

```json
{
  ...,
  "days": {
    "TUE": {
      "date": "2022-02-07",
      "preclasses": [],
      "classes": [
        {
          "no": 1,
          "substitution": false,
          "examination": false,
          "name": "Computer Science",
          "abbreviation": "CS",
          "location": "Computer Lab 4",
          "hosts": [
            "Terry C. Bavis"
          ]
        },
        {
          "no": 2,
          "substitution": true,
          "examination": false,
          "name": "Poetry",
          "abbreviation": "POE",
          "location": "Class 34",
          "hosts": [
            "Not Karen Senior"
          ]
        }
      ],
      "events": [],
      "dayevents": []
    }
  }
}
```

### Pre-classes
Pre-classes are classes before first class.

#### Structure
`preclasses` element is an array containing [`class objects`](#class).

```json
"preclasses": [
  {
    "no": 1,
    "substitution": false,
    "examination": false,
    "name": "Physical Education",
    "abbreviation": "PE",
    "location": "GYM 1",
    "hosts": [
      "Joe Schmoe",
      "Karen Karen"
    ]
  },
  ...
]
```

### Classes
Classes are classes throughout the day.

#### Structure
`classes` element is an array containing [`class objects`](#class).

```json
"classes": [
  {
    "no": 1,
    "substitution": false,
    "examination": true,
    "name": "Mathematics",
    "abbreviation": "MAT",
    "location": "Class 23",
    "hosts": [
      "Joe Schmoe II."
    ]
  },
  ...
]
```

### Events
Events are special occasions such as school event or holidays.

#### Structure
`events` element is an array containing [`event objects`](#event).

```json
"events": [
  {
    "from": "08.05",
    "to": "08.10",
    "title": "OEM",
    "location": "Class 34",
    "hosts": [
      "Karen Karen Junior"
    ]
  },
  ...
]
```

### Day events
Events are special type of event that lasts a whole day, therefore it doesn't have defined timespan.

#### Structure
`dayevents` element is an array containing [`day event objects`](#day-event).

```json
"dayevents": [
  {
    "title": "IPO",
    "location": "Class 124",
    "hosts": [
      "Alex Smith"
    ]
  },
  ...
]
```

[contributors-shield]: https://img.shields.io/github/contributors/OpenTimeTable/OpenTimetable-v1.svg?style=for-the-badge
[forks-shield]: https://img.shields.io/github/forks/OpenTimeTable/OpenTimetable-v1.svg?style=for-the-badge
[stars-shield]: https://img.shields.io/github/stars/OpenTimeTable/OpenTimetable-v1.svg?style=for-the-badge
[issues-shield]: https://img.shields.io/github/issues/OpenTimeTable/OpenTimetable-v1.svg?style=for-the-badge
[license-shield]: https://img.shields.io/github/license/OpenTimeTable/OpenTimetable-v1.svg?style=for-the-badge

[linux-shield]: https://img.shields.io/badge/Linux-Yes-green?style=for-the-badge&logo=linux
[windows-shield]: https://img.shields.io/badge/Windows-Not%20yet-red?style=for-the-badge&logo=windows
[macos-shield]: https://img.shields.io/badge/MacOs-Not%20yet-red?style=for-the-badge&logo=apple

[contributors-url]: https://github.com/OpenTimeTable/OpenTimetable-v1/graphs/contributors
[forks-url]: https://github.com/OpenTimeTable/OpenTimetable-v1/network/members
[stars-url]: https://github.com/OpenTimeTable/OpenTimetable-v1/stargazers
[issues-url]: https://github.com/OpenTimeTable/OpenTimetable-v1/issues
[license-url]: https://github.com/OpenTimeTable/OpenTimetable-v1/blob/master/LICENSE