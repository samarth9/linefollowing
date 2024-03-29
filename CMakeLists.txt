cmake_minimum_required(VERSION 2.8.3)
project(linefollowing)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  actionlib
  actionlib_msgs
  roscpp
  rospy
  rosserial_arduino
  rosserial_client
  std_msgs
  message_generation
)
find_package(
	OpenCV
)
## System dependencies are found with CMake's conventions
# find_package(Boost REQUIRED COMPONENTS system)


## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependencies might have been
##     pulled in transitively but can be declared for certainty nonetheless:
##     * add a build_depend tag for "message_generation"
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

## Generate messages in the 'msg' folder
add_message_files(
   FILES
   line_arduino.msg
 )

## Generate services in the 'srv' folder
# add_service_files(
#   FILES
#   Service1.srv
#   Service2.srv
# )

## Generate actions in the 'action' folder
add_action_files(
  DIRECTORY action
  FILES Align.action
)

## Generate added messages and services with any dependencies listed here
 generate_messages(
   DEPENDENCIES
   std_msgs actionlib_msgs actionlib
 )

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES lineFollowing
   CATKIN_DEPENDS roscpp rospy std_msgs message_runtime rosserial_arduino rosserial_client actionlib actionlib_msgs
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
# include_directories(include)
include_directories(include
  ${catkin_INCLUDE_DIRS}
)

rosserial_generate_ros_lib(
  PACKAGE rosserial_arduino
  SCRIPT make_libraries.py
)
   
rosserial_configure_client(
  DIRECTORY src
  TOOLCHAIN_FILE ${ROSSERIAL_ARDUINO_TOOLCHAIN}
)
   
rosserial_add_client_target(src hello ALL) #this hello need not be changed for anything
rosserial_add_client_target(src hello-upload) #this hello need not be changed for anything


add_executable(imu src/imu.cpp)
target_link_libraries(imu ${catkin_LIBRARIES})
add_dependencies(imu linefollowing_generate_messages_cpp)

add_executable(imuTemp src/imuTemp.cpp)
target_link_libraries(imuTemp ${catkin_LIBRARIES})
add_dependencies(imuTemp linefollowing_generate_messages_cpp)

add_executable(taskHandler src/taskHandler.cpp)
target_link_libraries(taskHandler ${catkin_LIBRARIES})
add_dependencies(taskHandler linefollowing_generate_messages_cpp)

add_executable(taskHandlerTest src/taskHandlerTest.cpp)
target_link_libraries(taskHandlerTest ${catkin_LIBRARIES})
add_dependencies(taskHandlerTest linefollowing_generate_messages_cpp)

add_executable(LineOdroidROS src/LineOdroidROS.cpp)
target_link_libraries(LineOdroidROS ${catkin_LIBRARIES})
target_link_libraries(LineOdroidROS  ${OpenCV_LIBS} )
add_dependencies(LineOdroidROS linefollowing_generate_messages_cpp)


add_executable(LineNew src/LineNew.cpp)
target_link_libraries(LineNew ${catkin_LIBRARIES})
target_link_libraries(LineNew  ${OpenCV_LIBS} )
add_dependencies(LineNew linefollowing_generate_messages_cpp)

#add_executable(kalman src/kalman.cpp)
#target_link_libraries(kalman ${catkin_LIBRARIES})
#target_link_libraries(kalman  ${OpenCV_LIBS} )
#add_dependencies(kalman linefollowing_generate_messages_cpp)

## Declare a cpp library
# add_library(lineFollowing
#   src/${PROJECT_NAME}/lineFollowing.cpp
# )

## Declare a cpp executable
# add_executable(lineFollowing_node src/lineFollowing_node.cpp)

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
# add_dependencies(lineFollowing_node lineFollowing_generate_messages_cpp)

## Specify libraries to link a library or executable target against
# target_link_libraries(lineFollowing_node
#   ${catkin_LIBRARIES}
# )

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS lineFollowing lineFollowing_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_lineFollowing.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
