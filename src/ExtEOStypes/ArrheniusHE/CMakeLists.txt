if(${verbose_traversal})
  message(STATUS "Entered ArrheniusHE subdirectory")
endif(${verbose_traversal})
#
# headers
#
file(GLOB ArrheniusHE_h "*.h")
list( APPEND HEADER_FILES ${ArrheniusHE_h} )
set( HEADER_FILES ${HEADER_FILES} PARENT_SCOPE )

file(GLOB ArrheniusHE_h  RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" "*.h")
foreach( file_h ${ArrheniusHE_h})
    execute_process(COMMAND "${CMAKE_COMMAND}" -E create_symlink
                                ${CMAKE_CURRENT_SOURCE_DIR}/${file_h}
                                ${HEADER_DIR}/${file_h})
endforeach( file_h)
#
# SOURCE
#
execute_process(COMMAND "${CMAKE_COMMAND}" -E create_symlink
                                ${CMAKE_CURRENT_SOURCE_DIR}
                                ${CMAKE_CURRENT_BINARY_DIR}/SOURCE)
#
# library
#
file(GLOB ArrheniusHE_src  RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" "Subs*/*.C")

add_library( ArrheniusHE-obj OBJECT ${ArrheniusHE_src} )
  set_property(TARGET ArrheniusHE-obj PROPERTY POSITION_INDEPENDENT_CODE ON )
  set_property(TARGET ArrheniusHE-obj PROPERTY CXX_STANDARD 11)
  set_property(TARGET ArrheniusHE-obj PROPERTY CXX_STANDARD_REQUIRED ON)
  set_property(TARGET ArrheniusHE-obj PROPERTY INTERPROCEDURAL_OPTIMIZATION ON)

add_library( ArrheniusHE ${${projectName}_policy}
                 $<TARGET_OBJECTS:ArrheniusHE-obj> )
  set_property(TARGET ArrheniusHE PROPERTY CXX_STANDARD 11)
  set_property(TARGET ArrheniusHE PROPERTY CXX_STANDARD_REQUIRED ON)
  set_property(TARGET ArrheniusHE PROPERTY INTERPROCEDURAL_OPTIMIZATION ON)
  target_link_libraries(ArrheniusHE ExtEOS PTequilibrium StiffenedGas)
#
# Tests
#
add_subdirectory(Tests)

if(${verbose_traversal})
  message(STATUS "Exiting ArrheniusHE subdirectory")
endif(${verbose_traversal})
