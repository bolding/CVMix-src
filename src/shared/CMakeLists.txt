add_library(cvmix_objects OBJECT .)
set_property( TARGET cvmix_objects PROPERTY FOLDER shared )

target_sources( cvmix_objects PRIVATE
   cvmix_kinds_and_types.F90
   cvmix_background.F90
   cvmix_convection.F90
   cvmix_ddiff.F90
   cvmix_kpp.F90
   cvmix_math.F90
   cvmix_put_get.F90
   cvmix_shear.F90
   cvmix_tidal.F90
   cvmix_utils.F90
)

# Check if shared or static builds are turned off
if( NOT CVMIX_BUILD_STATIC_LIBS )
   message( STATUS "Turning STATIC builds OFF" )
endif()
if( NOT CVMIX_BUILD_SHARED_LIBS )
   message( STATUS "Turning SHARED builds OFF" )
endif()

# Note - @bolding:
# Building static and shared libs require an ugly construct to build on both
# Windows and Linux. Windows will not create the libraries unless a Fortran
# file is explicitly given - and Linux complains (I think due to a race
# condition) if it is. Therefor the construct below - a proper solution is
# most welcome.

# Add static lib target
if( CVMIX_BUILD_STATIC_LIBS )
   if ( WIN32 )
      add_library( cvmix_static STATIC ${CMAKE_SOURCE_DIR}/src/dummy.F90 $<TARGET_OBJECTS:cvmix_objects> )
   else()
      add_library( cvmix_static STATIC $<TARGET_OBJECTS:cvmix_objects> )
   endif()
   list( APPEND LIB_TARGETS cvmix_static )
endif()

# Add shared lib target
if( CVMIX_BUILD_SHARED_LIBS )
   if ( WIN32 )
      add_library( cvmix_shared SHARED ${CMAKE_SOURCE_DIR}/src/dummy.F90 $<TARGET_OBJECTS:cvmix_objects> )
   else()
      add_library( cvmix_shared SHARED $<TARGET_OBJECTS:cvmix_objects> )
   endif()
   list( APPEND LIB_TARGETS cvmix_shared )
endif()

# Set common lib target properties
foreach( _lib IN LISTS LIB_TARGETS )
   target_compile_definitions( ${_lib} PUBLIC "${PUBLIC_FLAGS}" )
   target_include_directories( ${_lib}
      PUBLIC
         $<INSTALL_INTERFACE:include/cvmix>
      PRIVATE
         $<BUILD_INTERFACE:${PROJECT_BINARY_DIR}/${INCLUDE_DIR}> )
   set_target_properties( ${_lib} PROPERTIES OUTPUT_NAME cvmix)
endforeach()
