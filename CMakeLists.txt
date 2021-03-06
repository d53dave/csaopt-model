cmake_minimum_required(VERSION 3.3)
project(model)

include(CTest)

set(PROJECT_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/include)
set(CMAKE_CXX_STANDARD 11) # adds -std=c++11
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/../CMakeModules) # main (top) cmake dir
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/CMakeModules) # project specific cmake dir

set (PROJECT_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

set(SOURCE_FILES src/ModelSettings.h src/State.h src/State.cpp src/Optimization.h src/Options.h)

set(CMAKE_BUILD_TYPE Debug)


include_directories(
        ${PROJECT_SOURCE_DIR}/include
        ${PROJECT_BINARY_DIR}/include   # add the binary tree to the search path so that we will find Version.h
)

add_library(model ${SOURCE_FILES})

set(${PROJECT_NAME}_INCL_DIRS ${PROJECT_SOURCE_DIR}/include CACHE INTERNAL "${PROJECT_NAME}: Include Directories" FORCE)

set_target_properties(model PROPERTIES LINKER_LANGUAGE CXX) #this is optional, in case no .cpp file is in project

enable_testing()
file(GLOB TEST_FILES "test/*.cpp")

#Run through each source
foreach(testFile ${TEST_FILES})
    #Extract the filename without an extension (NAME_WE)
    get_filename_component(testName ${testFile} NAME_WE)

    #Add compile target
    add_executable(${testName} ${testFile})
    add_test(NAME ${testName} COMMAND ${testName} )
endforeach(testFile)

# Add a target to generate API documentation with Doxygen
find_package(Doxygen)
if(DOXYGEN_FOUND)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile @ONLY)
    add_custom_target(doc
            ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
            COMMENT "Generating API documentation with Doxygen" VERBATIM
    )
endif(DOXYGEN_FOUND)
