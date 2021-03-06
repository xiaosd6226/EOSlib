if(${verbose_traversal})
  message(STATUS "Entered EOStypes subdirectory")
endif(${verbose_traversal})

message(STATUS "EOStypes\n")

list(APPEND EOStypes_libs IdealGas StiffenedGas Hayes HayesBM GenHayes
                          MieGruneisen Davis JWL Porous PTequilibrium )

set( tmpfile "${CMAKE_CURRENT_BINARY_DIR}/EOStypes.cmake" )
file(WRITE ${tmpfile} "add_library( EOStypes ${${projectName}_policy}\n")
foreach(EOStype ${EOStypes_libs})
  add_subdirectory(${EOStype})
  #set(EOStypes-obj "${EOStypes-obj} $<TARGET_OBJECTS:${EOStype}>")
  file(APPEND ${tmpfile} "    $<TARGET_OBJECTS:${EOStype}-obj>\n")
endforeach(EOStype)

file(APPEND ${tmpfile}
  "    )\n"
  "  set_property(TARGET EOStypes PROPERTY CXX_STANDARD_REQUIRED ON)\n"
  "  set_property(TARGET EOStypes PROPERTY INTERPROCEDURAL_OPTIMIZATION ON)\n"
  "  set_property(TARGET EOStypes PROPERTY LINKER_LANGUAGE CXX)\n"
  #  "  target_link_libraries(EOStypes EOS)\n" )
  )
include(${tmpfile})

#add_library( EOStypes ${${projectName}_policy}
#                          ${EOStypes-obj}
#                          $<TARGET_OBJECTS:EOS-obj> )
#  set_property(TARGET EOStypes PROPERTY CXX_STANDARD 11)
#  set_property(TARGET EOStypes PROPERTY CXX_STANDARD_REQUIRED ON)
#  set_property(TARGET EOStypes PROPERTY INTERPROCEDURAL_OPTIMIZATION ON)
#  set_property(TARGET EOStypes PROPERTY LINKER_LANGUAGE CXX)
#
# Tests
#
set(BLESSED_OUTPUT ${EOSLIB_BLESSED_OUTPUT}/EOStypes)
set( BIN ${CMAKE_CURRENT_BINARY_DIR} )
set( CMAKE_RUNTIME_OUTPUT_DIRECTORY ${BIN} )

list( APPEND tests TestRiemann )

foreach(test ${tests})
  add_executable(${test} ${test}.C)
  target_link_libraries(${test} EOStypes EOS)
  add_test(NAME ${test}
    COMMAND ${CMAKE_COMMAND}
    -D test_cmd=${BIN}/${test}
    -D test_args:string=${args}
    -D out_blessed=${BLESSED_OUTPUT}/TestRiemannOut.blessed
    -D err_blessed=${BLESSED_OUTPUT}/TestRiemannErr.blessed 
    -P ${CMAKE_BINARY_DIR}/TEST/compare_outputs.cmake
   )
 set_tests_properties(TestRiemann PROPERTIES ENVIRONMENT "${CTEST_ENVIRONMENT}")
endforeach(test)


if(${verbose_traversal})
  message(STATUS "Exiting EOStypes subdirectory")
endif(${verbose_traversal})
