# CVMix library sources
add_subdirectory( shared )

if(CVMIX_BUILD_DRIVER)

   add_executable( cvmix_driver . )

   if(CVMIX_USE_NetCDF)
      add_compile_definitions(_NETCDF)
      include_directories($ENV{NetCDF_INCLUDE})
      link_directories($ENV{NetCDF_LIBDIR})
   endif(CVMIX_USE_NetCDF)

   add_library(cvmix_io STATIC .)
   set_property( TARGET cvmix_io PROPERTY FOLDER drivers )
   target_sources( cvmix_io PRIVATE cvmix_io.F90 )
   target_link_libraries( cvmix_io PRIVATE cvmix_static )
   if(CVMIX_USE_NetCDF)
      target_link_libraries( cvmix_io PRIVATE netcdff )
   endif(CVMIX_USE_NetCDF)

   add_subdirectory( drivers )

   target_sources( cvmix_driver PRIVATE cvmix_driver.F90 )
   set_property( TARGET cvmix_driver PROPERTY FOLDER driver )
   target_include_directories( cvmix_driver PRIVATE ${CMAKE_BINARY_DIR}/modules )
   target_link_libraries( cvmix_driver PRIVATE cvmix_drivers ) 

   install( TARGETS cvmix_driver EXPORT cvmixConfig
   RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR} )

endif()
