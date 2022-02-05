cmake_minimum_required(VERSION 3.10)

project(sampsdk VERSION 1.0.0 LANGUAGES CXX)

include(GNUInstallDirs)

if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
    set(SAMPSDK_MASTER_PROJECT ON)
endif()

add_library(sampsdk STATIC
    "amxplugin.cpp"
)
add_library(sampsdk::sampsdk ALIAS sampsdk)

target_include_directories(sampsdk PUBLIC
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

option(SAMPSDK_INSTALL "Generate the install targets" ${SAMPSDK_MASTER_PROJECT})
if(SAMPSDK_INSTALL)
    include(CMakePackageConfigHelpers)

    install(
        TARGETS sampsdk
        EXPORT ${PROJECT_NAME}Targets
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    )
    install(
        FILES
            "plugin.h"
            "plugincommon.h"
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    )
    install(DIRECTORY "amx" DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

    set(config_file ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake)
    set(version_file ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake)
    set(config_install_destination lib/cmake/${PROJECT_NAME})

    configure_package_config_file(
        ${CMAKE_CURRENT_SOURCE_DIR}/cmake/Config.cmake.in
        ${config_file}
        INSTALL_DESTINATION ${config_install_destination}
    )

    write_basic_package_version_file(
        ${version_file}
        COMPATIBILITY SameMajorVersion
    )

    install(
        FILES
            ${config_file}
            ${version_file}
        DESTINATION ${config_install_destination}
    )
    install(
        EXPORT ${PROJECT_NAME}Targets
        NAMESPACE ${PROJECT_NAME}::
        DESTINATION ${config_install_destination}
    )
endif()
