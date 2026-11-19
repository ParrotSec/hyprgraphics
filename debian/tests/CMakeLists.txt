cmake_minimum_required(VERSION 3.19)
project(hyprgraphics)

set(CMAKE_CXX_STANDARD 26)
add_compile_options(
  -Wall
  -Wextra
  -Wpedantic
  -Wno-unused-parameter
  -Wno-unused-value
  -Wno-missing-field-initial
)

enable_testing()

find_package(PkgConfig REQUIRED)
pkg_check_modules(
  deps
  REQUIRED
  IMPORTED_TARGET
  pixman-1
  cairo
  hyprutils
  libjpeg
  libwebp
  libmagic
  libpng)

pkg_check_modules(
  JXL
  IMPORTED_TARGET
  libjxl
  libjxl_cms
  libjxl_threads
)

if(JXL_FOUND)
  add_compile_definitions(JXL_FOUND)
endif()

pkg_check_modules(
  HEIF
  IMPORTED_TARGET
  libheif
)

if(HEIF_FOUND)
  add_compile_definitions(HEIF_FOUND)
endif()

find_library(HYPRGRAPHICS_LIB hyprgraphics REQUIRED)

add_custom_target(tests)

add_executable(hyprgraphics_image "tests/image.cpp")
target_link_libraries(hyprgraphics_image PRIVATE ${HYPRGRAPHICS_LIB} PkgConfig::deps)
add_test(
  NAME "Image"
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/tests
  COMMAND hyprgraphics_image "image")
add_dependencies(tests hyprgraphics_image)

add_executable(hyprgraphics_arg "tests/arg.cpp")
target_link_libraries(hyprgraphics_arg PRIVATE ${HYPRGRAPHICS_LIB} PkgConfig::deps)
add_test(
  NAME "ARG"
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/tests
  COMMAND hyprgraphics_arg "image")
add_dependencies(tests hyprgraphics_arg)
