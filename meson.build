project(
  'uknocs',
  'c',
  version: '0.0.0',
  license: 'MIT',
  meson_version: '>=0.63.0',
  default_options: [
    'warning_level=2',
    'werror=true',
    'prefix=/usr/'
  ],
)

docs_src = [
  'docs/build.rst', 'docs/index.rst', 'docs/operating-systems.rst',
  'docs/yocto-project.rst'
]

docs_dir = meson.current_source_dir() + '/docs'
docs_build_dir = meson.current_build_dir() + '/docs'
sphinx = find_program('sphinx-build-5', 'sphinx-build', required: true)

if sphinx.found()
  custom_target('docs',
                command: [sphinx, '-W', '-b', 'html', docs_dir, '@OUTPUT@'],
                input: docs_src, output: 'docs', install: true, install_dir: '')
endif
