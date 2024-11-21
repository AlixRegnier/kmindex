add_library(BlockCompressor INTERFACE)

option(FORCE_BUILD_BLOCKCOMPRESSOR "Always build, instead of using the system version." OFF)
option(BLOCKCOMPRESSOR_HEADER_ONLY "Use header only version of BlockCompressor." OFF)

if (NOT FORCE_BUILD_BLOCKCOMPRESSOR AND NOT FORCE_BUILD_ALL)

  find_package(BlockCompressor ${BLOCKCOMPRESSOR_MIN_REQUIRED_VERSION} CONFIG QUIET)

  if (TARGET BlockCompressor::BlockCompressor)

    get_target_property(BLOCKCOMPRESSOR_INCLUDE_DIRS BlockCompressor::BlockCompressor INTERFACE_INCLUDE_DIRECTORIES)

    find_library(BLOCKCOMPRESSOR_LIBRARY BlockCompressor)

    #file(READ ${FMT_INCLUDE_DIRS}/fmt/core.h fmt_core)

    # if (NOT fmt_core MATCHES "FMT_VERSION ([0-9]+)([0-9][0-9])([0-9][0-9])")
    #   set(FMT_VERSION "Unknown version")
    # endif()

    #set(FMT_VERSION "${CMAKE_MATCH_1}.${CMAKE_MATCH_2}.${CMAKE_MATCH_3}")

    if (BLOCKCOMPRESSOR_HEADER_ONLY)
      target_link_libraries(BlockCompressor INTERFACE BlockCompressor::BlockCompressor-header-only)
      message(STATUS "Found BlockCompressor: header-only") #(found suitable version \"${FMT_VERSION}\", minimum required is \"${FMT_MIN_REQUIRED_VERSION}\")")
    else()
      target_link_libraries(BlockCompressor INTERFACE BlockCompressor::BlockCompressor)
      message(STATUS "Found BlockCompressor: ${BLOCKCOMPRESSOR_LIBRARY}") #(found suitable version \"${FMT_VERSION}\", minimum required is \"${FMT_MIN_REQUIRED_VERSION}\")")
    endif()

    return()

  else()
      message(STATUS "BLOCKCOMPRESOR not found: use bundled version.")
  endif()

endif()


set(BLOCKCOMPRESSOR_INCLUDE_DIR ${THIRD_DIR}/blockcompressor/BLOCKCOMPRESSOR-external/src/BLOCKCOMPRESSOR-external/plugins/BlockCompressor)

include(ExternalProject)

# Download and build kmtricks as an External Project
ExternalProject_Add(BLOCKCOMPRESSOR-external
    PREFIX BLOCKCOMPRESSOR-external
    GIT_REPOSITORY https://github.com/AlixRegnier/kmtricks.git

    CMAKE_ARGS 
        -DCMAKE_INSTALL_PREFIX=${BLOCKCOMPRESSOR_DIR} -DWITH_PLUGIN=ON

    INSTALL_COMMAND ""
)

target_include_directories(BlockCompressor SYSTEM INTERFACE
  "${BLOCKCOMPRESSOR_INCLUDE_DIR}/include"
)

# Need fix on variable BLOCKCOMPRESSOR_DIR
if (NOT BLOCKCOMPRESSOR_HEADER_ONLY)
  target_link_libraries(BlockCompressor INTERFACE
    "${BLOCKCOMPRESSOR_DIR}/${CMAKE_INSTALL_LIBDIR}/${CMAKE_STATIC_LIBRARY_PREFIX}BlockCompressor${CMAKE_STATIC_LIBRARY_SUFFIX}"
  )
else()
  target_compile_definitions(BlockCompressor INTERFACE BLOCKCOMPRESSOR_HEADER_ONLY)
endif()

add_dependencies(BlockCompressor BLOCKCOMPRESSOR-external)
target_compile_definitions(BlockCompressor INTERFACE DMAX_C=${MAX_C})
