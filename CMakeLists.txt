cmake_minimum_required(VERSION 3.15)
project(bunny)

set(CMAKE_CXX_STANDARD 20)

add_executable(bunny main.cpp)

include_directories (src/include)
include_directories (test/include)

file (GLOB_RECURSE SRC_SOURCES "src/*.cpp" "src/*.hpp")
add_library (src_static STATIC ${SRC_SOURCES})
target_link_libraries(bunny src_static)

file (GLOB_RECURSE TEST_SOURCES "test/*.cpp" "test/*.hpp")
add_library (test_static STATIC ${TEST_SOURCES})
target_link_libraries(bunny test_static)

add_subdirectory(vendor/glfw)
include_directories(vendor/glfw/include)
target_link_libraries(bunny glfw ${GLFW_LIBRARIES})
# target_link_libraries(bunny glfw ${OPENGL_glu_LIBRARY} ${GLFW_LIBRARIES})

add_subdirectory(vendor/glad)
include_directories (vendor/glad/include)
target_link_libraries(bunny glad)

include_directories (vendor/glm)

add_subdirectory(vendor/stb_image)
include_directories(vendor/stb_image)
target_link_libraries(bunny stb_image)

add_subdirectory(vendor/imgui)
include_directories(vendor/imgui)
target_link_libraries(bunny imgui)

add_subdirectory(vendor/fmt)
include_directories (vendor/fmt/include)
target_link_libraries(bunny fmt)

find_program(
        CLANG_FORMAT_EXE
        NAMES "clang-format"
        DOC "Path to clang-format executable"
)
if(NOT CLANG_FORMAT_EXE)
    message(STATUS "clang-format not found.")
else()
    message(STATUS "clang-format found: ${CLANG_FORMAT_EXE}")
    file(GLOB_RECURSE FILES_TO_FORMAT "test/*.cpp" "test/*.hpp" "src/*.cpp" "src/*.hpp" main.cpp)
    add_custom_target(
            clang-format
            COMMAND ${CLANG_FORMAT_EXE} -i -style=file ${FILES_TO_FORMAT}
    )
    add_dependencies(bunny clang-format)
endif()
