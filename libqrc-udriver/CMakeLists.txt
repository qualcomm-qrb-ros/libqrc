cmake_minimum_required(VERSION 3.8)
project(qrc_udriver)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

include_directories(
  include
)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -O0 -fPIC)
endif()

find_package(ament_cmake REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(LIBGPIOD REQUIRED libgpiod)

add_library(${PROJECT_NAME} SHARED
  src/qti_qrc_udriver.c
  src/qti_qrc_uart.c
)

if(LIBGPIOD_VERSION VERSION_GREATER_EQUAL "2.0.0")
  target_compile_definitions(${PROJECT_NAME} PRIVATE LIBGPIOD_V2)
else()
  target_compile_definitions(${PROJECT_NAME} PRIVATE LIBGPIOD_V1)
endif()

SET_TARGET_PROPERTIES(${PROJECT_NAME} PROPERTIES SOVERSION 1 VERSION 1.1.1)
target_link_libraries(${PROJECT_NAME} gpiod)
install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)
install(FILES include/qti_qrc_udriver.h DESTINATION include)

add_executable(${PROJECT_NAME}_test
  test/qti_qrc_test.c
)
target_link_libraries(${PROJECT_NAME}_test ${PROJECT_NAME})
install(TARGETS ${PROJECT_NAME}_test
  RUNTIME DESTINATION bin)
ament_export_include_directories(include)
ament_package()
