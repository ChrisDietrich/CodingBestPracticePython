# Best Practice when coding in Python

* Target Python 3, not Python 2
* Use [f-strings](https://www.python.org/dev/peps/pep-0498/) where appropriate


## Interpreter (shebang) and character encoding
Use these as the first lines in your module:
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

"""
Calculates the ColorHashes for each entry in the urlscan database table.
"""
```

## Logging

Use [Python logging](https://docs.python.org/3/library/logging.html) instead of `print`. Add this to the top of your module and use `logging.info(...)` and similar to provide information about what is being done:
```python
# Initialize logging
import os, logging
# Each log line includes the date and time, the log level, the current function and the message
formatter = logging.Formatter('%(asctime)s %(levelname)-8s %(funcName)-30s %(message)s')
# The log file is the same as the module name plus the suffix ".log"
# i.e.: calculate.py -> calculate.py.log
fh = logging.FileHandler("%s.log" % (os.path.basename(__file__)))
sh = logging.StreamHandler()
fh.setLevel(logging.DEBUG)      # set the log level for the log file
fh.setFormatter(formatter)
sh.setFormatter(formatter)
sh.setLevel(logging.INFO)       # set the log level for the console
logger = logging.getLogger(__name__)
logger.addHandler(fh)
logger.addHandler(sh)
logger.setLevel(logging.DEBUG)
logger.propagate = False
```

* Here is the [list of log levels](https://docs.python.org/3/howto/logging.html#logging-levels)

Here are some example calls. Use `logger.exception()` in an outer `except` block because that will automatically log the stack trace in addition to the log message.
```python
    ...
    logger.warning(f"image.mode={image.mode} for uuid={uuid} in image_path={image_path} is not in (RGB, RGBA)")
    ...
    try:
        ...
        logger.debug(f"Successfully processed uuid={uuid}")
    except:
        logger.exception(f"Error during calculation for batch_index={batch_index} uuid={uuid}")
```


