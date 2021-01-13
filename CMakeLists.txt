cmake_minimum_required(VERSION 3.17.0)

project(Example VERSION 0.1.0 LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CMAKE_CXX_VISIBILITY_PRESET hidden)
set(CMAKE_VISIBILITY_INLINES_HIDDEN YES)

add_definitions(-DUNICODE -D_UNICODE)
add_definitions(-DWIN32_LEAN_AND_MEAN -DNOMINMAX)


function(add_lib_variant BASE_NAME VARIANT SOURCES)
	set(LIB_NAME "${BASE_NAME}_${LIB_VARIANT}")
	add_library(${LIB_NAME} SHARED ${SOURCES})

	# This causes Visual Studio to only list one of the targets - because they
	# have the same name.
	set_target_properties(${LIB_NAME} PROPERTIES
		OUTPUT_NAME ${BASE_NAME}
		DEFINE_SYMBOL ${BASE_NAME}_EXPORTS
	)

	# This ensures that each of the libs end up in their own sub-directory
	# such that they don't clash with each other.
	set_target_properties(${EXTENSION_LIB} PROPERTIES
		ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${LIB_VARIANT}"
		LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${LIB_VARIANT}"
		RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${LIB_VARIANT}"
	)
endfunction()


# The reason why I'm generating multiple versions of a shared library is that
# I'm making plugins for an application where I have to target multiple versions
# of the host application the plugins are loaded into.
# This host application will call an initialization function based on
# the name of the binary. Thus the OUTPUT_NAME name for each library has to be
# the same.
set(LIB_SOURCES example_lib.h example_lib.cpp)
set(LIB_VARIANTS "foo" "bar")
foreach(LIB_VARIANT ${LIB_VARIANTS})
	add_lib_variant("example_lib" ${LIB_VARIANT} "${LIB_SOURCES}")
endforeach()


add_executable(example_app main.cpp)
