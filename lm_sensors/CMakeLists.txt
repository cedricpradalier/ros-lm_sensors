cmake_minimum_required(VERSION 2.4.6)
project(lm_sensors)
if(USE_ROSBUILD)

    include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

    # Set the build type.  Options are:
    #  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
    #  Debug          : w/ debug symbols, w/o optimization
    #  Release        : w/o debug symbols, w/ optimization
    #  RelWithDebInfo : w/ debug symbols, w/ optimization
    #  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
    #set(ROS_BUILD_TYPE RelWithDebInfo)

    rosbuild_init()

    #set the default path for built executables to the "bin" directory
    set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
    #set the default path for built libraries to the "lib" directory
    set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

    #uncomment if you have defined messages
    rosbuild_genmsg()
    #uncomment if you have defined services
    #rosbuild_gensrv()

    #common commands for building c++ executables and libraries
    #rosbuild_add_library(${PROJECT_NAME} src/example.cpp)
    #target_link_libraries(${PROJECT_NAME} another_library)
    #rosbuild_add_boost_directories()
    #rosbuild_link_boost(${PROJECT_NAME} thread)
    rosbuild_add_executable(lm_sensors src/main.cpp
        src/LMSensor.cpp src/LMSensorList.cpp)
    target_link_libraries(lm_sensors sensors)
else()
    find_package(catkin REQUIRED 
        COMPONENTS roscpp diagnostic_msgs diagnostic_updater
        message_generation)

    # ROS message generation
    add_message_files(DIRECTORY msg FILES Sensor.msg SensorList.msg)
    generate_messages(DEPENDENCIES std_msgs)

    catkin_package(
        CATKIN_DEPENDS roscpp diagnostic_updater diagnostic_msgs
        message_runtime
        #DEPENDS 
    )

    find_library(SENSORS_LIB NAMES sensors PATHS
        /usr/lib /usr/share/lib
        /usr/lib/x86_64-linux-gnu
        /usr/lib/i386-linux-gnu)

    include_directories(
        include
        ${catkin_INCLUDE_DIRS}
    )

    add_executable(lm_sensors src/main.cpp
        src/LMSensor.cpp src/LMSensorList.cpp)
    target_link_libraries(lm_sensors ${SENSORS_LIB} ${catkin_LIBRARIES})

    install(TARGETS lm_sensors
    ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    )

endif()
