cmake_minimum_required(VERSION 3.17.0)

project(Example VERSION 0.1.0 LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CMAKE_CXX_VISIBILITY_PRESET hidden)
set(CMAKE_VISIBILITY_INLINES_HIDDEN YES)

add_definitions(-DUNICODE -D_UNICODE)
add_definitions(-DWIN32_LEAN_AND_MEAN -DNOMINMAX)


set(LIB_SOURCES example_lib.h example_lib.cpp)

# Foo variant
add_library(example_lib_foo SHARED ${LIB_SOURCES})
set_target_properties(example_lib_foo PROPERTIES
	OUTPUT_NAME example_lib
	DEFINE_SYMBOL example_lib_EXPORTS
)
set_target_properties(example_lib_foo PROPERTIES
	ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/foo"
	LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/foo"
	RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/foo"
)

# Bar variant
add_library(example_lib_bar SHARED ${LIB_SOURCES})
# This causes Visual Studio to only list one of the targets - because they
# have the same name.
set_target_properties(example_lib_bar PROPERTIES
	OUTPUT_NAME example_lib
	DEFINE_SYMBOL example_lib_EXPORTS
)
# This ensures that each of the libs end up in their own sub-directory
# such that they don't clash with each other.
set_target_properties(example_lib_bar PROPERTIES
	ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bar"
	LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bar"
	RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bar"
)
