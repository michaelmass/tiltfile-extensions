# tiltfile-extensions
> A collection of Tiltfile extensions for the Tilt CLI

## Usage

In your Tiltfile, download the tiltfile using `curl` then use `load` or `include` to load the extension.

```python
if not os.path.exists('extension_tiltfile'):
  local('curl https://raw.githubusercontent.com/botpress/tilt-extensions/mm-extensions/commons/Tiltfile > extension_tiltfile', quiet=True)

load('./extension_tiltfile', 'dotenv_watch')

dotenv_watch()
```

Make sure to add the `ext_tiltfile` to your `.gitignore` file.

## Extensions

### dotenv_watch

The `dotenv_watch` function watches for changes on the `.env` file and reloads the `Tiltfile` with the `.env` environment variables when the file changes.

```python
load('./extension_tiltfile', 'dotenv_watch')

dotenv_watch()
```

## Notes

The [vscode tilt extension](https://marketplace.visualstudio.com/items?itemName=tilt-dev.Tiltfile) doesn't load the extension correctly like mentionned in the [documentation](https://docs.tilt.dev/extensions). This little workaround should help you load the extension in your `Tiltfile`.

I suggest also looking into the offical [Extensions](https://github.com/tilt-dev/tilt-extensions) before doing this pattern.
