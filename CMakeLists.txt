# Copyright (C) 2012 LuaDist.
# Created by Peter Drahoš
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( libzmq CXX C )
cmake_minimum_required ( VERSION 2.8 )
include ( cmake/dist.cmake )
include ( configure )

#2DO OpenPGM (ZMQ_HAVE_OPENPGM)

include_directories ( src include ${CMAKE_CURRENT_BINARY_DIR} )
file ( GLOB ZMQ_SRC src/*.cpp )

add_definitions ( -D_REENTRANT -D_THREAD_SAFE )
if ( WIN32 )
  set ( ZMQ_PLATFORM ZMQ_HAVE_WINDOWS )
  set ( LIBS Ws2_32 )
  #TODO? HAVE_MINGW32
elseif ( CYGWIN )
set ( ZMQ_PLATFORM ZMQ_HAVE_CYGWIN )
add_definitions ( -D_DARWIN_C_SOURCE )
elseif ( APPLE )
set ( ZMQ_PLATFORM ZMQ_HAVE_OSX )
set ( ZMQ_HAVE_UIO true )
add_definitions ( -D_GNU_SOURCE )
elseif ( UNIX )
# actually Linux
set ( ZMQ_PLATFORM ZMQ_HAVE_LINUX )
add_definitions ( -D_GNU_SOURCE )
else ( )
  #FIX: add other platforms noted in configure.in?
  #  ZMQ_HAVE_ANDROID ZMQ_HAVE_SOLARIS ZMQ_HAVE_FREEBSD ZMQ_HAVE_NETBSD
  #  ZMQ_HAVE_OPENBSD ZMQ_HAVE_QNXNTO ZMQ_HAVE_AIX ZMQ_HAVE_HPUX
  message ( FATAL_ERROR "unknown platform" )
endif ( )

configure_file ( src/platform.hpp.cmake ${CMAKE_CURRENT_BINARY_DIR}/platform.hpp )

add_library ( zmq ${ZMQ_SRC} )
target_link_libraries ( zmq ${LIBS} )

install_library ( zmq )
install_header ( include/zmq.h include/zmq_utils.h )
install_data ( AUTHORS COPYING README NEWS )
