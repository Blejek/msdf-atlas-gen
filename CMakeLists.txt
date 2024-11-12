cmake_minimum_required(VERSION 3.15)

project(msdf-atlas-gen VERSION 1.0 LANGUAGES CXX)

if(MSVC)
    set(CMAKE_CXX_FLAGS_DEBUG "/Od /Zi" CACHE STRING "" FORCE)
    set(CMAKE_CXX_FLAGS_DIST "/O2 /DNDEBUG" CACHE STRING "" FORCE)
    set(CMAKE_CXX_FLAGS_RELEASE "/O2 /Ob1 /Zi" CACHE STRING "" FORCE)
    set(${PROJECT_CMAKE_LINKING_FLAGS_TYPE}_DEBUG "/DEBUG /OPT:REF /OPT:ICF" CACHE STRING "" FORCE)
    set(${PROJECT_CMAKE_LINKING_FLAGS_TYPE}_DIST "/DEBUG:NONE /OPT:REF /OPT:ICF" CACHE STRING "" FORCE)
    set(${PROJECT_CMAKE_LINKING_FLAGS_TYPE}_RELEASE "/DEBUG /OPT:REF /OPT:ICF" CACHE STRING "" FORCE)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
else()
    set(CMAKE_CXX_FLAGS_DEBUG "-O0")
    set(CMAKE_CXX_FLAGS_DIST "-O3 -DNDEBUG")
    set(CMAKE_CXX_FLAGS_RELEASE "-O3 -g")
endif()

add_subdirectory(msdfgen)

file(GLOB_RECURSE MSDF_ATLAS_HEADERS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "msdf-atlas-gen/*.h" "msdf-atlas-gen/*.hpp")
file(GLOB_RECURSE MSDF_ATLAS_SOURCES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "msdf-atlas-gen/*.cpp")

# msdf-atlas-gen library
add_library(msdf-atlas-gen ${MSDF_ATLAS_HEADERS} ${MSDF_ATLAS_SOURCES})
add_library(msdf-atlas-gen::msdf-atlas-gen ALIAS msdf-atlas-gen)
set_target_properties(msdf-atlas-gen PROPERTIES PUBLIC_HEADER "${MSDF_ATLAS_HEADERS}")
target_compile_definitions(msdf-atlas-gen PUBLIC
    MSDF_ATLAS_VERSION=${MSDF_ATLAS_VERSION}
    MSDF_ATLAS_VERSION_MAJOR=${MSDF_ATLAS_VERSION_MAJOR}
    MSDF_ATLAS_VERSION_MINOR=${MSDF_ATLAS_VERSION_MINOR}
    MSDF_ATLAS_VERSION_REVISION=${MSDF_ATLAS_VERSION_REVISION}
    MSDF_ATLAS_COPYRIGHT_YEAR=${MSDF_ATLAS_COPYRIGHT_YEAR}
)
target_include_directories(msdf-atlas-gen INTERFACE
    $<INSTALL_INTERFACE:include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

target_compile_definitions(msdf-atlas-gen PUBLIC MSDF_ATLAS_NO_ARTERY_FONT)
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT msdf-atlas-gen)

target_compile_features(msdf-atlas-gen PUBLIC cxx_std_11)
target_link_libraries(msdf-atlas-gen PUBLIC msdfgen::msdfgen)

target_compile_definitions(msdf-atlas-gen PUBLIC MSDF_ATLAS_PUBLIC=)
