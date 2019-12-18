# TIL
Today I Learned

## 2019-12-18

### F-strings are the fastest string interpolation method

Tags: `python` `python3.8`

F-strings outperform every other method. Interestingly, concatenation outperforms `.format`.

https://twitter.com/raymondh/status/1205969258800275456

## 2019-12-11

### Default override configuration

Tags: `docker` `docker-compose`

`docker-compose.override.yml` is read by `docker-compose` by default, laid on top of `docker-compose.yml`.

Using `docker-compose.override.yml` allows for use cases where, for example, an image is built locally for development, but pulled in production: the build (placed in `docker-compose.override.yml`) can be avoided entirely with `docker-compose -f docker-compose.yml -f docker-compose.production.yml` to exclude the `override` entirely.

More in the [compose documentation](https://docs.docker.com/compose/extends/#multiple-compose-files).

## 2019-12-02

### Quickly jump through changes

Tags: `vim` `neovim`

Use `g;` and `g,` to quickly jump backwards and forwards through the history of cursor positions of changes.

See `:h g;`

## 2019-11-21

### Browser-native toggles, modals, and progress bars

Tags: `html`

HTML 5.2 includes `<summary>`, `<details>`, `<dialog>`, `<meter>`, and `<progress>` tags, providing native alternatives to many components usually supplied by libraries or frameworks.

Via [AppsFlyer](https://medium.com/appsflyer/10-superpowers-html5-gives-you-and-you-are-not-using-fbbf5824bba6)

## 2019-11-20

### Flatten nested arrays natively

Tags: `javascript` `ES2019`

ES2019 adds `Array.prototype.flat`, which you can use to [flatten arrays to a supplied depth](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/flat).

The proposal is [Stage 4](https://github.com/tc39/proposal-flatMap) along with `flatMap`.

## 2019-11-19

### Use new `typing` features in older versions of Python

Tags: `python` `python3.5` `python3.6` `python3.7` `python3.8` `typing`

New accepted or experimental `typing` PEPs are backported to older versions of Python that already included `typing` in the standard library on a provisional basis, via the [`typing_extensions` PyPI package](https://pypi.org/project/typing-extensions/).

```pip install typing-extensions```

```python
#!/usr/bin/python3.6

from typing_extensions import Literal
```

`typing_extensions` allows you to use e.g. types added in Python 3.8 in Python 3.7 or 3.6, for example `typing.Literal`. 

For versions older than 3.5 (before `typing` was added to the standard library), there is also the `typing` module on PyPI to backport `typing` to those versions.

### Typed dictionaries

Tags: `python` `python3.8` `typing`

[PEP-589]() adds support for typing dictionaries requiring a fixed set of string keys with specific value types.

Example from the [Python docs](https://docs.python.org/3/library/typing.html#typing.TypedDict):

```python3
class Point2D(TypedDict):
    x: int
    y: int
    label: str

a: Point2D = {'x': 1, 'y': 2, 'label': 'good'}  # OK
b: Point2D = {'z': 3, 'label': 'bad'}           # Fails type check
```

### Literal typing

Tags: `python` `python3.8` `typing`

[PEP-586](https://www.python.org/dev/peps/pep-0586/) adds support for type annotations where literal values are expected.

Example from the PEP:

```python3
from typing import Literal

def accepts_only_four(x: Literal[4]) -> None:
    pass

accepts_only_four(4)   # OK
accepts_only_four(19)  # Rejected
```

See also the [Python docs](https://docs.python.org/3/library/typing.html#typing.Literal).

## 2019-11-08

### Safely parse Python literals from untrusted input strings

Tags: `python`

[`ast.literal_eval`](https://docs.python.org/3.7/library/ast.html#ast.literal_eval) can be used to safely parse strings as a limited set of Python literals.

## 2019-11-06

### Profile Python with multithreading support

Tags: `python`

Yappi can profile Python across multiple threads:

https://github.com/sumerc/yappi

## 2019-11-02

### Docker images on different architectures

Tags: `docker` `raspberrypi`

Feels a little obvious: Docker images (for example on DockerHub) that were built for one processing architecture may throw errors on another one; for example on Raspberry Pis using Arm processors for x86 built images.

The solution is to build the image locally on the Pi, wherein the build will be specific to the correct architecture.

## 2019-10-30

### Nicer async in Python

Tags: `python` `python3.8` `async`

`asyncio.run`, a much nicer interface to managing an async event loop, is stable in Python 3.8.

```python3
async def main():
    await asyncio.sleep(1)
    print('hello')

asyncio.run(main())
```

https://docs.python.org/3/whatsnew/3.8.html#asyncio

https://docs.python.org/3/library/asyncio-task.html#asyncio.run

Via [@redraw](https://github.com/redraw)

### Create a URL for a `File` or `Blob`

Tags: `javascript` `frontend`

You can turn a `Blob` or similar into a URL for download with `createObjectURL`.

https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL

https://gist.github.com/danallison/3ec9d5314788b337b682

## 2019-10-29

### Performance-orientated timing

Tags: `python` `performance`

[`time.perf_counter`](https://docs.python.org/3/library/time.html#time.perf_counter) is an equivalent to `time.time` that has the highest possible resolution for performance measuring purposes.

## 2019-10-28

### Edit files on a remote server locally using Vim

Tags: `vim` `remote`

```
vim scp://server/file
```

https://medium.com/usevim/vim-101-editing-remote-files-a6d2f9c8d9fb

### PyClean: Quickly remove Python cache files from a project

Tags: `python`

Remove cache files for all sub-directories in the current directory:

```
pyclean .
```

http://manpages.ubuntu.com/manpages/trusty/man1/py3clean.1.html

## 2019-10-07

### Built-in statistics functions

Tags: `python` `statistics`

Python has a built-in statistics module containing basic statistical functions:

https://docs.python.org/3/library/statistics.html
