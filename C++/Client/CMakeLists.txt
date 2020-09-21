cmake_minimum_required(VERSION 2.8)

project(Client)

 list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake")

# find QT5 and required components
find_package(Qt5 COMPONENTS REQUIRED Core Gui Widgets)
find_package(MAVSDK REQUIRED)
find_package(mongocxx REQUIRED)
find_package(bsoncxx REQUIRED)

# need this to include the auto generated ui_mainwindow.h
# file from the qt5_wrap_ui call below.
set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(project_sources mainwindow.cpp main.cpp mqttconnection.cpp)
set(project_headers mainwindow.h mqttconnection.h)
set(project_ui mainwindow.ui)

# wrap the ui file to a c++ header
qt5_wrap_ui(ui_wrap ${project_ui})
qt5_wrap_cpp(moc_sources ${project_headers})

include_directories(${MOSQUITTO_INCLUDE_DIRS})
include_directories(${MOSQUITTOPP_INCLUDE_DIRS})
include_directories(${LIBMONGOCXX_INCLUDE_DIR})
include_directories(${LIBBSONCXX_INCLUDE_DIR})

add_executable(client_gui
        ${project_sources}
        ${project_headers}
        ${ui_wrap}
        ${moc_sources})

target_link_libraries(client_gui
        PUBLIC
        Qt5::Core
        Qt5::Gui
        Qt5::Widgets
        MAVSDK::mavsdk_telemetry
        MAVSDK::mavsdk_action
        MAVSDK::mavsdk
        /usr/lib/x86_64-linux-gnu/libmosquittopp.so)

target_link_libraries(client_gui PRIVATE mongo::bsoncxx_shared)

target_link_libraries(client_gui PRIVATE mongo::mongocxx_shared)



