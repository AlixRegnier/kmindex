add_library(sdsl INTERFACE)

option(FORCE_BUILD_SDSL "Always build, instead of using the system version." OFF)
set(SDSL_DIR ${EXT_INSTALL_DIR}/sdsl)

include(ExternalProject)

# Download and build SDSL as an External Project
ExternalProject_Add(SDSL-external
    GIT_REPOSITORY https://github.com/simongog/sdsl-lite.git
    GIT_TAG v2.1.1
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${SDSL_DIR} -DBUILD_SHARED_LIBS=OFF
)


target_include_directories(sdsl SYSTEM INTERFACE
  "${SDSL_DIR}/include"
)

target_link_libraries(sdsl INTERFACE
  "${SDSL_DIR}/lib/${CMAKE_STATIC_LIBRARY_PREFIX}sdsl${CMAKE_STATIC_LIBRARY_SUFFIX}"
)

add_dependencies(sdsl SDSL-external)

