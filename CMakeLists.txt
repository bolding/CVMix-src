cmake_minimum_required( VERSION 3.9 )

project( cvmix VERSION 4.0.1 LANGUAGES Fortran )

# Use solution folders in IDEs
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Use standard GNU installation directories
if ( NOT WIN32 )
   include( GNUInstallDirs )
endif()

# Configuration options
option( CVMIX_BUILD_STATIC_LIBS "Build static library" ON )
option( CVMIX_BUILD_SHARED_LIBS "Build shared library" ON )
option( CVMIX_BUILD_DRIVER "Build CVMix example/test driver" OFF )
option( CVMIX_USE_NetCDF "Enable NetCDF format" OFF )

# Set default build type to Release if not specified
if( NOT CMAKE_CONFIGURATION_TYPES AND NOT CMAKE_BUILD_TYPE )
   message( STATUS "Setting default build type to 'Release'.  Set CMAKE_BUILD_TYPE variable to change build types." )
   set_property( CACHE CMAKE_BUILD_TYPE PROPERTY VALUE "Release" )
endif()

set( CMAKE_Fortran_MODULE_DIRECTORY ${CMAKE_BINARY_DIR}/modules )
set( CMAKE_POSITION_INDEPENDENT_CODE ON )

add_subdirectory( src )

# Install library targets
install( TARGETS ${LIB_TARGETS} EXPORT cvmixConfig
	 ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
	 LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} )
install( DIRECTORY ${CMAKE_BINARY_DIR}/modules/ EXPORT cvmixConfig
	 DESTINATION  include/cvmix
	 FILES_MATCHING
	 PATTERN "*.mod" )
install(EXPORT cvmixConfig DESTINATION cmake)

