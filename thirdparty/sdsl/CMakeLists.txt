add_library(sdsl INTERFACE)

option(FORCE_BUILD_SDSL "Always build, instead of using the system version." OFF)
option(SDSL_HEADER_ONLY "Use header only version of sdsl." OFF)

#set(SDSL_MIN_REQUIRED_VERSION "8.1.0...<9.0.0")

# if (NOT FORCE_BUILD_SDSL AND NOT FORCE_BUILD_ALL)

#   find_package(sdsl ${SDSL_MIN_REQUIRED_VERSION} CONFIG QUIET)

#   if (TARGET sdsl::sdsl)

#     get_target_property(SDSL_INCLUDE_DIRS sdsl::sdsl INTERFACE_INCLUDE_DIRECTORIES)

#     find_library(SDSL_LIBRARY sdsl)

#     file(READ ${SDSL_INCLUDE_DIRS}/sdsl/core.h sdsl_core)

#     if (NOT sdsl_core MATCHES "SDSL_VERSION ([0-9]+)([0-9][0-9])([0-9][0-9])")
#       set(SDSL_VERSION "Unknown version")
#     endif()

#     set(SDSL_VERSION "${CMAKE_MATCH_1}.${CMAKE_MATCH_2}.${CMAKE_MATCH_3}")

#     if (SDSL_HEADER_ONLY)
#       target_link_libraries(sdsl INTERFACE sdsl::sdsl-header-only)
#       message(STATUS "Found SDSL: header-only (found suitable version \"${SDSL_VERSION}\", minimum required is \"${SDSL_MIN_REQUIRED_VERSION}\")")
#     else()
#       target_link_libraries(sdsl INTERFACE sdsl::sdsl)
#       message(STATUS "Found SDSL: ${SDSL_LIBRARY} (found suitable version \"${SDSL_VERSION}\", minimum required is \"${SDSL_MIN_REQUIRED_VERSION}\")")
#     endif()

#     return()

#   else()
#       message(STATUS "SDSL not found: use bundled version.")
#   endif()

# endif()

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

if (NOT SDSL_HEADER_ONLY)
  target_link_libraries(sdsl INTERFACE
    "${SDSL_DIR}/${CMAKE_INSTALL_LIBDIR}/${CMAKE_STATIC_LIBRARY_PREFIX}sdsl${CMAKE_STATIC_LIBRARY_SUFFIX}"
  )
else()
  target_compile_definitions(sdsl INTERFACE SDSL_HEADER_ONLY)
endif()

add_dependencies(sdsl SDSL-external)

