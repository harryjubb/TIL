# Snippets

## Python

### Decorator to time a function

```python3
import time
import functools

def time_function(function):

    @functools.wraps(function)
    def wrapped(*args, **kwargs):
        t0 = time.time()
        result = function(*args, **kwargs)
        t1 = time.time()
 
        print(f"Timed {function.__name__}: {t1-t0:.5f}")
        return result

    return wrapped
```
