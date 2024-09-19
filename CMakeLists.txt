
cmake_minimum_required( VERSION 3.5 )
project( libxls LANGUAGES CXX )

set( INCLUDE_DIRS include ${CMAKE_CURRENT_SOURCE_DIR} )

set(SRC_DIRS ${CMAKE_CURRENT_SOURCE_DIR}/src) # Set include directory variable
file(GLOB_RECURSE SOURCE_FILES
    "${SRC_DIRS}/*.cpp"
    "${SRC_DIRS}/*.c"
)

if(NOT EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/config.h")
    message(STATUS "config.h not found, running bootstrap and configure...")

    # 使用 execute_process 來執行 ./bootstrap
    execute_process(
        COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/bootstrap
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
        RESULT_VARIABLE BOOTSTRAP_RESULT
        ERROR_VARIABLE BOOTSTRAP_ERROR
        OUTPUT_VARIABLE BOOTSTRAP_OUTPUT
    )

    # 檢查 bootstrap 執行結果
    if(NOT BOOTSTRAP_RESULT EQUAL 0)
        message(FATAL_ERROR "Bootstrap failed: ${BOOTSTRAP_ERROR}")
    endif()

    # 使用 execute_process 來執行 ./configure
    execute_process(
        COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/configure
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
        RESULT_VARIABLE CONFIGURE_RESULT
        ERROR_VARIABLE CONFIGURE_ERROR
        OUTPUT_VARIABLE CONFIGURE_OUTPUT
    )

    # 檢查 configure 執行結果
    if(NOT CONFIGURE_RESULT EQUAL 0)
        message(FATAL_ERROR "Configure failed: ${CONFIGURE_ERROR}")
    endif()
else()
    message(STATUS "config.h found, skipping bootstrap and configure.")
endif()

add_library(${PROJECT_NAME} STATIC ${SOURCE_FILES})
target_link_libraries(${PROJECT_NAME} PRIVATE iconv)
target_include_directories(${PROJECT_NAME} PUBLIC ${INCLUDE_DIRS}
)
