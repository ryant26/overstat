# Overstat [![Build Status](https://travis-ci.org/ryant26/overstat.svg?branch=develop)](https://travis-ci.org/ryant26/overstat) [![npm version](https://badge.fury.io/js/overstat.svg)](https://badge.fury.io/js/overstat)

*Forked from [Oversmash](https://github.com/filp/oversmash)*

API wrapper for Blizzard's Overwatch player stats. Uses promises.

Blizzard does not expose an official API, so this library relies partially on scraping, using [cheerio](https://github.com/cheeriojs/cheerio).  

Please also keep in mind there is no builtin rate-limiting support, so it's on you to use the library responsibly.

## Differences from Oversmash
- Actively maintained (we use this as a core dependency for [fireteam.gg](https://fireteam.gg))
- No ES Modules, No Babel
- Converted async/await to promises for integration with our core project

**Note: See [Releases](https://github.com/ryant26/overstat/releases) for release details**

## Features

- Ability to retrieve basic user information, such as name, portrait, level and accounts
  - Correctly identifies the platform and region for each account
- Ability to retrieve detailed stats for a player, for a given region and platform
  - Includes **all** stats available on playoverwatch.com
  - Includes the player's current competitive rank
  - Includes full list of achievements, with details on which the player has completed
  - Stats are retrieved and grouped automatically per career type (quickplay/competitive), hero, and group (e.g combat, awards, etc)
  - Supports new heroes and new types of stats as they're added, no changes required to the code
- Supports normalizing names and values (e.g converting achievement names to `snake_case`, properly handling floating-point values in stats, etc)
- Minimalist and straightforward API

## Usage

Install through `npm`:

```shell
$ npm install --save Overstat
```

Example:

```js
import overstat from 'overstat'

// Create a new overstat object. `overstat()` accepts an options
// object (see below)
const ow = overstat()

// Get basic details about a user, including their platform/region accounts.
//
ow.player('bob-12345').then(player => {
  console.log(player)
})

// Output:
// { name: 'bob-12345',
//   accounts:
//    [ { level: 440,
//        portrait: 'https://blzgdapipro-a.akamaihd.net/game/unlocks/xyz.png',
//        displayName: 'bob#12345',
//        platform: 'pc' } ] }

// Get detailed stats about a user (for a specific region), including
// achievements unlocked, per-career and per-hero stats, and their
// current competitive rank
ow.playerStats('bob-12345', 'us', 'pc').then(player => {
  console.log(player)
})

// Output:
// { name: 'bob-12345',
//   region: 'us',
//   platform: 'pc',
//   stats:
//    { competitiveRank: 3700,
//      achievements:
//       [ { name: 'centenary', achieved: true },
//         { name: 'level_10', achieved: true },
//         { name: 'level_25', achieved: true },
//         { name: 'level_50', achieved: true },
//         { name: 'undying', achieved: true },
//         { name: 'survival_expert', achieved: true },
//         /* ... etc ... */],
//      quickplay:
//       { all: { /* avg stats across all characters */ }
//         reaper:
//          { combat:
//             { melee_final_blows: 190,
//               solo_kills: 2922,
//               objective_kills: 6592,
//               final_blows: 9519,
//               damage_done: 6897,
//               eliminations: 18456,
//               environmental_kills: 83,
//               multikills: 155 },
//            assists:
//             { healing_done: 1102,
//               recon_assists: 25,
//               teleporter_pads_destroyed: 18 },
//            best:
//             { eliminations_most_in_game: 44,
//               final_blows_most_in_game: 27,
//               damage_done_most_in_game: 17491,
//   /* ... etc ... */
```
### `overstat()` options

The following options are configurable.

```js
{
  // Convert things like achievement names to snake_case
  normalizeNames: true,

  // When set to snake, names are normalized as snake_case; when set to camel,
  // names are lowerCamelCase. Only applies if normalizeNames is enabled.
  normalizeNamesAs: 'snake' || 'camel',

  // Convert values to their correct format, e.g numbers in stats to JS numbers
  // When disabled, all stats values are strings as extracted from Blizzard
  normalizeValues: true,

  // Convert percentage values to ints, e.g '32%' to 32
  percentsToInts: true,

  // Default values to use if platform & region are not passed to the playerStats
  // method.
  defaultRegion: 'us',
  defaultPlatform: 'pc',

  // Used to extract account details from the career link returned from the
  // playoverwatch API
  accountIdentityRegex: /^\/career\/([\w]+)\/.+$/,

  requestOptions: {
    baseURL: 'https://playoverwatch.com/en-us',
    headers: {
      'User-Agent': 'https://github.com/filp/overstat (hi jeff)'
    }
  }
}
```

`requestOptions` are passed directly to `request.defaults` on [request](https://github.com/request/request)

## Debugging

`overstat` uses [debug](https://github.com/visionmedia/debug). Run your code calling overstat with
`DEBUG=overstat` to enable debug logging

## Development 🐝 🐝 🐝

See [`LICENSE.md`](/LICENSE.md) for license information

Contributions are welcome - please follow the style guidelines as enforced by the included `.eslintrc`!
