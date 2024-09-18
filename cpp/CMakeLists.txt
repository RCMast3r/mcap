cmake_minimum_required(VERSION 3.15...3.30)
project(mcap_cmake LANGUAGES CXX)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

add_library(mcap INTERFACE)

find_package(PkgConfig REQUIRED)
find_package(zstd REQUIRED)
pkg_check_modules(lz4 REQUIRED IMPORTED_TARGET liblz4)

target_include_directories(mcap INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/mcap/include/mcap>
    $<INSTALL_INTERFACE:include/mcap>
)

target_link_libraries(mcap INTERFACE PkgConfig::lz4 zstd::libzstd)

include(GNUInstallDirs)
install(TARGETS mcap
  EXPORT mcapTargets
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  PUBLIC_HEADER DESTINATION include
)

# NAMESPACE sets the namespace that your target will live within.
# you can have multiple targets under the same namespace
install(EXPORT mcapTargets
  FILE mcapTargets.cmake
  NAMESPACE mcap::
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/mcap
)

# this looks for your .cmake.in file that you created in step 1.
# if you set the directory differently you may need to handle this differently
include(CMakePackageConfigHelpers)
configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/mcapConfig.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/mcapConfig.cmake"
  INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/mcap
)

install(FILES
  "${CMAKE_CURRENT_BINARY_DIR}/mcapConfig.cmake"
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/mcap
)

# actually install your header files
install(
    DIRECTORY mcap/include/mcap
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include
    )
