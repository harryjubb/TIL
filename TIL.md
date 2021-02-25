# TIL
Today I Learned

## 2021-02-25

### Package Python apps in a single executable bundle

Tags: `python`

https://docs.python.org/3/library/zipapp.html

## 2021-02-14

### Language sensitive number formatting

Tags: `javascript` `i18n`

JS has a built-in object for formatting numbers with internationalisation:

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat

## 2021-02-06

### Building the RDKit minimal JS library

Tags: `rdkit` `docker` `wasm` `javascript`

The [minimal RDKit JS build](https://github.com/rdkit/rdkit/tree/master/Code/MinimalLib) (emscripten compiled to WASM) allows a lot of cheminformatics work (including [2D depictions, fingerprint generation and similarity searching](http://rdkit.blogspot.com/2019/11/introducing-new-rdkit-javascript.html)) to be done in the browser.

### Building the library

Using Docker it's easy to build the library using a single [Dockerfile](https://github.com/rdkit/rdkit/blob/master/Code/MinimalLib/docker/Dockerfile).

If the [Dockerfile](https://github.com/rdkit/rdkit/blob/master/Code/MinimalLib/docker/Dockerfile) is placed on it's own in a folder, build with:

```shell
docker build -t rdkitjs . 
```

The build will take some time...  ☕️

The built JS and WASM file can then be copied out of a running instance of the container:

```shell
CONTAINER_ID=$(docker run -d rdkitjs sleep 20)
docker cp $CONTAINER_ID:/src/rdkit/build/Code/MinimalLib/RDKit_minimal.js .
docker cp $CONTAINER_ID:/src/rdkit/build/Code/MinimalLib/RDKit_minimal.wasm .
docker stop $CONTAINER_ID
docker rm $CONTAINER_ID
```

### Using the library

Use in your web app with

```html
<script src="<path/to>/RDKit_minimal.js"></script>
```

It may be a good idea to add cache-buster to the filename, so that the large-ish (~4.3 MB without gzip) RDKit WASM can be cached with a long lifetime.

If you do, you'll need to rename both the JS and WASM file, and in the JS file, find:

```javascript
wasmBinaryFile="RDKit_minimal.wasm"
```

and replace the filename with your altered name.

When loaded, an `initRDKitModule` function is added to `window`. `window.initRDKitModule` returns a `Promise`, which resolves to a handler object for RDKit functionality.

Example Typescript wrapper around `initRDKitModule`:


```typescript
const setupRDKit = async () => {
  let rdkit

  try {
    rdkit = await (window as any).initRDKitModule()
    if (process.env.NODE_ENV !== 'production') {
      console.log('RDKit initialized')
    }
    (window as any).rdkit = rdkit
    return rdkit
  } catch (error) {
    console.error('Error initializing RDKit', error)
  }
}

export default setupRDKit
```

Using the above, check out `window.rdkit` in browser development tools to see the functionality available. `window.rdkit.get_mol` allows you to get a molecule from e.g. SMILES; that `Mol` object provides further methods such as `get_svg`.

## 2021-02-01

### Pi-hole Caddy config with SSL / TLS with Cloudflare

Tags: `caddy` `dns` `cloudflare` `pihole`

Hosting sites on a local network with SSL (using the Cloudflare DNS validation module for ACME / Let'sEncrypt validation) on a [Pi-hole](https://pi-hole.net/) DHCP / DNS managed network with Caddy: can be tricky as making a local DNS record can throw Caddy's DNS validation off.

In this very specific case, rather than giving a limited config:

```
tls {env.ACME_EMAIL} { 
  dns cloudflare {env.CLOUDFLARE_API_TOKEN}
}
```

A full config can be provided giving resolvers to use for DNS validation:

```
example.com {
    root * /var/caddy/example.com
    file_server
    tls {
        issuer acme {
            dir https://acme-staging-v02.api.letsencrypt.org/directory
            dns cloudflare <api_token>
            resolvers 1.1.1.1 1.0.0.1
        }
    }
}
```

https://github.com/caddy-dns/cloudflare/issues/13#issuecomment-731636080

## 2020-11-06

### Site-wide Python install customisations

Tags: `python` `administration`

You can customise a Python install as a system administrator by creating a `sitecustomize` module in the install's `site-packages` directory, and including code to be applied on every Python run there.

https://docs.python.org/3/library/site.html

You can use this to do things like globally opt out of SSL verification (not that you should in the vast majority of cases!)

https://www.python.org/dev/peps/pep-0476/#opting-out

## 2020-10-28

### Quick SSL certificates in a pinch

Tags: `ssl` `letsencrypt` `certbot` `docker`

If you need a certificate quickly (and free) and you're not able to set up automated Let'sEncrypt renewal, you can grab a three month valid certificate by running Certbot manually in Docker, e.g.

```
docker run -it --rm --name certbot \
            -v "$(pwd)/etc:/etc/letsencrypt" \
            -v "$(pwd)/lib:/var/lib/letsencrypt" \
            certbot/certbot -d '*.domain.com' --manual --preferred-challenges dns certonly
```

Certbot will interactively take you through validating via setting a DNS `TXT` record manually. The certificates live in `etc/live/domain.com` on your host system (from the `pwd` bind mount), will last for three months. `fullchain.pem` is the full certificate chain used for web servers, `privkey.pem` is the private key.

## 2020-10-26

### Postgres template databases

Tags: `postgres` `database`

You can update the default template database, `template1`, by running commands against it (e.g. `CREATE EXTENSION`). Subsequent created databases will be based off of the updated `template1`, which is handy for setting defaults for all creating databases to inherit.

If you mess up `template1` somehow, it can be restored to it's original setup from the uneditable `template0` database.

https://www.postgresql.org/docs/13/manage-ag-templatedbs.html

## 2020-06-20

### Dynamically import from folders with Webpack

Tags: `javascript` `typescript` `webpack` `frontend`

If you have a folder of files you want to import at build-time, Webpack can traverse the folder structure for you and import them. For example, to import every Vue file (assuming you're using a Vue Webpack loader):

```javascript
const cache = {}

function importAll (r) {
  r.keys().forEach(key => { cache[key] = r(key) })
}

importAll(require.context('./plugins', true, /\.vue$/))
```

`cache` has key:value pairs of filename: `Module`.

In Vue for example, this can be used to import a large set of components without importing them all individually or maintaining a manifest:

```typescript
const plugins = Object.fromEntries(
  Object.entries(cache).map(([filename, esModule]) => [
    esModule.default.name, esModule.default
  ])
)

export default Vue.extend({
  name: 'Plugins',
  components: {
    ...plugins
  }
})
```

More info at https://webpack.js.org/guides/dependency-management/#context-module-api

## 2020-06-20

### Grab a file from another `git` branch

Tags: `git`

Avoid a lot of messy merge commits by rebasing on pull by default:

```shell
git config --global pull.rebase true
```

## 2020-05-05

### Grab a file from another `git` branch

Tags: `git`

While `cherry-pick` is great for copying over specific commits, if you want to grab a whole file onto your current branch from another, it's a simple as a `checkout`:

```shell
git checkout my-awesome-source-branch the/path/to/yourfile.rad
```

https://github.com/thoughtbot/til/blob/master/git/grab-a-file-from-another-branch.md

## 2020-04-22

### Easily log output of userdata scripts for debugging

Tags: `aws` `cloud` `bash` `shell` `*nix`

Log userdata scripts for cloud instances in detail, to allow easier debugging of any issues when the script is run when the instance is initialised.

https://aws.amazon.com/premiumsupport/knowledge-center/ec2-linux-log-user-data/

## 2020-04-15

### Checking TCP connectivity with `telnet`

Tags: `networking` `bash` `shell` `*nix`

https://netbeez.net/blog/telnet-to-test-connectivity-to-tcp/

## 2020-02-12

### Dynamic configuration of Dockerised nginx

Tags: `nginx` `docker` `bash` `shell` `*nix`

If you want to bake an `nginx` config into a Docker image, and/or dynamically set configuration in a volume mounted config, you can have the `COMMAND` for the container use [**`envsubst`**](https://www.gnu.org/software/gettext/manual/html_node/envsubst-Invocation.html) to substitute in environment variable values in the config before the `nginx` process is started, for example in a `docker-compose.yml`:

```yaml
web:
  image: nginx
  volumes:
   - ./mysite.template:/etc/nginx/conf.d/mysite.template
  ports:
   - "8080:80"
  environment:
   - NGINX_HOST=foobar.com
   - NGINX_PORT=80
  command: /bin/bash -c "envsubst < /etc/nginx/conf.d/mysite.template > /etc/nginx/conf.d/default.conf && exec nginx -g 'daemon off;'"
```

From https://hub.docker.com/_/nginx

## 2020-02-06

### Use a custom `GraphQLView` for better traceback handling

Tags: `python` `django` `graphql` `graphene` `graphene-django`

When using [Graphene-Django](https://github.com/graphql-python/graphene-django) to use GraphQL with Django, GraphQL swallows your error messages and tracebacks, making it more difficult to debug exceptions in resolvers.

There is a [GitHub issue](https://github.com/graphql-python/graphene-django/issues/124) discussing the problem, a number of solutions are suggested including using middleware, however middleware has the caveat that it has to be applied individually on each request.

A generally applicable solution is subclassing `GraphQLView` and adding back the traceback there (adapted from [Martin Samami on Medium](https://medium.com/@martin.samami/make-graphene-django-and-sentry-exceptions-work-together-f796be60a901) adding Sentry logging):

```python3
import traceback
from pprint import pprint
from graphene_django.views import GraphQLView


class ErrorLoggingGraphQLView(GraphQLView):
    """
    GraphQL view that logs errors better for easier debugging.

    https://github.com/graphql-python/graphene-django/issues/124#issuecomment-368283927
    https://medium.com/@martin.samami/make-graphene-django-and-sentry-exceptions-work-together-f796be60a901
    """

    def execute_graphql_request(self, *args, **kwargs):
        """Extract any exceptions and log them better than the default"""
        result = super().execute_graphql_request(*args, **kwargs)
        if result.errors:
            for error in result.errors:
                try:
                    raise error.original_error
                except Exception as original_error:
                    pprint(original_error)
                    traceback.print_exc()
        return result
```

Then in your `urls.py`:

```python3
from django.urls import include, path
from django.views.decorators.csrf import csrf_exempt
from your_schema_location import schema
from your_module import ErrorLoggingGraphQLView

urlpatterns = [
    path(
        "graphql/", csrf_exempt(ErrorLoggingGraphQLView.as_view(schema=schema, graphiql=True))
    ),
]
```

## 2020-01-15

### Get an alert when terminal commands finish

Tags: `bash` `zsh`

When running a long terminal command, it would be nice to be able to do something else while it runs, but be notified when it's finished.

The following `bash`/`zsh` function rings the terminal bell when the command passed to it either finishes or fails:

```bash
function ring () {
	($@ && tput bel) || tput bel
}
```

`ring` could also be adapted to send a notification with a custom message, using e.g. Applescript `osascript`.

Alternatively, there are richer solutions, such as [NotiFyre](https://github.com/kaustubhhiware/NotiFyre).

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
