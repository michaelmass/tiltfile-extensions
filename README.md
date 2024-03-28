# tiltfile-extensions
> A collection of Tiltfile extensions for the Tilt CLI

## Usage

In your Tiltfile, download the tiltfile using `curl` then use `load` or `include` to load the extension.

```python
if not os.path.exists('extension_tiltfile'):
  local('curl https://raw.githubusercontent.com/michaelmass/tiltfile-extensions/master/Tiltfile > extension_tiltfile', quiet=True)

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

### define_config

The `define_config` function allows you to define a configuration settings that is easier to setup. It loads the configuration from the config file, environment variables and uses a default values.

```python
load('./extension_tiltfile', 'define_config')

# Define a setting called `environment_var` with a default value of `DEV`.
# This setting can be loaded from the environment variable `ENVIRONMENT_VAR` or the file `tilt_config.json`.
get_environment_var = define_config(key="environment_var", default="DEV", type='string', required=True)

cfg = config.parse()

environment_var = get_environment_var(cfg) # Get the value of the configuration setting `environment_var`
```

## Notes

The [vscode tilt extension](https://marketplace.visualstudio.com/items?itemName=tilt-dev.Tiltfile) doesn't load the extension correctly like mentionned in the [documentation](https://docs.tilt.dev/extensions). This little workaround should help you load the extension in your `Tiltfile`.

I suggest also looking into the offical [Extensions](https://github.com/tilt-dev/tilt-extensions) before doing this pattern.
