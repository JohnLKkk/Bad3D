
# Add source to this project's executable.
add_executable(Core
    BApplication.h
    main.cpp
)


set_property(TARGET Core PROPERTY VS_DEBUGGER_WORKING_DIRECTORY "$<TARGET_FILE_DIR:Core>")

target_include_directories(Core PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")
target_link_libraries(Core vma glm tinyobjloader stb_image)

target_link_libraries(Core Vulkan::Vulkan OpenGL::GL)

add_dependencies(Core Shaders)
