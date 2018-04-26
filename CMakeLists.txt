CMAKE_MINIMUM_REQUIRED (VERSION 3.0.0 FATAL_ERROR)
PROJECT (sink VERSION 0.0.1 LANGUAGES C CXX)
#
# Policies
# --------
FOREACH (_policy
    CMP0063             # Honor visibility properties for all target types
    CMP0018)            # Ignore CMAKE_SHARED_LIBRARY_<Lang>_FLAGS variables
  IF (POLICY ${_policy})
    CMAKE_POLICY (SET ${_policy} NEW)
  ENDIF ()
ENDFOREACH ()
#
# Options
# -------
# OPTION (OPT_SINK_XXX "XXX" OFF)
#
# Sources
# -------
SET (SINK_LIB_SRCS src/sink.c)
SET (SINK_CMD_SRCS src/sink_shell.c src/cmd.c)
#
# Include directories
# -------------------
INCLUDE_DIRECTORIES(. src)
#
# Compiler settings
# -----------------
SET (CMAKE_C_VISIBILITY_PRESET   hidden)
SET (CMAKE_CXX_VISIBILITY_PRESET hidden)
SET (CMAKE_POSITION_INDEPENDENT_CODE ON)
#
# Installation settings
# ---------------------
INCLUDE (GNUInstallDirs) # enforce lib64 if needed
INCLUDE (InstallRequiredSystemLibraries) # system libraries if needed
#
# Libraries
# ---------
ADD_LIBRARY (sink_lib_objects OBJECT ${SINK_LIB_SRCS})
ADD_LIBRARY (sink_lib_shared  SHARED $<TARGET_OBJECTS:sink_lib_objects>)
ADD_LIBRARY (sink_lib_static  STATIC $<TARGET_OBJECTS:sink_lib_objects>)

SET_TARGET_PROPERTIES(sink_lib_shared PROPERTIES OUTPUT_NAME "sink")
SET_TARGET_PROPERTIES(sink_lib_static PROPERTIES OUTPUT_NAME "sink_static")

SET (CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
#
# Export definition
#
INCLUDE (GenerateExportHeader)
GENERATE_EXPORT_HEADER(sink_lib_objects
  EXPORT_FILE_NAME "src/sink_export.h"
  )
TARGET_COMPILE_OPTIONS(sink_lib_objects PUBLIC "-Dsink_EXPORTS")
#
# Target properties
#
SET_TARGET_PROPERTIES(sink_lib_shared
  PROPERTIES
  VERSION    ${sink_VERSION}
  SOVERSION  ${sink_VERSION_MAJOR}
  )
SET_TARGET_PROPERTIES(sink_lib_static
  PROPERTIES
  VERSION    ${sink_VERSION}
  )
#
# Executables
# -----------
ADD_EXECUTABLE(sink_executable ${SINK_CMD_SRCS})
SET_TARGET_PROPERTIES(sink_executable PROPERTIES OUTPUT_NAME "sink")
TARGET_LINK_LIBRARIES(sink_executable PUBLIC sink_lib_shared)
#
# Enable Testing
# --------------
#ENABLE_TESTING()
#ADD_TEST(NAME build_executable COMMAND ${CMAKE_COMMAND} --build ${CMAKE_BINARY_DIR} --target lemon_executable)
#
#ADD_TEST(NAME test            COMMAND ${CMAKE_COMMAND} -E env $<TARGET_FILE:lemon_executable> test/test.lm)
#ADD_TEST(NAME test_helloworld COMMAND ${CMAKE_COMMAND} -E env $<TARGET_FILE:lemon_executable> test/test_helloworld.lm)
#
#SET_TESTS_PROPERTIES (test            PROPERTIES DEPENDS build_executable)
#SET_TESTS_PROPERTIES (test_helloworld PROPERTIES DEPENDS build_executable)
