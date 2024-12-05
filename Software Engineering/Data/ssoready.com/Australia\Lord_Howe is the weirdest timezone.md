# [Australia/Lord_Howe is the weirdest timezone](https://ssoready.com/blog/engineering/truths-programmers-timezones/)

* Earth's rotation is slowing down and days are getting (ever so slightly) longer
* To correct for that the Internationl Earth Rotation and Reference Systems Service (IERS) inserts an extra second in a day to make clocks go slower
  * This is called a leap second
* UTC incorporates leap seconds - UT1 is UTC with the leap seconds removed

## Weird Time Zones

* `Asia/Kathmandu` is on 5 hour, 45 minute offset ahead of UTC
* Filesystem includes a database called the IANA Timezone Database
  * These files, and the paths to the files, act as timezone identifiers
  * `/usr/share/zoneinfo/America/Los_Angeles`
* The "designator" for Kathmandu is `+0545`
* `ChST` (Chamorro Standard Time) is the only designator with a lowercase letter
  * Appears in `Pacific/Saipan`

## How are timezones with DST represented

```
Europe/Athens
EET-2EEST,M3.5.0/3,M10.5.0/4

Standard time is called EET, it’s 2 hours ahead of UTC. 
DST is called EEST (it’s 3 hours ahead, an implicit default relative to standard time).
Start DST in month 3 on the last instance of (5) day 0 (Sunday) in that month, at 3am local (/3).
End DST on month 10 on the last Sunday at 4am local (5.0/4).
```

## Africa/Casablanca and Asia/Gaza follow the moon, but timezones follow the sun

* Morocco and Gaza observe daylight savings based on Ramadan
