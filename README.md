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
OTTF file consist of version and other 2 major parts [`cues`](#cues) (define week periods and their duration) and [`days`](#days) (define what classes for each day are there in set periods).

```json
{
  "version:"1.0",
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

#### Span
Period object is used to define a timespan of either periods or recesses.

| Field | Datatype | Description                      | Example |
| ----: | -------- | -------------------------------- | ------: |
|  from | `String` | Start time of a class (`HH.mm`). |   06.40 |
|    to | `String` | End time of a class (`HH.mm`).   |   07.20 |

```json
{
  "from": "06.40",
  "to": "07.25"
}
```

#### Class
Class object is used to define a school period in a specific day in a week.

|        Field | Datatype       | Description                                     |   Example |
| -----------: | -------------- | ----------------------------------------------- | --------: |
| substitution | `Boolean`      | True if a substitute host is hosting the class. |     false |
|  examination | `Boolean`      | True if there is an examination planned.        |     false |
|     canceled | `Boolean`      | True if class is canceled.                      |     false |
|         name | `String`       | Name of a class.                                | Chemistry |
| abbreviation | `String`       | Abbreviation of a class name.                   |       CHM |
|     location | `String`       | Where is the class hosted.                      |     Lab 2 |
|        hosts | `String Array` | String array of class hosts.                    |   NileRed |

```json
{
  "substitution": false,
  "examination": false,
  "canceled": false,
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
`Cues` consists of [`periods`](#periods) and [`Recesses`](#recesses).

```json
{
  ...,
  "cues": {
    "periods": {
      "1": {
        "from": "07.30",
        "to": "08.15"
      },
      "2": {
        "from": "08.20",
        "to": "09.05"
      },
      "3": {
        "from": "09.10",
        "to": "09.55"
      }
    },
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

### Periods
Periods are timespans for given classes throughout the day.

#### Structure
`periods` element maps [`span objects`](#span) with corresponding sequence number of a period.

```json
"periods": {
  "1": {
    "from": "07.30",
    "to": "08.15"
  },
  ...
}
```

### Recesses
Recesses throughout the day.

#### Structure
`recesses` element is an array containing [`span objects`](#span).

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
`Days` is a maps day abbreviations (MON, TUE, WED, THU, FRI, SAT, SUN) to corresponding `Day` objects.

Day object consists of `date` (date in ISO-8601 standard), [`classes`](#classes), [`events`](#events) and [`dayevents`](#day-events).

```json
{
  ...,
  "days": {
    "TUE": {
      "date": "2022-02-07",
      "classes": {
        "1": [
          {
            "substitution": false,
            "examination": false,
            "name": "Computer Science",
            "abbreviation": "CS",
            "location": "Computer Lab 4",
            "hosts": [
              "Terry C. Bavis"
            ]
          }
        ],
        "3": [
          {
            "substitution": true,
            "examination": false,
            "name": "Poetry",
            "abbreviation": "POE",
            "location": "Class 34",
            "hosts": [
              "Not Karen Senior"
            ]
          }
        ]
      },
      "events": [],
      "dayevents": []
    }
  }
}
```

### Classes
Classes are classes throughout the day.

#### Structure
`classes` element maps an id of [`span object`](#span) from [`periods`](#periods) with an array of corresponsing [`class objects`](#class) (since there can be multiple classes with the same timespan).

```json
"classes": {
  "1": [
    {
      "substitution": false,
      "examination": true,
      "name": "Mathematics",
      "abbreviation": "MAT",
      "location": "Class 23",
      "hosts": [
        "Joe Schmoe II."
      ]
    }
  ],
  ...
}
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