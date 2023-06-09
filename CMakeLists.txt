# Project
cmake_minimum_required(VERSION 3.8)
project("Skinify")

set(CMAKE_CXX_STANDARD 20)

set(LIB_DIR "${CMAKE_CURRENT_SOURCE_DIR}/lib")
set(SRC_DIR "${CMAKE_CURRENT_SOURCE_DIR}/src")

# Icon
set(APP_ICON_RESOURCE_WINDOWS "${CMAKE_CURRENT_SOURCE_DIR}/resources/icons/appicon.rc")

# Sources
set(SOURCES 
    "${SRC_DIR}/Main.cpp"
    "${SRC_DIR}/core/Skinify.cpp"
    "${SRC_DIR}/core/Image.cpp"
    "${SRC_DIR}/interface/Ui.cpp"
    "${SRC_DIR}/interface/ImGui.cpp"
    "${SRC_DIR}/interface/ImGui-Custom.cpp"
    ${APP_ICON_RESOURCE_WINDOWS}
)

set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /ENTRY:mainCRTStartup")
add_executable(${PROJECT_NAME} WIN32  ${SOURCES})
set_target_properties(${PROJECT_NAME} PROPERTIES LINK_FLAGS_RELEASE "-mwindows /ENTRY:mainCRTStartup /INCREMENTAL:NO")

set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_STANDARD 20)

# Resources
configure_file(resources/fonts/Roboto-Medium.ttf resources/fonts/Roboto-Medium.ttf COPYONLY)
configure_file(resources/images/banner.png resources/images/banner.png COPYONLY)
configure_file(resources/images/icon.png resources/images/icon.png COPYONLY)

# OpenGL
find_package(OpenGL REQUIRED)
if(NOT OPENGL_FOUND)
     message(STATUS "OpenGL was not found!")
endif(NOT OPENGL_FOUND)

# STB_image
target_include_directories(${PROJECT_NAME} PUBLIC lib/stb_image)
target_link_directories(${PROJECT_NAME} PUBLIC lib/stb_image)

# GLFW
add_subdirectory(lib/glfw)
target_include_directories(${PROJECT_NAME} PUBLIC lib/glfw)
link_libraries(glfw)

# GLEW
add_subdirectory(lib/glew)
target_include_directories(${PROJECT_NAME} PUBLIC lib/glew/include)

# ImGui
set(IMGUI_DIR "${LIB_DIR}/imgui")

add_library(IMGUI)

target_sources(IMGUI
    PUBLIC
        ${IMGUI_DIR}/imgui_demo.cpp
        ${IMGUI_DIR}/imgui_draw.cpp
        ${IMGUI_DIR}/imgui_tables.cpp
        ${IMGUI_DIR}/imgui_widgets.cpp
        ${IMGUI_DIR}/imgui.cpp

    PUBLIC
        ${IMGUI_DIR}/backends/imgui_impl_glfw.cpp
        ${IMGUI_DIR}/backends/imgui_impl_opengl3.cpp
)

target_include_directories(IMGUI
    PUBLIC ${IMGUI_DIR}
    PUBLIC ${IMGUI_DIR}/backends
)

target_link_libraries(${PROJECT_NAME}
    libglew_static
    OpenGL::GL
    IMGUI
	glfw
)
