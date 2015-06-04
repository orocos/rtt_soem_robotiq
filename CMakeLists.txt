cmake_minimum_required(VERSION 2.6.3)

project(soem_robotiq_drivers)
# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
#set(ROS_BUILD_TYPE RelWithDebInfo)

# Set the CMAKE_PREFIX_PATH in case you're not using Orocos through
# ROS for helping these find commands find RTT.
find_package(OROCOS-RTT REQUIRED ${RTT_HINTS} )
# Defines the orocos_* cmake macros. See that file for additional
# documentation.
include(${OROCOS-RTT_USE_FILE_PATH}/UseOROCOS-RTT.cmake)

# Do setup in case of ros package, If ROS_ROOT is set, it is                                                                                                 # recommended to use RTT/OCL through the ros packages.                                                                                                       
#uncomment if you have defined messages
if(NOT ORO_USE_ROSBUILD)
  find_package(catkin REQUIRED message_generation rtt_roscomm)
  add_message_files( FILES
    FingerMsg.msg
    RobotiqMsg.msg
    )

  generate_messages()
endif()

if(ORO_USE_ROSBUILD)
  rosbuild_genmsg()
  rosbuild_include(rtt_rosnode GenerateRTTtypekit)
endif()

ros_generate_rtt_typekit(soem_robotiq_drivers)

find_package(soem REQUIRED)
include_directories(${soem_INCLUDE_DIRS})

orocos_use_package(soem_master)

orocos_plugin(soem_Robotiq src/soem_robotiq_drivers.cpp src/soem_robotiq_3Finger.cpp)

target_link_libraries(soem_Robotiq ${soem_LIBRARIES})

if(NOT ORO_USE_ROSBUILD)
  add_dependencies(soem_Robotiq ${PROJECT_NAME}_generate_messages_cpp)
endif()
orocos_generate_package()
