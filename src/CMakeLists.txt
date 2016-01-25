 #     Copyright (c) 2010-2012 Delft University of Technology.
 #
 #    This software is protected by national and international copyright.
 #    Any unauthorized use, reproduction or modification is unlawful and
 #    will be prosecuted. Commercial and non-private application of the
 #    software in any form is strictly prohibited unless otherwise granted
 #    by the authors.
 #
 #    The code is provided without any warranty; without even the implied
 #    warranty of merchantibility or fitness for a particular purpose.
 #
 #    Changelog
 #      YYMMDD    Author            Comment
 #      110820    S.M. Persson      File created.
 #      111025    K. Kumar          Adapted file to work with Revision 194.
 #      111026    K. Kumar          Adapted file so all headers show in project tree in Qt Creator.
 #/

# Specify minimum CMake version required to compile Tudat.
cmake_minimum_required( VERSION 2.6 )

# Specific Tudat project name.
project( Sofa)

# Define the basic input (trunk).
set(SRCROOT ${PROJECT_SOURCE_DIR})

set(LIBROOT "${SRCROOT}/../lib")
set(BINROOT "${SRCROOT}/../bin")

if (WIN32)
 if (MSVC)
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /EHsc /Ox /W3 /FC -D_SCL_SECURE_NO_WARNINGS")
   # Because we are using static boost libraries, with static runtime, we need to force MSVC to
   # also use static runtime: (from http://www.cmake.org/Wiki/CMake_FAQ#Dynamic_Replace)
   foreach(flag_var
         CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
         CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
     # Find all dynamic runtime (MD) references and replace with static (MT)
     if(${flag_var} MATCHES "/MD")
       string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
     endif(${flag_var} MATCHES "/MD")
   endforeach(flag_var)
 else( )
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2 -Wall -g -Woverloaded-virtual -Wold-style-cast -Wnon-virtual-dtor")
 endif( )
else( )
 set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2 -Wall -g -Woverloaded-virtual -Wold-style-cast -Wnon-virtual-dtor")
endif( )

# Set CMake build-type. If it not supplied by the user (either directly as an argument of through
# the "UserSettings.txt" file, the default built type is "Release". NB: It is important this
# section is placed AFTER the "UserSettings.txt" file is included, to ensure that that the correct
# indiciation is given for which build type has been selected.
if((NOT CMAKE_BUILD_TYPE) OR (CMAKE_BUILD_TYPE STREQUAL "Release"))
   message(STATUS "WARNING: building release version!")
   set(CMAKE_BUILD_TYPE Release)
elseif(CMAKE_BUILD_TYPE STREQUAL "Debug")
   message(STATUS "WARNING: building debug version!")
endif()

# Set the global macros for setting up targets.
macro(setup_library_target target_name CUSTOM_OUTPUT_PATH)
 set_property(TARGET ${target_name} PROPERTY LIBRARY_OUTPUT_DIRECTORY "${LIBROOT}")
 set_property(TARGET ${target_name} PROPERTY ARCHIVE_OUTPUT_DIRECTORY "${LIBROOT}")
 install(TARGETS ${target_name} LIBRARY DESTINATION "${BINROOT}" ARCHIVE DESTINATION "${LIBROOT}")
endmacro(setup_library_target)

 #Set the source files.

# Set the header files.
#set(SOFAINTERFACE_HEADERS
#  "${SRCROOT}/sofa.h"
#  "${SRCROOT}/sofam.h"
#)
file(GLOB_RECURSE SOFAINTERFACE_SOURCES RELATIVE ${SRCROOT} *.c)
file(GLOB_RECURSE SOFAINTERFACE_HEADERS RELATIVE ${SRCROOT} *.h)

# Add static libraries.

add_library(sofa STATIC ${SOFAINTERFACE_SOURCES} ${SOFAINTERFACE_HEADERS})
setup_library_target(sofa "${SRCROOT}${SOFAINTERFACEDIR}")

# End of file.
