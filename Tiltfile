load('ext://dotenv', 'dotenv')

def dotenv_watch(dot_env_path='.env'):
  if os.path.exists(dot_env_path):
    dotenv()

  watch_file(dot_env_path)

valid_types = ['string', 'int', 'bool', 'string_list']

def define_config(key, default=None, type='string', required=False):
  if not type in valid_types:
    fail('Unknown type: %s' % type)

  if type == 'string':
    config.define_string(key)
  elif type == 'int':
    config.define_string(key)
  elif type == 'bool':
    config.define_bool(key)
  elif type == 'string_list':
    config.define_string_list(key)

  def get_value(cfg):
    env_name = key.upper().replace('-', '_')
    default_value = os.environ.get(env_name, default)
    value = cfg.get(key, default_value)

    if required and value == None:
      fail('Missing required config: %s' % key)

    if type == 'string':
      return value

    if type == 'int':
      return int(value)

    if type == 'bool':
      return bool(value)

  return get_value

def default_resources():
  # Noop resource allows us to have a mode with no resource enabled
  local_resource(name="noop", cmd='echo \'Empty resource\'')

def _get_mode_resources(modes, mode='default'):
  if mode == 'noop':
    return ['noop']

  if mode == 'all' or mode == 'full':
    return []

  if mode == 'default' and not 'default' in modes:
    return []

  if mode not in modes:
    fail('mode must be one of %s' % modes.keys())

  return modes[mode]

def set_mode(modes, mode='default', resources=[]):
  mode_resources = _get_mode_resources(modes, mode)
  enabled_resources = resources + mode_resources
  config.clear_enabled_resources()
  config.set_enabled_resources(enabled_resources)
  return enabled_resources

def dict_merge(a, b):
  c = {}
  c.update(a)
  c.update(b)
  return c

def bool_to_string(value):
  return 'true' if value else 'false'

true_values = ['true', '1', 'yes', 'on', 'y', 't', 'enabled', 'enable', 'active', 'activated']
false_values = ['false', '0', 'no', 'off', 'n', 'f', 'disabled', 'disable', 'inactive', 'deactivated']

def string_to_bool(value):
  if type(value) != "string":
    fail('Invalid string value: %s' % value)

  v = value.lower()

  is_true = v in true_values

  if is_true:
    return True

  is_false = v in false_values

  if is_false:
    return False

  fail('Invalid boolean value: %s' % value)

def write_file(filename, content):
  local('echo "$DATA_CONTENT" > %s' % filename, quiet=True, env={'DATA_CONTENT': content})
