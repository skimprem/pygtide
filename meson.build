project('etpred', 'c',
  version : '0.1',
  license: 'BSD-3',
  meson_version: '>=0.64.0',
  default_options : ['warning_level=2'],
)

# Get Fortran compiler
add_languages('fortran')

# Find Python interpreter and its dependencies
py = import('python').find_installation('python', pure: false)
py_dep = py.dependency()

# Find numpy include directory
incdir_numpy = run_command(py,
    ['-c', 'import os; os.chdir(".."); import numpy; print(numpy.get_include())'],
    check: true
).stdout().strip()

# Find f2py include directory
incdir_f2py = run_command(py,
    ['-c', 'import os; os.chdir(".."); import numpy.f2py; print(numpy.f2py.get_include())'],
    check: true
).stdout().strip()

# Define include directories for numpy and f2py using relative paths
inc_np = include_directories(incdir_numpy, incdir_f2py)

# Define custom target for f2py compilation
# etpred_source = custom_target('etpredmodule.c',
#     input: ['src/etpred.f90'],  # .f so no F90 wrappers
#     output: ['etpredmodule.c', 'etpred-f2pywrappers.f'],
#     command: [py, '-m', 'numpy.f2py', '@INPUT@', '-m', 'etpred', '--lower']
# )

etpred_build = run_command(
    'python',
    '-m', 'numpy.f2py',
    '-c',
    '-m', 'etpred',
    'src/etpred.f90',
    '--build-dir', 'build',
    check: true
).stdout().strip()

# Define Python extension module
py.extension_module('etpred',
    [
        'src/etpred.f90',
        # etpred_source
        'build/etpredmodule.c',
        'build/etpred-f2pywrappers.f',
        'build/etpred-f2pywrappers2.f90',
    ],
    incdir_f2py / 'fortranobject.c',
    include_directories: inc_np,
    dependencies: py_dep,
    install: true
)

# Add Python source files
python_sources = files(
    'pygtide/__init__.py',
    'pygtide/core.py',
    'pygtide/tests.py',
    'pygtide/update_etpred_data.py',
)

# Install Python sources
py.install_sources(
    'pygtide/__init__.py',
    'pygtide/core.py',
    'pygtide/tests.py',
    'pygtide/update_etpred_data.py',
    # 'pygtide/commdat/',
    subdir: 'pygtide'
)

install_subdir(
    'pygtide' / 'commdat',
    install_dir: py.get_install_dir() / 'pygtide',
    strip_directory : false
)