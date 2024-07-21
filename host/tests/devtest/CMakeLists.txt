#
# Copyright 2015 Ettus Research LLC
# Copyright 2018 Ettus Research, a National Instruments Company
#
# SPDX-License-Identifier: GPL-3.0-or-later
#

# Formatting
message(STATUS "")

# All devtest files get installed:
file(GLOB py_devtest_files "*.py")
UHD_INSTALL(PROGRAMS
    ${py_devtest_files}
    DESTINATION ${PKG_LIB_DIR}/tests/devtest
    COMPONENT tests
)

# Arguments:
# - pattern: This will be used to identify which devtest_*.py is to be executed.
# - filter: Will be used in args strings as "type=<filter>".
# - devtype: A descriptive string. Is only used for CMake output.
macro(ADD_DEVTEST pattern filter devtype)
    message(STATUS "Adding ${devtype} device test target")
    add_custom_target("test_${pattern}"
        ${RUNTIME_PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/run_testsuite.py
        "--src-dir" "${CMAKE_CURRENT_SOURCE_DIR}"
        "--devtest-pattern" "${pattern}"
        "--args" "$$EXTRA_DEV_ARGS,type=${filter}"
        "--build-type" "${CMAKE_BUILD_TYPE}"
        "--build-dir" "${UHD_BINARY_DIR}"
        "--python-interp" "${RUNTIME_PYTHON_EXECUTABLE}"
        COMMENT "Running device test on all connected ${devtype} devices:"
        WORKING_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}"
    )
endmacro(ADD_DEVTEST)

if(ENABLE_B200)
    ADD_DEVTEST("b2xx" "b200" "B2XX")
endif(ENABLE_B200)

# Formatting
message(STATUS "")
