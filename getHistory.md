

## interval
* seconds: **1s 2s 3s** ...
  * when S is in upper case, these special value means align interval to minute
    * **1S 5S 10S 15S 30S**
* minutes: **1m 2m 3m** ...
  * when M is in upper case, these special value means align interval to hour
    * **1M 5M 10M 15M 30M**
* hours: **1h 2h 3h** ...
  * when H is in upper case, these special value means align interval to day
    * **1H 2H 3H 4H 6H 12H**
* days: **1d 2d 3d** ...
  * D and d have same meaning
* weeks: **1w 2w 3w** ...
  * W and w have same meaning
* months: **1n 2n 3n** ...
  * when N is in upper case, these special value means align interval to year
    * **1N 2N 3N 4N 6N**
* years: **1y 2y 3y** ...
   * Y and y have same meaning
* other values
  * **none**: don't use any rollup, return all values within the time range
  * **default**: responder choose a interval based on the time range