# Best Practice when coding in Python

* Target Python 3, not Python 2 (Python 2 will be outdated in 2020)
* Use [f-strings](https://www.python.org/dev/peps/pep-0498/) where appropriate
  * Example 
  ```python
  message = f"Image with uuid={uuid} has color_hash={color_hash} as computed by calculator={str(calculatorInstance)}"
  ```
* Code style: Follow [PEP 8](https://www.python.org/dev/peps/pep-0008/) where sensible
  * Example: Configure your editor to use **4 spaces** when pressing the tabulator key (do not use the tabulator character)
* Limit lines to approx. 100 to 120 characters
* Naming
  * Variables, functions, methods, packages, modules
    * `lower_case_with_underscores`
  * Classes and Exceptions
    * `CapWords`
  * Protected methods and internal functions
    * `_single_leading_underscore(self, ...)`
  * Private methods
    * `__double_leading_underscore(self, ...)`
  * Constants
    * `ALL_CAPS_WITH_UNDERSCORES`


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
fh.setLevel(logging.DEBUG)      # set the log level for the log file
fh.setFormatter(formatter)

# Additionally, we want to display the current log output on stdout.
# This can be archived using an additional stream handler.
sh = logging.StreamHandler()
sh.setFormatter(formatter)
sh.setLevel(logging.INFO)       # set the log level for the console

logger = logging.getLogger(__name__)

# Add the Handler to the logger instance.
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

### Use Child Logger for Each Imported Class or Module

When structuring a python application or a script in multiple modules (or classes), a `ChildLogger` for each
module should be used.

```python

import logging

class MyClass():
  def __init__(self):
    self.__logger = logging.getLogger(__name__).getChild('my_class')
```

## Editor/IDE and code check

* [PyCharm](https://www.jetbrains.com/pycharm/)
* vim (esp. when editing code on a remote server)
* Check your code statically (e.g., syntax) with `pylint -E` which will print errors. If it has no output, everything should be fine.
```bash
# Install pylint via pip
[user@host code]$ pip3 install pylint
...
# Check the calculate.py module for errors
[user@host code]$ pylint -E calculate.py
[user@host code]$
```

