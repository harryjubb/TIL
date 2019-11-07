# TIL
Today I Learned

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

```
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
