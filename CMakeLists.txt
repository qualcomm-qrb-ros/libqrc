cmake_minimum_required(VERSION 3.5)
project(libqrc)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -O0 -fPIC)
endif()

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(INCLUDE_DIRS
  include/app_msg/
  include/module/
  protocol/qrc/
  protocol/qrc/tinyframe/
  protocol/tinyframe/
)

include_directories(
  ${INCLUDE_DIRS}
)

set(LIBQRC_SRCS
  protocol/qrc_msg_management.c
  protocol/qrc/qrc.c
  protocol/qrc/qrc_threadpool.c
  protocol/tinyframe/TinyFrame.c
)

add_library(${PROJECT_NAME} SHARED
  ${LIBQRC_SRCS}
)
target_link_libraries(${PROJECT_NAME} qrc_udriver)
SET_TARGET_PROPERTIES(${PROJECT_NAME} PROPERTIES VERSION 1.0.0)

set(EXPORT_INCLUDE_DIRS
  include/app_msg/
  include/module/
)

install(
  DIRECTORY ${EXPORT_INCLUDE_DIRS}
  DESTINATION include
)

install(
  TARGETS ${PROJECT_NAME}
  EXPORT export_${PROJECT_NAME}
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include
)
