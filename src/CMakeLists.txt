# Source files
set(SHADER_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/Shaders")
set(SHADER_BINARY_DIR "${CMAKE_CURRENT_BINARY_DIR}/Shaders")

file(GLOB_RECURSE GLSL_SOURCE_FILES
    "${SHADER_SOURCE_DIR}/*.frag"
    "${SHADER_SOURCE_DIR}/*.vert"
)

foreach(GLSL ${GLSL_SOURCE_FILES})
    get_filename_component(FILE_NAME ${GLSL} NAME)
    set(SPIRV "${SHADER_BINARY_DIR}/${FILE_NAME}.spv")
    add_custom_command(
        OUTPUT ${SPIRV}
        COMMAND ${Vulkan_GLSLC_EXECUTABLE} ${GLSL} -o ${SPIRV}
        DEPENDS ${GLSL}
    )
    list(APPEND SPIRV_BINARY_FILES ${SPIRV})
endforeach(GLSL)

add_custom_target(
    Shaders
    DEPENDS ${SPIRV_BINARY_FILES}
)

set(SOURCES
    "main.cpp"
)

# Create the executable
add_executable(${PROJECT_NAME} ${SOURCES} ${GLSL_SOURCE_FILES} )
add_dependencies(${PROJECT_NAME} Shaders)

set_property(TARGET ${PROJECT_NAME} PROPERTY COMPILE_WARNING_AS_ERROR ON)

# Link libraries
target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(${PROJECT_NAME} PRIVATE ${Vulkan_LIBRARIES} glfw)
