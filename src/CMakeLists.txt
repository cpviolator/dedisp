set (DEDISP_OBJS
  dedisp.cu
  )

# generate a cmake object library for all cu files
add_library(dedisp ${DEDISP_OBJS})

# make one library
set(DEDISP_LIB dedisp)
