configure_file("${CMAKE_SOURCE_DIR}/lib/config.hpp.in" "${CMAKE_BINARY_DIR}/include/kmindex/config.hpp")

set(KMINDEX_LIB "kmindex-lib")
if (NOT APPLE)
  set(CMAKE_INSTALL_RPATH $ORIGIN)
endif()


file(GLOB_RECURSE KMINDEX_SOURCES "src/*.cpp")
add_library(${KMINDEX_LIB} ${KMINDEX_SOURCES})

set_target_properties(${KMINDEX_LIB} PROPERTIES OUTPUT_NAME "kmindex")
target_compile_definitions(${KMINDEX_LIB} PUBLIC KMTRICKS_PUBLIC)

add_library(${KMINDEX_LIB}::${KMINDEX_LIB} ALIAS ${KMINDEX_LIB})

target_compile_features(${KMINDEX_LIB} PUBLIC cxx_std_17)

target_link_libraries(${KMINDEX_LIB} PUBLIC
  $<BUILD_INTERFACE:build_type_flags>
  $<BUILD_INTERFACE:warning_flags>
  $<BUILD_INTERFACE:fmt>
  $<BUILD_INTERFACE:kmtricks>
  $<BUILD_INTERFACE:json>
  $<BUILD_INTERFACE:mio_mmap>
  $<BUILD_INTERFACE:xxhash>
  $<BUILD_INTERFACE:sha1h>
  $<BUILD_INTERFACE:semver>
  $<BUILD_INTERFACE:bitpack>
  $<BUILD_INTERFACE:BlockDecompressor>
  $<BUILD_INTERFACE:zstd>
  $<BUILD_INTERFACE:sdsl>
)

add_dependencies(${KMINDEX_LIB} deps)

target_include_directories(${KMINDEX_LIB} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/lib/include>
  $<BUILD_INTERFACE:${CMAKE_BINARY_DIR}/include>
  $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor>
  $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor/ZSTD>
  $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor/LZMA>
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

set_target_properties(${KMINDEX_LIB} PROPERTIES
  SOVERSION ${CMAKE_PROJECT_VERSION_MAJOR}
  VERSION ${CMAKE_PROJECT_VERSION}
)

