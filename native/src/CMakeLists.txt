################################################################
# A minimal CMake file that is compatible with sbt-jni         #
#                                                              #
# All settings required by sbt-jni have been marked so, please #
# add/modify/remove settings to build your specific library.   #
################################################################

cmake_minimum_required(VERSION 2.6)

# Define project and related variables
#
project (TomsOpt)

# Force to RELEASE
# XXX This can be removed once https://github.com/jodersky/sbt-jni/pull/8/files is released.
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release" CACHE STRING
      "Choose the type of build, options are: Debug Release
RelWithDebInfo MinSizeRel."
      FORCE)
endif(NOT CMAKE_BUILD_TYPE)


# Set versions and library name
# (required by sbt-jni) please use semantic versioning
#
set (VERSION_MAJOR 0)
set (VERSION_MINOR 0)
set (VERSION_PATCH 0)
# (required by sbt-jni) major version will always be appended to library name
set (LIB_NAME ${CMAKE_PROJECT_NAME}${VERSION_MAJOR})

# Command-line options
#
# (set by sbt-jni)
set (LIB_INSTALL_DIR lib CACHE PATH "Path in which to install libraries (equivalent to Autoconf --libdir).")
# (set by sbt-jni)
set (LIB_ENABLE_MINOR_VERSIONS ON CACHE BOOLEAN "Build libraries with minor and patch versions appended.")

# Setup JNI
find_package(JNI REQUIRED)
if (JNI_FOUND)
    message (STATUS "JNI include directories: ${JNI_INCLUDE_DIRS}")
endif()

# Include directories
include_directories(.)
include_directories(include)
include_directories(${JNI_INCLUDE_DIRS})

# Setup main shared library
file(GLOB LIB_SRC
  "*.c"
  "*.cpp"
)
add_library(${LIB_NAME} SHARED ${LIB_SRC})

# By default, in a regular build, minor and patch versions are added to the generated files.
# When built through sbt-jni however, LIB_ENABLE_MINOR_VERSIONS is deactivated and only a
# major-versioned library file is built.
if (LIB_ENABLE_MINOR_VERSIONS)
   set_target_properties(
	${LIB_NAME}
	PROPERTIES
	VERSION 0.${VERSION_MINOR}.${VERSION_PATCH} # major version always 0, it is included in library name
	SOVERSION 0
   )
endif()

# Installation targets
install(TARGETS ${LIB_NAME} LIBRARY DESTINATION ${LIB_INSTALL_DIR})
