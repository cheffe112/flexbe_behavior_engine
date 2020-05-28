cmake_minimum_required(VERSION 2.8.3)
project(flexbe_widget)

find_package(catkin REQUIRED)
find_package(PythonLibs 2.7 REQUIRED)

## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
catkin_python_setup()

# specify catkin-specific information
# INCLUDE_DIRS - The exported include paths (i.e. cflags) for the package
# LIBRARIES - The exported libraries from the project
# CATKIN_DEPENDS - Other catkin projects that this project depends on
# DEPENDS - Non-catkin CMake projects that this project depends on
# CFG_EXTRAS - Additional configuration options 
catkin_package(
    INCLUDE_DIRS src
    LIBRARIES ${PROJECT_NAME})

# use add_library() or add_executable() as required
#add_library(${PROJECT_NAME} ${${PROJECT_NAME}_SRCS})

set(
    BIN_FILES
    create_repo
)

set(
    PYTHON_BIN_FILES
    be_action_server
    be_compress
    be_launcher
    breakpoint
    evaluate_logs
)

foreach(node ${PYTHON_BIN_FILES} )
    if (COMPILE_PYTHON)
        include_directories(${PYTHON_INCLUDE_DIRS})
        execute_process(COMMAND cython --embed -o ${CATKIN_DEVEL_PREFIX}/${node}.c ${CMAKE_CURRENT_SOURCE_DIR}/bin/${node})
        add_executable(${node} ${CATKIN_DEVEL_PREFIX}/${node}.c)
        target_link_libraries(${node} ${catkin_LIBRARIES} ${PYTHON_LIBRARIES})
        install(TARGETS ${node} RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
    else ()
        install(PROGRAMS bin/${node} DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
    endif ()
endforeach()

foreach(node ${BIN_FILES} )
    install(PROGRAMS bin/${node} DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
endforeach()
