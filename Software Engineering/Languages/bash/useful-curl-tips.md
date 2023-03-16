# [Useful cURL tips](https://dannyda.com/2020/01/20/useful-curl-tips-download-upload-post-proxy-header-download-in-chunks-etc/#section2)

* `-#` option downloads with progress bar
* `--limit-rate 500k` can limit the download speed
* `-L` switch follows redirects
* Can add a timeout by using `-m 60` to timeout after a minute
* `-u username:password`
* `-F <path to file>` to upload a file (can specify multiple `_F` options to upload multiple files)
* Can download in chunks by specifying `--range` value like `--range 0-99999999` (first 100MB)
* Specify user agent with `-A` flag
* Specify cookie using `-b name=value;name2=value2`
* `curl -d "key1=value1" -d "key2=value2" -X POST` - `Content-Type` header value will be `application/x-www-form-urlencoded`
* `curl -F 'file=@/path/to/binary/file'` to upload a binary file
  * `Content-Type` header will be `multipart/form-data'`
* `-G` for `GET` request when combining with `-d`
* `--data-urlencode` is similar to `-d` but with url encoding
* `-I` or `--head` sends a `HEAD` request
* `-v` for verbose output
* `--trace - ` to pass request details to standard output 

