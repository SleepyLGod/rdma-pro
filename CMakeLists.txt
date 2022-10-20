cmake_minimum_required(VERSION 3.3 FATAL_ERROR)
project(rdma-pro)
set(CMAKE_CXX_STANDARD 20)
add_compile_options(-Wall -Wextra -pedantic -Werror)
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake_modules")
find_package(Threads REQUIRED)
find_package(ibverbs REQUIRED)

if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE RelWithDebInfo)
endif ()

string(LENGTH "${CMAKE_SOURCE_DIR}/" SOURCE_PATH_LENGTH)
add_definitions("-DSOURCE_PATH_LENGTH=${SOURCE_PATH_LENGTH}")

include_directories(include)

option(RDMA-PRO_BUILD_EXAMPLES "Build examples" ON)
option(RDMA-PRO_BUILD_DOCS "Build docs" OFF)
option(RDMA-PRO_ASAN "Build with AddressSanitizer" OFF)

if (RDMA-PRO_ASAN)
    add_compile_options(-fno-omit-frame-pointer -fsanitize=address)
    add_link_options(-fno-omit-frame-pointer -fsanitize=address)
endif ()

if (RDMA-PRO_BUILD_DOCS)
    # check if Doxygen is installed
    find_package(Doxygen)
    if (DOXYGEN_FOUND)
        # set input and output files
        set(DOXYGEN_OUT ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)
        # request to configure the file
        message("Doxygen build started")
        # note the option ALL which allows to build the docs together with the application
        add_custom_target( doc_doxygen ALL
                COMMAND ${DOXYGEN_EXECUTABLE} ${DOXYGEN_OUT}
                WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
                COMMENT "Generating API documentation with Doxygen"
                VERBATIM )
    else (DOXYGEN_FOUND)
        message("Doxygen need to be installed to generate the doxygen documentation")
    endif (DOXYGEN_FOUND)
endif ()

set(RDMA-PRO_SOURCE_FILES
# TODO: 0 add src files
        )

set(RDMA-PRO_LINK_LIBRARIES ibverbs Threads::Threads)

# TODO: 1
# add_library(rdma-pro STATIC ${RDMA-PRO_SOURCE_FILES})

list(APPEND
        RDMA-PRO_COMPILE_OPTIONS
        PUBLIC
        -flto
        PUBLIC
        -fno-rtti
        )

list(APPEND
        RDMA-PRO_LINK_OPTIONS
        PUBLIC
        -flto
        PUBLIC
        -fno-rtti
        )

if (CMAKE_CXX_COMPILER_ID MATCHES Clang)
    list(APPEND RDMA-PRO_COMPILE_OPTIONS
            PUBLIC
            -stdlib=libc++
            -fcoroutines-ts
            )
elseif (CMAKE_CXX_COMPILER_ID MATCHES GNU)
    if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS "11")
        list(APPEND RDMA-PRO_COMPILE_OPTIONS
                PUBLIC
                -fcoroutines
                )
    endif ()
endif ()

# TODO: 2
#target_compile_options(rdma-pro ${RDMA-PRO_COMPILE_OPTIONS})
#target_link_options(rdma-pro ${RDMA-PRO_LINK_OPTIONS})
#target_link_libraries(rdma-pro ${RDMA-PRO_LINK_LIBRARIES})

find_program(iwyu_path NAMES include-what-you-use iwyu)
if (iwyu_path)
    message("-- Using include-what-you-use ${iwyu_path}")
    set_property(TARGET rdma-pro PROPERTY CXX_INCLUDE_WHAT_YOU_USE ${iwyu_path})
endif ()

# TODO: 4
#if (RDMA-PRO_BUILD_EXAMPLES)
#    include_directories(examples/include)
#    set(RDMA-PRO_EXAMPLES_LIB_SOURCE_FILES
#            examples/socket/event_loop.cc
#            examples/socket/channel.cc
#            examples/socket/tcp_connection.cc
#            examples/socket/tcp_listener.cc
#            examples/acceptor.cc
#            examples/connector.cc
#            examples/qp_transmission.cc
#            )
#    add_library(rdma-pro_examples STATIC ${RDMA-PRO_EXAMPLES_LIB_SOURCE_FILES})
#    target_compile_options(rdma-pro_examples ${RDMA-PRO_COMPILE_OPTIONS})
#    target_link_options(rdma-pro_examples ${RDMA-PRO_LINK_OPTIONS})
#    set(RDMA-PRO_EXAMPLES helloworld send_bw write_bw)
#    foreach (EXAMPLE IN LISTS RDMA-PRO_EXAMPLES)
#        add_executable(${EXAMPLE} examples/${EXAMPLE}.cc)
#        target_link_libraries(${EXAMPLE} rdma-pro rdma-pro_examples)
#        target_compile_options(${EXAMPLE} ${RDMA-PRO_COMPILE_OPTIONS})
#    endforeach ()
#endif ()

# TODO: 3
include(GNUInstallDirs)
#install(TARGETS rdma-pro EXPORT rdma-pro ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})
#install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/rdma-pro DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
