cmake_minimum_required(VERSION 2.6)
project(libamf C CXX)

# generic variables
set(PACKAGE           "libamf")
set(PACKAGE_NAME      ${PACKAGE})
set(PACKAGE_BUGREPORT "libamf@sf.net")
set(PACKAGE_VERSION   "0.1.0")
set(PACKAGE_STRING    "${PACKAGE_NAME} ${PACKAGE_VERSION}")

#compiler tests
include(CheckCXXCompilerFlag)
check_cxx_compiler_flag("-std=c++11" COMPILER_SUPPORTS_CXX11)
check_cxx_compiler_flag("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
endif()

#platform tests
include(CheckFunctionExists)
include(CheckIncludeFile)
include(CheckTypeSize)
include(TestBigEndian)

check_include_file(sys/types.h  HAVE_SYS_TYPES_H)
check_include_file(stdint.h     HAVE_STDINT_H)
check_include_file(stddef.h     HAVE_STDDEF_H)
check_include_file(inttypes.h   HAVE_INTTYPES_H)

check_type_size("double"      SIZEOF_DOUBLE)
check_type_size("float"       SIZEOF_FLOAT)
check_type_size("long double" SIZEOF_LONG_DOUBLE)

if(MSVC AND NOT HAVE_STDINT_H)
  set(int16_t  1)
  set(int32_t  1)
  set(int64_t  1)
  set(int8_t   1)
  set(uint16_t 1)
  set(uint32_t 1)
  set(uint64_t 1)
  set(uint8_t  1)
endif(MSVC AND NOT HAVE_STDINT_H)

test_big_endian(IS_BIGENDIAN)
if(IS_BIGENDIAN)
  set(WORDS_BIGENDIAN 1)
endif(IS_BIGENDIAN)

# configuration file
configure_file(config-cmake.h.in ${CMAKE_BINARY_DIR}/config.h)
include_directories(${CMAKE_BINARY_DIR})

# Visual C++ specific configuration
if(MSVC)
  # use static library
  set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /MT")
  
  # C runtime deprecation in Visual C++ 2005 and later
  add_definitions(-D_CRT_SECURE_NO_DEPRECATE -D_CRT_NONSTDC_NO_DEPRECATE)
endif(MSVC)

add_subdirectory(src)

# tests
enable_testing()
add_subdirectory(tests)
