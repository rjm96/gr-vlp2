# Copyright 2011,2012 Free Software Foundation, Inc.
#
# This file is part of GNU Radio
#
# GNU Radio is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3, or (at your option)
# any later version.
#
# GNU Radio is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with GNU Radio; see the file COPYING.  If not, write to
# the Free Software Foundation, Inc., 51 Franklin Street,
# Boston, MA 02110-1301, USA.

########################################################################
# Setup library
########################################################################
include(GrPlatform) #define LIB_SUFFIX

include_directories(${Boost_INCLUDE_DIR})
link_directories(${Boost_LIBRARY_DIRS})
list(APPEND vlp2_sources
    amp2d_fixed_height_impl.cc
    amp2d_ff_impl.cc
    trilat_scaleable_ff_impl.cc )

set(vlp2_sources "${vlp2_sources}" PARENT_SCOPE)
if(NOT vlp2_sources)
	MESSAGE(STATUS "No C++ sources... skipping lib/")
	return()
endif(NOT vlp2_sources)

add_library(gnuradio-vlp2 SHARED ${vlp2_sources})
#target_link_libraries(gnuradio-vlp2-fortran ${LAPACK_LIBRARIES} ${BLAS_LIBRARIES})
target_link_libraries(gnuradio-vlp2 ${Boost_LIBRARIES} ${GNURADIO_ALL_LIBRARIES})
set_target_properties(gnuradio-vlp2 PROPERTIES DEFINE_SYMBOL "gnuradio_vlp2_EXPORTS")

if(APPLE)
    set_target_properties(gnuradio-vlp2 PROPERTIES
        INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib"
    )
endif(APPLE)

########################################################################
# Install built library files
########################################################################
install(TARGETS gnuradio-vlp2
    LIBRARY DESTINATION lib${LIB_SUFFIX} # .so/.dylib file
    ARCHIVE DESTINATION lib${LIB_SUFFIX} # .lib file
    RUNTIME DESTINATION bin              # .dll file
)

########################################################################
# Build and register unit test
########################################################################
include(GrTest)

include_directories(${CPPUNIT_INCLUDE_DIRS})

list(APPEND test_vlp2_sources
    ${CMAKE_CURRENT_SOURCE_DIR}/test_vlp2.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/qa_vlp2.cc
)

add_executable(test-vlp2 ${test_vlp2_sources})

target_link_libraries(
  test-vlp2
  ${ARMADILLO_LIBRARIES}
  ${GNURADIO_RUNTIME_LIBRARIES}
  ${Boost_LIBRARIES}
  ${CPPUNIT_LIBRARIES}
  gnuradio-vlp2
)

GR_ADD_TEST(test_vlp2 test-vlp2)
