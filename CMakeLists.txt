cmake_minimum_required (VERSION 2.6)
project (tuneit)

#SET(CMAKE_C_FLAGS "-g -O0 -Wall")

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules/")

set (EXTRA_LIB m)

INCLUDE (CheckIncludeFiles)
CHECK_INCLUDE_FILES(sys/soundcard.h OSS_FOUND)
if(OSS_FOUND)
	add_definitions(-DUSE_OSS=1)
endif(OSS_FOUND)

find_package(ALSA)
if(ALSA_FOUND)
	add_definitions(-DUSE_ALSA=1)
	include_directories(${ALSA_INCLUDE_DIRS})
	set (EXTRA_LIB ${EXTRA_LIB} ${ALSA_LIBRARIES})
endif(ALSA_FOUND)

find_package(Jack)
if(JACK_FOUND)
	add_definitions(-DUSE_JACK=1)
	include_directories(${JACK_INCLUDE_DIRS})
	set (EXTRA_LIB ${EXTRA_LIB} ${JACK_LIBRARIES})
endif(JACK_FOUND)

find_library(FFTW_LIBRARY NAMES fftw3f)

set (EXTRA_LIB ${EXTRA_LIB} ${FFTW_LIBRARY})

add_definitions(-DPACKAGE_NAME="tuneit")

add_executable (tuneit
src/tuneit.c)

target_link_libraries (tuneit ${EXTRA_LIB})

SET(man_files src/tuneit.1)
INSTALL(FILES ${man_files} DESTINATION ${INSTALL_MANPREFIX}/man/man1)
INSTALL(FILES ${doc_files} DESTINATION share/doc/tuneit)
INSTALL(TARGETS tuneit RUNTIME DESTINATION bin)
