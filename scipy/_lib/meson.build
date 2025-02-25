fs = import('fs')
if not fs.exists('boost/README.rst')
  error('Missing the `boost` submodule! Run `git submodule update --init` to fix this.')
endif
if not fs.exists('highs/README.md')
  error('Missing the `highs` submodule! Run `git submodule update --init` to fix this.')
endif
if not fs.exists('unuran/README.md')
  error('Missing the `unuran` submodule! Run `git submodule update --init` to fix this.')
endif

_lib_pxd = [
  fs.copyfile('__init__.py'),
  fs.copyfile('_ccallback_c.pxd'),
  fs.copyfile('ccallback.pxd'),
  fs.copyfile('messagestream.pxd'),
]

# Cython pyx -> c generator with _lib_pxd dependency
lib_cython_gen = generator(cython_cli,
  arguments : ['@INPUT@', '@OUTPUT@'],
  output : '@BASENAME@.c',
  depends : [_cython_tree, _lib_pxd])

py3.extension_module('_ccallback_c',
  lib_cython_gen.process('_ccallback_c.pyx'),
  c_args: [cython_c_args, Wno_discarded_qualifiers],
  include_directories: 'src',
  link_args: version_link_args,
  install: true,
  subdir: 'scipy/_lib'
)

py3.extension_module('_test_ccallback',
  'src/_test_ccallback.c',
  include_directories: 'src',
  link_args: version_link_args,
  install: true,
  subdir: 'scipy/_lib'
)

py3.extension_module('_fpumode',
  '_fpumode.c',
  include_directories: 'src',
  link_args: version_link_args,
  install: true,
  subdir: 'scipy/_lib'
)

py3.extension_module('_test_deprecation_call',
  cython_gen.process('_test_deprecation_call.pyx'),
  c_args: cython_c_args,
  include_directories: 'src',
  link_args: version_link_args,
  install: true,
  subdir: 'scipy/_lib'
)

py3.extension_module('_test_deprecation_def',
  cython_gen.process('_test_deprecation_def.pyx'),
  c_args: cython_c_args,
  include_directories: 'src',
  link_args: version_link_args,
  install: true,
  subdir: 'scipy/_lib'
)

# May be easier as a compile flag, but use a config header file to stay
# in sync with what is done in setup.py
# TODO: the `prefix` here is recommended in
#       https://mesonbuild.com/Compiler-properties.html#does-a-function-exist
#       and seems needed, but why does it fail to detect it for the conda
#       compiler? Does it not exist, or is it hidden elsewhere?

conf_memstream = configuration_data()

if meson.get_compiler('c').has_function('open_memstream',
                                        prefix: '#include <stdio.h>')
  conf_memstream.set('has_openmemstream', '1')
else
  conf_memstream.set('has_openmemstream', '0')
endif

configure_file(
  input: 'src/messagestream_config.h.in',
  output: 'messagestream_config.h',
  configuration: conf_memstream
)

py3.extension_module('messagestream',
  lib_cython_gen.process('messagestream.pyx'),
  c_args: cython_c_args,
  include_directories: 'src',
  link_args: version_link_args,
  install: true,
  subdir: 'scipy/_lib'
)

python_sources = [
  '__init__.py',
  '_bunch.py',
  '_ccallback.py',
  '_disjoint_set.py',
  '_docscrape.py',
  '_finite_differences.py',
  '_gcutils.py',
  '_pep440.py',
  '_testutils.py',
  '_threadsafety.py',
  '_tmpdirs.py',
  '_util.py',
  'decorator.py',
  'deprecation.py',
  'doccer.py',
  'uarray.py',
]

py3.install_sources(
  python_sources,
  subdir: 'scipy/_lib'
)

subdir('_uarray')
subdir('tests')
