set (DEDISP_OBJS
  dedisp.cu
  )

# generate a cmake object library for all cu files
add_library(dedisp ${DEDISP_OBJS})

# make one library
set(DEDISP_LIB dedisp)

# Install headers
set(DEDISP_HEADERS
  dedisp.h
  )
install(FILES ${DEDISP_HEADERS} DESTINATION include)

# Install lib
install(TARGETS dedisp LIBRARY DESTINATION lib)
