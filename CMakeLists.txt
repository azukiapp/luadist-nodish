# Copyright (C) 2007-2013 LuaDist.
# Created by Peter Drahoš
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

cmake_minimum_required ( VERSION 2.8 )
include ( cmake/dist.cmake )
project ( ${DIST_NAME} NONE )
include ( lua )

set ( GIT_ARCHIVE_URL ${DIST_REPOSITORY}/archive )
if ( DIST_CHECKOUT )
  set ( SOURCE_FILE_NAME ${DIST_CHECKOUT}.tar.gz )
  set ( SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/${DIST_NAME}-${DIST_CHECKOUT} )
else ()
  set ( SOURCE_FILE_NAME v${DIST_VERSION}.tar.gz )  
  set ( SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/${DIST_NAME}-${DIST_VERSION} )
endif ()
set ( SOURCE_URL ${GIT_ARCHIVE_URL}/${SOURCE_FILE_NAME} )
set ( SOURCE_FILE ${CMAKE_CURRENT_BINARY_DIR}/${SOURCE_FILE_NAME} )

if ( NOT EXISTS "${SOURCE_FILE}" )
  message ( "${SOURCE_URL}" )
  file( DOWNLOAD "${SOURCE_URL}" "${SOURCE_FILE}" )

  execute_process(
    COMMAND tar -xzvf ${SOURCE_FILE}
  )
endif ()

file( GLOB LIBSYSCALL
  "${SOURCE_DIR}/src/nodish/**/*.lua"
  "${SOURCE_DIR}/src/nodish/*.lua"
)

foreach(_file ${LIBSYSCALL})
  get_filename_component ( _file "${_file}" REALPATH )
  get_filename_component ( _file_name "${_file}" NAME )
  get_filename_component ( _path "${_file}" PATH )
  get_filename_component ( _name "${_file}" NAME_WE )

  string ( REGEX REPLACE "${SOURCE_DIR}/src/" "" _module "${_path}" )
  string ( REGEX REPLACE "/" "." _module_name "${_module}" )

  install_lua_module ( "${_module_name}.${_name}" ${_file} )
endforeach ( )

install_data ( ${SOURCE_DIR}/README.md ${SOURCE_DIR}/LICENSE )
