cmake_minimum_required(VERSION 3.5)
project(hash_library_vendor)

## Compile options
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
  set(CMAKE_CXX_EXTENSIONS OFF)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -Werror)
endif()

## Dependencies
find_package(ament_cmake_auto REQUIRED)
ament_auto_find_build_dependencies()

## Define vendor macro
macro(build_vendor_package)
  set(git_tag "hash_library_v8")
  include(FetchContent)
  fetchcontent_declare(hash_library_vendor
    GIT_REPOSITORY https://github.com/stbrumme/hash-library.git
    GIT_TAG ${git_tag}
  )
  fetchcontent_makeavailable(hash_library_vendor)

  file(GLOB HASH_LIBRARY_SOURCE "${CMAKE_CURRENT_BINARY_DIR}/_deps/${PROJECT_NAME}-src/*.cpp")
  file(GLOB HASH_LIBRARY_INCLUDE "${CMAKE_CURRENT_BINARY_DIR}/_deps/${PROJECT_NAME}-src/*.h")

  ament_auto_add_library(hash_library_vendor SHARED
    ${HASH_LIBRARY_SOURCE}
    ${HASH_LIBRARY_INCLUDE}
  )

  install(
    FILES
      ${HASH_LIBRARY_INCLUDE}
    DESTINATION
      ${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME}
  )
endmacro()

## Call vendor macro
build_vendor_package()
ament_export_include_directories(include)

## Tests
if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

## Package
ament_auto_package()
