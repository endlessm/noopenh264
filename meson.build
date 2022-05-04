project('noopenh264', ['cpp'],
  version : '0.1.0',
  meson_version: '>= 0.40.0',
  license: 'MPL-2.0 or LGPLv2.1+',
  default_options: [
    'c_std=gnu11',
    'warning_level=2',
  ],
)

major_version = '6'
matching_version = '2.2.0'

pkgconfig = import('pkgconfig')

prefix = get_option('prefix')
libdir = join_paths(prefix, get_option('libdir'))
includedir = join_paths(prefix, get_option('includedir'))

cpp = meson.get_compiler('cpp')

inc = include_directories([
  join_paths('codec', 'api', 'svc'),
])

api_headers = []
api_header_deps = []

subdir('codec')

all_objects = [
  libencoder.extract_all_objects(),
  libdecoder.extract_all_objects()
]

libopenh264_shared = shared_library('openh264',
  objects: all_objects,
  install: true,
  soversion: major_version,
  version: matching_version)

libopenh264_static = static_library('openh264',
  objects: all_objects,
  install: true)

pkg_install_dir = '@0@/pkgconfig'.format(get_option('libdir'))

foreach t : ['', '-static']
  pkgconf = configuration_data()
  pkgconf.set('prefix', join_paths(get_option('prefix')))
  pkgconf.set('libdir', libdir)
  pkgconf.set('VERSION', matching_version)
  if t == '-static'
    do_install = false
    pkgconf.set('LIBS', '-lstdc++ -lpthread -lm')
    pkgconf.set('LIBS_PRIVATE', '')
  else
    do_install = true
    pkgconf.set('LIBS', '')
    pkgconf.set('LIBS_PRIVATE', '-lstdc++ -lpthread -lm')
  endif

  message('do_install: @0@'.format(do_install))

  configure_file(
    input: 'openh264.pc.in',
    output: 'openh264@0@.pc'.format(t),
    install: t == '-static' ? false : true,
    install_dir: t == '-static' ? '' : pkg_install_dir,
    configuration: pkgconf)
endforeach

openh264_dep = declare_dependency(
  link_with: libopenh264_shared,
  include_directories: include_directories('include'),
  dependencies: api_header_deps)

subdir ('include')
