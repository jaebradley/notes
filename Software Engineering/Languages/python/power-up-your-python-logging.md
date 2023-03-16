# [Power Up Your Python Logging](https://medium.com/better-programming/power-up-your-python-logging-6dd3ed38f322)

* Best practice is to name loggers like `logging.getLogger(__name__)` as each file will now have it's own unique logger name
* `Logger`'s core job is to send `LogRecord`s out to different handlers
  * So `logger.info` will emit a `LogRecord` "event" with a specific `level` attached
* `Handlers` control where `LogRecord`s go
  * `StreamHandler` will send output to standard out while file handler send info to a log file
  * Handlers can be attached to logger instances like

```python
file_handler = logging.FileHandler('some-log-file.log')
logger.addHandler(file_handler)
```

* `QueueHandler` and `QueueListener` are used to prevent the main thread from being blocked by `LogRecord` creation
  * The `QueueHandler` puts the message in a queue that lives on a separate thread
  * The `QueueListener` sends the `LogRecord` to all the other handlers
