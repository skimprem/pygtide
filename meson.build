project('etpred',
        ['c', 'fortran'],
        version: '0.1',
        meson_version: '>=1.1.0',
        default_options: [
            'warning_level=1',
            'buildtype=release'
        ])

# Get Fortran compiler
fc = meson.get_compiler('fortran')

# Find Python interpreter and its dependencies
py = import('python').find_installation('python', pure: false)
py_dep = py.dependency()

# Find numpy include directory
incdir_numpy = run_command(py,
    ['-c', 'import os; import numpy; print(numpy.get_include())'],
    check: true
).stdout().strip()

# Find f2py include directory
incdir_f2py = run_command(py,
    ['-c', 'import os; import numpy.f2py; print(numpy.f2py.get_include())'],
    check: true
).stdout().strip()

# Define include directories for numpy and f2py using relative paths
inc_np = include_directories(incdir_numpy)
inc_f2py = include_directories(incdir_f2py)

# Define paths for f2py source files (using relative paths)
fortranobject_c = 'fortranobject.c'

# Declare dependencies
np_dep = declare_dependency(include_directories: inc_np)

# Define custom target for f2py compilation
etpred_source = custom_target('etpredmodule.c',
    input: ['src/etpred.f90'],  # Relative path to Fortran source file
    output: ['etpredmodule.c', 'etpred-f2pywrappers.f'],
    command: [py, '-m', 'numpy.f2py', '@INPUT@', '-m', 'etpred']
)

# Define Python extension module
py.extension_module('etpred',
    [
        'src/etpred.f90',
        etpred_source,
        incdir_f2py / fortranobject_c
    ],
    include_directories: [inc_np, inc_f2py],
    dependencies: [py_dep, np_dep],
    install: true
)
