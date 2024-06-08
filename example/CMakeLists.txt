include_directories(${PROJECT_SOURCE_DIR}/src)

set (DEDISP_TEST_OBJS
  gasdev.c
  ran1.c
  )

# generate libs
add_library(dedisp_test ${DEDISP_TEST_OBJS})
set_target_properties(dedisp_test PROPERTIES POSITION_INDEPENDENT_CODE ${BUILD_SHARED_LIBS})

add_executable(testdedisp testdedisp.c)
target_link_libraries(testdedisp PUBLIC dedisp dedisp_test -lm)

# Install library
install(TARGETS dedisp_test LIBRARY DESTINATION lib)

# Install executables
install(TARGETS testdedisp RUNTIME DESTINATION bin)
