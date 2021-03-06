cmake_minimum_required(VERSION 2.8)

project(fusion_xml)
include(gtest.cmake)

# set(COVERAGE OFF CACHE BOOL "Coverage")

file(GLOB sources 
    "FusionXML.cpp" 
    )
file(GLOB headers 
    "FusionXML.h"
    "Key.h"
    )
add_executable(fusion_xml 
    main.cpp 
    ${headers}
    ${sources}
    ) 

file(GLOB sourcesTests 
    "unit_fixture.cpp"
    )
file(GLOB headersTests 
    "unit_fixture.h"
    )
add_executable(tests 
    "unit_tests.cpp"
    ${headers}
    ${sources} 
    ${headersTests} 
    ${sourcesTests} 
    )

target_include_directories(
    fusion_xml
    PRIVATE
    "./rapidxml"
    )   

target_include_directories(
    tests
    PRIVATE
    "./rapidxml"
    )   

find_package (Threads)
target_link_libraries(tests gtest ${CMAKE_THREAD_LIBS_INIT})    

if (MSVC)
    target_compile_options(tests PRIVATE /std:c++14)

    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /Ox")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /Od")

    # VS 2017 removed tr1
    add_definitions(-DGTEST_HAS_TR1_TUPLE=0)
    
    if ((CMAKE_VERSION GREATER 3.7.9) OR (CMAKE_VERSION EQUAL 3.7.9))
    if (MSVC_IDE)
        option(VS_ADD_NATIVE_VISUALIZERS "Configure project to use Visual Studio native visualizers" TRUE)
    else()
        set(VS_ADD_NATIVE_VISUALIZERS FALSE CACHE INTERNAL "Native visualizers are Visual Studio extension" FORCE)
    endif()

    # add natvis file to the library so it will automatically be loaded into Visual Studio
    if(VS_ADD_NATIVE_VISUALIZERS)
        target_sources(fusion_xml
            ${CMAKE_CURRENT_SOURCE_DIR}/rapidxml.natvis
        )
    endif()
endif()
else()
    target_compile_options(fusion_xml PRIVATE -std=c++11 -g -fpermissive)
    target_compile_options(tests PRIVATE -std=c++11 -g -fpermissive)
    # if (COVERAGE)
    #     target_compile_options(tests PRIVATE --coverage)
    #     target_link_libraries(tests PRIVATE --coverage)
    # endif()
endif()




