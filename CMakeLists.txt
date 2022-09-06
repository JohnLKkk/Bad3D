# 暂时设置3.8版本CMake
cmake_minimum_required (VERSION 3.8)
# 一个叼的项目需要一个叼的名字
project ("Bad3D")

set(CMAKE_CXX_STANDARD 17)

# Vulkan
find_package(Vulkan REQUIRED)
# OpenGL
find_package(OpenGL REQUIRED)
# 跨平台窗口
#find_package(glfw3 REQUIRED)

# 添加一个子模块，第三方库
add_subdirectory(third_party)

set (CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_SOURCE_DIR}/bin")

add_subdirectory(src)


find_program(SL_VALIDATOR SLangValidator HINTS /usr/bin /usr/local/bin $ENV{VULKAN_SDK}/Bin/ $ENV{VULKAN_SDK}/Bin32/)

#[[
查找所有可用的shader,目前只支持SL/hlsl
]]
file(GLOB_RECURSE SHADER_SOURCE_FILES
    "${PROJECT_SOURCE_DIR}/shaders/*.frag"
    "${PROJECT_SOURCE_DIR}/shaders/*.vert"
    "${PROJECT_SOURCE_DIR}/shaders/*.hlsl"
    "${PROJECT_SOURCE_DIR}/shaders/*.vert"
    "${PROJECT_SOURCE_DIR}/shaders/*.comp"
    )

## iterate each shader
foreach(SL ${SHADER_SOURCE_FILES})
  message(STATUS "BUILDING SHADER")
  get_filename_component(FILE_NAME ${SL} NAME)
  set(SPIRV "${PROJECT_SOURCE_DIR}/shaders/${FILE_NAME}.spv")
  message(STATUS ${SL})
# 这里可能得加个hlsl生成器
  add_custom_command(
    OUTPUT ${SPIRV}
    COMMAND ${SL_VALIDATOR} -V ${SL} -o ${SPIRV}
    DEPENDS ${SL})
  list(APPEND SPIRV_BINARY_FILES ${SPIRV})
endforeach(SL)

add_custom_target(
    Shaders 
    DEPENDS ${SPIRV_BINARY_FILES}
    )
