cmake_minimum_required(VERSION 2.8.2)

project(Example)

include(CTest)

if (CMAKE_VERSION VERSION_LESS 3.2)
    set(UPDATE_DISCONNECTED_IF_AVAILABLE "")
else()
    set(UPDATE_DISCONNECTED_IF_AVAILABLE "UPDATE_DISCONNECTED 1")
endif()

include(DownloadProject.cmake)
download_project(PROJ                googletest
                 GIT_REPOSITORY      https://github.com/google/googletest.git
                 GIT_TAG             master
                 ${UPDATE_DISCONNECTED_IF_AVAILABLE}
)

# Prevent GoogleTest from overriding our compiler/linker options
# when building with Visual Studio
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

add_subdirectory(${googletest_SOURCE_DIR} ${googletest_BINARY_DIR})

# When using CMake 2.8.11 or later, header path dependencies
# are automatically added to the gtest and gmock targets.
# For earlier CMake versions, we have to explicitly add the
# required directories to the header search path ourselves.

##if (CMAKE_VERSION VERSION_LESS 2.8.11)    ## ==> remove this comment, once the following pull-request is accepted:
                                            ##      https://github.com/google/googletest/pull/701
  include_directories(
    "${gtest_SOURCE_DIR}/include"  ## ${googletest_SOURCE_DIR}/googletest/include
    "${gmock_SOURCE_DIR}/include"  ## ${googletest_SOURCE_DIR}/googlemock/include
    )
##endif()

# Trivial example using gtest and gmock
add_executable(example example.cpp)
target_link_libraries(example gmock_main) # target_link_libraries(example gtest gmock_main)
add_test(NAME example_test COMMAND example)
