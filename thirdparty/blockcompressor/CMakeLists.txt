add_library(ConfigurationLiterate SHARED
  ${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor/ConfigurationLiterate.cpp
)
target_include_directories(ConfigurationLiterate PRIVATE
  ${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor
)

add_library(BlockDecompressor SHARED
  ${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor/BlockDecompressor.cpp
)
target_link_libraries(BlockDecompressor PUBLIC ConfigurationLiterate sdsl)
target_include_directories(BlockDecompressor PRIVATE
  ${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor
)
add_library(BlockDecompressorZSTD SHARED
  ${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor/ZSTD/BlockDecompressorZSTD.cpp
)
target_include_directories(BlockDecompressorZSTD PUBLIC
  ${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor/ZSTD
  ${CMAKE_SOURCE_DIR}/thirdparty/kmtricks/plugins/BlockCompressor
)
target_link_libraries(BlockDecompressorZSTD PUBLIC BlockDecompressor zstd sdsl)
target_compile_definitions(BlockDecompressorZSTD INTERFACE DMAX_C=${MAX_C})
