cmake_minimum_required(VERSION 3.8)
project(ifopt_vendor)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)

macro(build_ifopt)
  set(cmake_commands)
  set(cmake_configure_args -Wno-dev)
  set(cmake_configure_args -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install)

  include(ExternalProject)
  externalproject_add(ifopt-ext
    GIT_REPOSITORY https://github.com/ethz-adrl/ifopt
    GIT_TAG master
    TIMEOUT 6000
    ${cmake_commands}
    CMAKE_ARGS
      ${cmake_configure_args}
  )

  # The external project will install to the build folder, but we'll install that on make install.
  install(
    DIRECTORY
      ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install/
    DESTINATION
      ${CMAKE_INSTALL_PREFIX}
  )
endmacro()

build_ifopt()
ament_export_include_directories(include)
ament_export_dependencies(
  ifopt
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  # set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  # set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()