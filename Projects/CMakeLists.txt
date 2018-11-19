cmake_minimum_required(VERSION 3.10)

set(PROJECTS_NAMES APP_Test)

link_directories("${PROJECT_SOURCE_DIR}/Physika_Src")                                       # 设置PhysLeo库路径
link_libraries(Physika_Core Physika_Dynamics Physika_Framework Physika_Geometry Physika_GUI Physika_IO Physika_Render)   

foreach(PROJECT_NAME IN ITEMS ${PROJECTS_NAMES})
    set(SRC_DIR "${PROJECT_SOURCE_DIR}/Projects/${PROJECT_NAME}")

    file(                                                                           #利用glob命令读取所有源文件list
        GLOB_RECURSE SRC_LIST 
        LIST_DIRECTORIES false
        CONFIGURE_DEPENDS
        "${SRC_DIR}/*.c*"
        "${SRC_DIR}/*.h*"
    )

    list(FILTER SRC_LIST EXCLUDE REGEX .*Media/.*)                                  #排除deprecated 文件下面的所有文件

    add_executable(${PROJECT_NAME} ${SRC_LIST})                                          #添加编译目标 静态链接库

    message("${CMAKE_CURRENT_BINARY_DIR}")

    file(COPY "${PROJECT_SOURCE_DIR}/Physika_Src/Physika_Dependency/OpenGL/lib/Windows/X64/freeglut.dll" "${PROJECT_SOURCE_DIR}/Physika_Src/Physika_Dependency/OpenGL/lib/Windows/X64/glew32.dll" "${PROJECT_SOURCE_DIR}/Physika_Src/Physika_Dependency/OpenGL/lib/Windows/X64/glu32.dll" "${PROJECT_SOURCE_DIR}/Physika_Src/Physika_Dependency/OpenGL/lib/Windows/X64/opengl32.dll" DESTINATION ${CMAKE_CURRENT_BINARY_DIR})


    foreach(SRC IN ITEMS ${SRC_LIST})                                                       #为VS工程添加filter 方便查看文件结构目录
        get_filename_component(SRC_PATH "${SRC}" PATH)
        file(RELATIVE_PATH SRC_PATH_REL "${SRC_DIR}" "${SRC_PATH}")
        string(REPLACE "/" "\\" GROUP_PATH "${SRC_PATH_REL}")
        source_group("${GROUP_PATH}" FILES "${SRC}")
    endforeach()

endforeach()