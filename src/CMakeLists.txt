# Object files
set (DEDISP_OBJS
  dedisp.cu
  )

# Headers
set(DEDISP_HEADERS
  dedisp.h
  )

# generate libs
add_library(dedisp ${DEDISP_OBJS})
set_target_properties(dedisp PROPERTIES POSITION_INDEPENDENT_CODE ${BUILD_SHARED_LIBS})

# Install headers
install(FILES ${DEDISP_HEADERS} DESTINATION include)

# Install lib
install(TARGETS dedisp LIBRARY DESTINATION lib)
