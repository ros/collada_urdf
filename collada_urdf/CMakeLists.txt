cmake_minimum_required(VERSION 3.12.2)
project(collada_urdf)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  # add_compile_options(-Wall -Wextra -Wpedantic -Wnon-virtual-dtor -Woverloaded-virtual)
endif()

find_package(ament_cmake_ros REQUIRED)
find_package(angles REQUIRED)
find_package(assimp REQUIRED)
find_package(Boost REQUIRED COMPONENTS system filesystem program_options)
find_package(COLLADA_DOM 2.4 REQUIRED COMPONENTS 1.5)
find_package(collada_parser REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(geometric_shapes REQUIRED)
find_package(resource_retriever REQUIRED)
find_package(urdf REQUIRED)
find_package(urdfdom_headers REQUIRED)

# XXX Stack overflow copy/paste
# Get all propreties that cmake supports
execute_process(COMMAND cmake --help-property-list OUTPUT_VARIABLE CMAKE_PROPERTY_LIST)

# Convert command output into a CMake list
STRING(REGEX REPLACE ";" "\\\\;" CMAKE_PROPERTY_LIST "${CMAKE_PROPERTY_LIST}")
STRING(REGEX REPLACE "\n" ";" CMAKE_PROPERTY_LIST "${CMAKE_PROPERTY_LIST}")

function(print_properties)
    message ("CMAKE_PROPERTY_LIST = ${CMAKE_PROPERTY_LIST}")
endfunction(print_properties)

function(print_target_properties tgt)
    if(NOT TARGET ${tgt})
      message("There is no target named '${tgt}'")
      return()
    endif()

    foreach (prop ${CMAKE_PROPERTY_LIST})
        string(REPLACE "<CONFIG>" "${CMAKE_BUILD_TYPE}" prop ${prop})
    # Fix https://stackoverflow.com/questions/32197663/how-can-i-remove-the-the-location-property-may-not-be-read-from-target-error-i
    #if(prop STREQUAL "LOCATION" OR prop MATCHES "^LOCATION_" OR prop MATCHES "_LOCATION$")
    #    continue()
    #endif()
        # message ("Checking ${prop}")
        get_property(propval TARGET ${tgt} PROPERTY ${prop} SET)
        if (propval)
            get_target_property(propval ${tgt} ${prop})
            message ("${tgt} ${prop} = ${propval}")
        endif()
    endforeach(prop)
endfunction(print_target_properties)
# XXX

# print_target_properties(assimp::assimp)


# Assimp target has wrong INTERFACE_INCLUDE_DIRS on Ubuntu Focal
# https://bugs.launchpad.net/ubuntu/+source/assimp/+bug/1882427
# if(UNIX)
#   set(keep_include_dirs)
#   set(need_to_find_assimp FALSE)
#   get_target_property(assimp_include_dirs assimp::assimp INTERFACE_INCLUDE_DIRECTORIES)
#   foreach(_include_dir ${assimp_include_dirs})
#     if(EXISTS "${_include_dir}")
#       list(APPEND keep_include_dirs "${_include_dir}")
#     else()
#       MESSAGE(STATUS "Assimp include dir ${_include_dir} does not exist; will try workaround")
#       set(need_to_find_assimp TRUE)
#     endif()
#   endforeach()
#   if(need_to_find_assimp)
#     find_path(actual_assimp_include_dir
#       NAMES "ai_assert.h"
#       HINTS "/usr/include/assimp"
#     )
#     if(actual_assimp_include_dir)
#       message(STATUS "Workaround assimp bug; found assimp header in ${actual_assimp_include_dir}")
#       # Include dir is one above
#       get_filename_component(actual_assimp_include_dir "${actual_assimp_include_dir}" DIRECTORY)
#       list(APPEND keep_include_dirs "${actual_assimp_include_dir}")
#     else()
#       message(WARNING "Needed to find assimp include directories, but did not find them")
#     endif()
#   endif()
#   message(STATUS "All assimp include dirs: ${keep_include_dirs}")
#   set_target_properties(assimp::assimp
#     PROPERTIES
#     INTERFACE_INCLUDE_DIRECTORIES ${keep_include_dirs}
#   )
# endif()


# print_target_properties(assimp::assimp)


# TODO(sloretz) none of this global stuff
# include_directories(include)
# include_directories(${Boost_INCLUDE_DIR})
# include_directories(${COLLADA_DOM_INCLUDE_DIRS})
# link_directories(${COLLADA_DOM_LIBRARY_DIRS})

#catkin_package(
#  LIBRARIES ${PROJECT_NAME}
#  INCLUDE_DIRS include
#  CATKIN_DEPENDS collada_parser geometric_shapes resource_retriever rosconsole urdf
#  DEPENDS ASSIMP COLLADA_DOM urdfdom_headers #)

# TODO(sloretz) get rid of this global stuff
# include_directories(${catkin_INCLUDE_DIRS} ${EIGEN3_INCLUDE_DIRS})
# link_directories(${catkin_LIBRARY_DIRS})

# TODO(sloretz) spell out library name - project_name is a pet peeve of mine
add_library(collada_urdf
  src/collada_urdf.cpp
)
target_include_directories(collada_urdf PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include>"
  ${Boost_INCLUDE_DIRS}
  ${COLLADA_DOM_INCLUDE_DIRS}
)
target_link_libraries(collada_urdf PRIVATE
  ${Boost_LIBRARIES}
  ${COLLADA_DOM_LIBRARIES}
)
ament_target_dependencies(collada_urdf PUBLIC
  "angles"
  "geometric_shapes"
  "resource_retriever"
  "urdf"
)

# TODO(sloretz) are assimp compiler flags really necessary?
# set_target_properties(collada_urdf PROPERTIES COMPILER_FLAGS "${ASSIMP_CXX_FLAGS} ${ASSIMP_CFLAGS_OTHER}")
# set_target_properties(collada_urdf PROPERTIES LINK_FLAGS "${ASSIMP_LINK_FLAGS}")

add_executable(urdf_to_collada src/urdf_to_collada.cpp)
target_link_libraries(urdf_to_collada
  collada_urdf
)

add_executable(collada_to_urdf src/collada_to_urdf.cpp)
target_link_libraries(collada_to_urdf
  # Assimp target is broken on Ubuntu Focal
  # https://bugs.launchpad.net/ubuntu/+source/assimp/+bug/1882427
  # assimp::assimp
  ${ASSIMP_LIBRARIES}
  collada_urdf
  Eigen3::Eigen
)
message(STATUS "Using assimp include dirs: ${ASSIMP_INCLUDE_DIRS}")
target_include_directories(collada_to_urdf PRIVATE
  # Can't use assimp::assimp :(
  ${ASSIMP_INCLUDE_DIRS}
)
ament_target_dependencies(collada_to_urdf
  "collada_parser"
)

# print_target_properties(collada_to_urdf)
# print_target_properties(assimp::assimp)

# TODO(sloretz) are assimp compiler flags really necessary?
# set_target_properties(collada_to_urdf PROPERTIES COMPILER_FLAGS "${ASSIMP_CXX_FLAGS} ${ASSIMP_CFLAGS_OTHER}")
# set_target_properties(collada_to_urdf PROPERTIES LINK_FLAGS "${ASSIMP_LINK_FLAGS}")

if(BUILD_TESTING)
# TODO(sloretz) port tests
#   catkin_add_gtest(test_collada_urdf test/test_collada_urdf.cpp WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/test)
#   target_link_libraries(test_collada_urdf ${PROJECT_NAME} ${catkin_LIBRARIES} ${COLLADA_DOM_LIBRARIES}
#     ${Boost_LIBRARIES})
endif()

ament_export_dependencies(urdf)

install(
  TARGETS collada_urdf urdf_to_collada collada_to_urdf
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(DIRECTORY include/
  DESTINATION include
)

ament_package()
