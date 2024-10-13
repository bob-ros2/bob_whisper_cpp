cmake_minimum_required(VERSION 3.8)
project(bob_whisper_cpp)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find ROS dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)

# SDL2
find_package(SDL2 REQUIRED)
string(STRIP "${SDL2_LIBRARIES}" SDL2_LIBRARIES)

# whisper.cpp
find_package(PkgConfig REQUIRED)
pkg_check_modules(WHISPER REQUIRED whisper)

#message(WHISPER_INCLUDE_DIRS="${WHISPER_INCLUDE_DIRS}")
#message(WHISPER_LIBRARIES="${WHISPER_LIBRARIES}")
#message(WHISPER_LDFLAGS="${WHISPER_LDFLAGS}")

# common

set(TARGET common)

add_library(${TARGET} STATIC
  src/common.h
  src/common.cpp
  src/grammar-parser.h
  src/grammar-parser.cpp
  src/dr_wav.h
)

target_include_directories(${TARGET} PUBLIC
  ${WHISPER_INCLUDE_DIRS}
)

target_link_libraries(${TARGET} PRIVATE 
  ${WHISPER_LIBRARIES}
)

# common-sdl

set(TARGET common-sdl)

add_library(${TARGET} STATIC
    src/common-sdl.h
    src/common-sdl.cpp
)

target_include_directories(${TARGET} PUBLIC
  ${SDL2_INCLUDE_DIRS}
)

target_link_libraries(${TARGET} PRIVATE 
  ${SDL2_LIBRARIES}
)

# command

include_directories(
  ${SDL2_INCLUDE_DIR}
  ${WHISPER_INCLUDE_DIRS}
  ${rclcpp_INCLUDE_DIRS}
  ${std_msgs_INCLUDE_DIRS}
)

set(TARGET command)

add_executable(${TARGET} src/command.cpp)

target_link_libraries(${TARGET} PRIVATE 
  common 
  common-sdl 
  ${WHISPER_LDFLAGS}
  ${rclcpp_LIBRARIES} 
  ${std_msgs_LIBRARIES} 
  ${CMAKE_THREAD_LIBS_INIT}
)

install(TARGETS
  command 
  DESTINATION lib/${PROJECT_NAME})

ament_package()
