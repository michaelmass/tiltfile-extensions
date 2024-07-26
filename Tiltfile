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

def postgres_uri(username='postgres', password='admin', port=5432, database='postgres', host='localhost'):
  return 'postgres://%s:%s@%s:%s/%s' % (username, password, host, port, database)

def redis_uri(password='admin', port=6379, host='localhost'):
  return 'redis://:%s@%s:%s' % (password, host, port)

def dict_omit(d, keys):
  return { k: v for k, v in d.items() if k not in keys }

def docker_resource(
  name=None,
  labels=[],
  build=None,
  image=None,
  entrypoint=None,
  command=None,
  ports=[],
  environment={},
  docker_deps=[],
  extra={},
):
  """Create a docker resource.

  The resource still need to be passed down to the docker_compose_resources function to be used.

  ```
  docker_res = docker_resource(
    name='hellomicro',
    labels=['services'],
    build={ context: 'path/to/context', dockerfile: 'path/to/Dockerfile' },
    image='michaelmass/hellomicro:latest',
    entrypoint=['/bin/sh', '-c'],
    command=['echo', 'Hello World'],
    ports=[{ host: 8080, container: 80 }],
    environment={'HELLO': 'world'},
    docker_deps=['db'],
    extra={'volumes': ['./data:/data']},
  )

  docker_compose_resources([docker_res])
  ```

  Args:
    name: the name of the resource `hellomicro`
    labels: labels to set on the resource `['services']`
    build: a build context for the docker image `{ context: 'path/to/context', dockerfile: 'path/to/Dockerfile' }`
    image: the docker image to use `michaelmass/hellomicro:latest`
    entrypoint: the entrypoint for the container `['/bin/sh', '-c']`
    command: the command to run in the container `['echo', 'Hello World']`
    ports: ports to expose on the container. `[{ host: 8080, container: 80 }]`
    environment: environment variables to set in the container `{'HELLO': 'world'}`
    docker_deps: list of docker resources that this resource depends on `['db']`
    extra: extra docker-compose options to set on the resource `{'volumes': ['./data:/data']}`

  Returns:
    docker resource object
  """
  resource = {
    'name': name,
    'depends_on': docker_deps,
    'restart': 'always',
    'image': image,
    'command': command,
    'entrypoint': entrypoint,
    'ports': [('%s:%s' % (port['host'], port['container'])) for port in ports],
    'environment': environment,
    'labels': labels,
  }

  if build:
    resource['build'] = build

  return dict_merge(resource, extra)

def docker_compose_resources(services=[]):
  docker_compose(encode_yaml({
    'version': '3.5',
    'services': { r['name']: dict_omit(r, ['name']) for r in services },
  }))

  for service in services:
    dc_resource(name=service['name'], labels=service['labels'])

def open(name, urls, deps, labels):
  cmd = 'start' if (os.name == 'nt') else 'open'
  local_resource(
    name=name,
    cmd=(';'.join(['%s %s' % (cmd, url) for url in urls])),
    labels=labels,
    resource_deps=deps,
  )

def default_settings():
  update_settings(max_parallel_updates=9999)

def resource(
  name,
  allow_parallel=True,
  env={},
  labels=[],
  cmd=None,
  dir=None,
  serve_cmd=None,
  serve_dir=None,
  serve_env=None,
  watch_dir=[],
  resource_deps=[],
  links=[],
  manual=False,
  readiness_probe=None,
):
  local_resource(
    name=name,
    cmd=cmd,
    allow_parallel=allow_parallel,
    labels=labels,
    env=env,
    dir=dir,
    serve_cmd=serve_cmd,
    serve_dir=(serve_dir if serve_dir else dir),
    serve_env=(serve_env if serve_env else env),
    resource_deps=resource_deps,
    deps=watch_dir,
    links=links,
    auto_init=(not manual),
    readiness_probe=readiness_probe,
  )
