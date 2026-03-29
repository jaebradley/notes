# [curl man page](https://curl.se/docs/manpage.html#--write-out)

## Variables
* Command line variables are supported using `--variable name=content` syntax
* Can also "import" and access existing environment variables via `--variable %name`
* Imports the variable called `name` 
* Exits with an error if that environment variable is not already set
* Default value can be set like `--variable %name=default`
* Example: `--variable '%USER' --expand-url = "https://example.com/api/{{USER}}/method"`
* When expanding variables, curl supports functions that modify the variable content
  * This includes trimming output (\`trim\`), url-encoded (\`url\`), base64 encoding (\`b64\`), base64 decoding (\`64dec\`), json-quoted (\`json\`)
  * `--variable %HOME --expand-variable fix@{{HOME}}/.secret --expand-data "{{fix:trim:url}}"`
    * Reads contents of the file at `$HOME/.secret`
    * Assigns this content to a variable called `fix`
    * Content is trimmed, and URL-encoded

## Data Option
* `-d`, `--data <data>`
* Can read from a filename like `--data @filename`
  * Carriage returns, newlines, null bytes are stripped out
* Default content-type is `application/x-www-form-urlencoded`

## Dump Header
* `-D`, `--dump-header <filename>` 

## Fail
* `-f`, `--fail`
* Fail with error code `22` and with no response body output when returned HTTP response code is 400 or greater
* Use `fail-with-body` to get both the error code and save the content

## Follow
* `--follow`
* Instructs curl to follow HTTP redirects
* Uses the same method string set with the initial request for status code `307` or `308` but may reset the method string to `GET` for `301`, `302`, and `303`
  * This is slightly different than `--location` which always sets a custom method in all subsequent requests

## Form
* `-F`, `--form <name=content>`
* HTTP method is `POST` and the content-type header is `multipart/form-data`
* Read content from stdin instead of a file by using a single `-` as the filename
* `-F name=John -F height=62cm` would send two text fields to the specified URL
* Can change the content type like `-F "name=John;type=text/foo"`

## Get
* `-G`, `--get`
* All data specified with `--data`, `--data-binary`, `--dataurlencode` is used in an HTTP GET request instead of an HTTP POST request
* The provided data is appended as a query string

## Head
* `-I`, `--head`
* Fetch the headers only

## Header
* `-H`, `--header`
* Adds regular request headers
* Can specify any number of extra headers
* Can add headers from a header file (i.e. the `@filename` syntax)
  * Adds a header for each line in the input file
  * `@-` means curl reads the header file from standard input
* `--proxy-header` needs to be used to send custom headers intended for an HTTP proxy
* Added headers are set in all HTTP requests, even when redirects are followed
  * `Authorization` and `Cookie` headers are explicitly not passed on in HTTP rquests when following redirects to other origins unless the `--location-trusted` option is specified 

## HTTP versions
* `--http1.1` is the default
* `--http2` : for HTTPS, curl negotiates HTTP/2 in the TLS handshake by default
  * For HTTP, curl attempts to upgrade the request to HTTP/2
* `--http3`: Attempt to connect to the host using HTTP/3, but fallback to earlier HTTP versions if the connection establishment fails or is slow
  * Only available for HTTPS

## JSON
* `--json <data>`
* Sends the specified JSON data in a POST request
* Shortcut for `--data-binary --header "Content-Type: application/json" --header "Accept: application/json"`

## Max Time
* `-m`, `--max-time <seconds>`
* Sets the maximum time in seconds for each transfer
* Prevents batch jobs from hanging for hours due to slow networks
* If retrying is enabled, then the maximum time counter is reset each time the transfer is retried

## Output
* `-o`, `--output <file>`
* Writes the output to the specified file instead of standard output
* To discard the response output silently use the `--out-null` option
  * More efficient and portable version of `--output /dev/null`
* There is a `--create-dirs` option to create local directories dynamically

## Silent
* `--silent`
* Use with `-S`, `--show-error` to have curl show an error message if the curl command fails

## Response Headers
* `-i`, `--show-headers` 
* `--dump-header` : use this option to save headers to a separate stream
* To view the request headers, consider using the `--verbose` option

## Progress Meter
* `--no-progress-meter` switch off the progress meter without muting or otherwise affecting warning and informational messages like `--silent`

## Write Out

* `-w`, `--write-out <format>`
* curl displays information to standard output after a completed transfer
* Format is a string that may contain plain text mixed with variables
* Example: `curl -w '%{response_code} {time_total}\n' https://example.com`
  * Output would show the response, as well as the response code and total time, in seconds, that the full operation took

## Verbose
* `>`: header sent by curl
* `<`: header received by curl
* `}`: data sent by curl
* `{`: data received by curl
* `*`: Additional information provided by curl
