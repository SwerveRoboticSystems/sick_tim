cmake_minimum_required(VERSION 2.8.3)
project(sick_tim_551)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  sensor_msgs
  diagnostic_updater
  dynamic_reconfigure
)

find_package(PkgConfig REQUIRED)
pkg_check_modules(libusb libusb-1.0)

find_package(Boost REQUIRED COMPONENTS system)

generate_dynamic_reconfigure_options(config/SickTim.cfg)

catkin_package(
  DEPENDS Boost libusb
  CATKIN_DEPENDS roscpp sensor_msgs diagnostic_updater dynamic_reconfigure
  LIBRARIES sick_tim_3xx
  INCLUDE_DIRS inc)

include_directories(inc ${catkin_INCLUDE_DIRS} ${libusb_INCLUDE_DIRS})

add_library(sick_tim_3xx
  src/sick_tim_common.cpp
  src/sick_tim_common_usb.cpp
  src/sick_tim_common_mockup.cpp
  src/abstract_parser.cpp
)
add_dependencies(sick_tim_3xx 
  ${PROJECT_NAME}_gencfg 
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(sick_tim_3xx
  ${catkin_LIBRARIES}
  ${libusb_LIBRARIES}
)

add_executable(sick_tim_551
  src/sick_tim_551.cpp
  src/sick_tim_common_tcp.cpp
  src/sick_tim_551_parser.cpp)
target_link_libraries(sick_tim_551 sick_tim_3xx)

install(
  TARGETS sick_tim_3xx
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(
  TARGETS sick_tim_551
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(
  FILES 
    include/${PROJECT_NAME}/abstract_parser.h
    include/${PROJECT_NAME}/sick_tim_common.h
    include/${PROJECT_NAME}/sick_tim_common_usb.h
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)


