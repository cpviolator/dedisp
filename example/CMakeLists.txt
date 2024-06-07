include_directories(../src)

set (DEDISP_TEST_OBJS
  gasdev.c
  ran1.c
  )

# generate a cmake object library for all test files
add_library(dedisp_test ${DEDISP_TEST_OBJS})

add_executable(testdedisp testdedisp.c)
target_link_libraries(testdedisp PUBLIC dedisp dedisp_test -lm)
