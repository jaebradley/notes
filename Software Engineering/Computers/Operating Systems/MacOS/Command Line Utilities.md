# Command Line Utilities

* `pbcopy` / `pbpaste` - copy and paste from clipboard
* `date`
  * `date -u` for UTC date or `TZ=UTC date`
  * `date +%s` for seconds from UTC epoch
  * `date +%s | date` is current time 
* `networkQuality`
  * `-v` verbose output
  * `-s` run tests sequentially instead of parallel upload/download
* `caffeinate` - keeps Mac awake until `caffeinate` is terminated
* `mdfind` - Spotlight terminal search
  * `-0` `NUL` character after each result so it can be used with `xargs -0`
  * `-live` provides live-updates to the files matching the query
    * `find` is cancelled by typing `Ctrl + C`
  * `-onlyin <directory>`
  * `-literal` or `-interpret` (as if the user had typed the string into the `Spotlight` menu)
  * `kind` values (subset)
    * `applications`
    * `audio`
    * `bookmark`
    * `contact`
    * `email`
    * `folder`
    * `event`
    * `todo`
    * `image`
    * `movies`
    * `pdf`
    * `preferenes`
* `cal` - displays calendar
  * `-month` - `1`-indexed month count or abbreviation or full name
* `ncal` - displays calendar but with the days of the week on the left hand side
  * `-w` - prints week number as last row in column, not available for `cal`
