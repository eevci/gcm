cmake_minimum_required(VERSION 3.0.2)
project(gcm)
option(CodeCoverage "CodeCoverage" OFF)
## Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++17)

find_package(Boost REQUIRED)
find_package(GTest REQUIRED)
find_package(catkin REQUIRED COMPONENTS
        roscpp
        message_generation
        std_msgs
        gcm_msgs
)

catkin_package(
        INCLUDE_DIRS include
        LIBRARIES gcm
        CATKIN_DEPENDS  message_runtime roscpp gcm_msgs std_msgs 
        DEPENDS Boost
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
 include test/include
 ${catkin_INCLUDE_DIRS} ${Boost_INCLUDE_DIRS} ${GTEST_INCLUDE_DIRS}
)

set(SRC_FILES
        src/gcm_node.cpp
        src/drivers/ethernet/UDPDriver.cpp
        src/drivers/ethernet/TCPDriver.cpp
        src/drivers/serial/RS232Driver.cpp
)

## Declare a C++ library
add_library(${PROJECT_NAME} SHARED ${SRC_FILES} )
add_dependencies(${PROJECT_NAME} gcm_msgs_generate_messages_cpp)
add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})


add_executable(${PROJECT_NAME}_node ${SRC_FILES})
add_dependencies(${PROJECT_NAME}_node gcm_msgs_generate_messages_cpp)
add_dependencies(${PROJECT_NAME}_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Specify libraries to link a library or executable target against
 target_link_libraries(${PROJECT_NAME}_node
   ${catkin_LIBRARIES} ${Boost_LIBRARIES}
 )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
if (CATKIN_ENABLE_TESTING)
 catkin_add_gtest(${PROJECT_NAME}_test
         test/main.cpp
         test/gcm/drivers/ethernet/TestUDPDriver.cpp
         test/gcm/drivers/ethernet/TestTCPDriver.cpp
         test/gcm/drivers/ethernet/TestRS232Driver.cpp
         )
 add_dependencies(${PROJECT_NAME} gcm_msgs_generate_messages_cpp)
 add_dependencies(${PROJECT_NAME}_test ${${PROJECT_NAME}_test_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
 target_link_libraries(${PROJECT_NAME}_test ${PROJECT_NAME} ${catkin_LIBRARIES})
endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
