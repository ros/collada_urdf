cmake_minimum_required(VERSION 3.12.2)
project(collada_parser)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  # TODO(sloretz) tackle compiler warnings 0_o
  # add_compile_options(-Wall -Wextra -Wpedantic -Wnon-virtual-dtor -Woverloaded-virtual)
endif()

find_package(ament_cmake_ros REQUIRED)
find_package(Boost REQUIRED system)
find_package(class_loader REQUIRED)
find_package(COLLADA_DOM 2.4 REQUIRED COMPONENTS 1.5)
find_package(PkgConfig)
find_package(pluginlib REQUIRED)
find_package(urdf REQUIRED)
find_package(urdf_parser_plugin REQUIRED)
find_package(urdfdom_headers REQUIRED)


# TODO target include directories
# include_directories(include ${Boost_INCLUDE_DIR} ${catkin_INCLUDE_DIRS} ${urdfdom_headers_INCLUDE_DIRS})

# if(COLLADA_DOM_FOUND)
#   include_directories(${COLLADA_DOM_INCLUDE_DIRS})
#   link_directories(${COLLADA_DOM_LIBRARY_DIRS})
# endif()

# build the parser lib
add_library(collada_parser SHARED
  src/collada_parser.cpp
)
target_include_directories(collada_parser PUBLIC
  ${Boost_INCLUDE_DIR}
  ${COLLADA_DOM_INCLUDE_DIRS}
  # ${urdf_INCLUDE_DIRS}
  # ${urdfdom_headers_INCLUDE_DIRS}
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)
target_link_libraries(collada_parser
  ${COLLADA_DOM_LIBRARIES}
  ${Boost_LIBRARIES}
  ${urdf_TARGETS}
  ${urdfdom_headers_TARGETS}
)

# necessary for collada reader to create the temporary dae files due
# to limitations in the URDF geometry
include (CheckFunctionExists)
check_function_exists(mkstemps HAVE_MKSTEMPS)
if(HAVE_MKSTEMPS)
  target_compile_definitions(collada_parser PRIVATE "HAVE_MKSTEMPS")
endif()

# build the plugin for the parser
add_library(collada_parser_plugin MODULE src/collada_parser_plugin.cpp)
# target_include_directories(collada_parser_plugin PRIVATE
#   ${class_loader_INCLUDE_DIRS}
#   ${urdf_parser_plugin_INCLUDE_DIRS}
# )
target_link_libraries(collada_parser_plugin PUBLIC
  collada_parser
  ${class_loader_TARGETS}
  ${urdf_parser_plugin_TARGETS}
)

# TODO Where did these compiler flags come from??
# set_target_properties(collada_parser
#   PROPERTIES COMPILER_FLAGS "${COLLADA_DOM_CFLAGS_OTHER}"
# )
# if(APPLE)
#   set_target_properties(${PROJECT_NAME}
#     PROPERTIES LINK_FLAGS
#     "${COLLADA_DOM_LDFLAGS_OTHER} -undefined dynamic_lookup"
#   )
# else()
#   set_target_properties(${PROJECT_NAME}
#     PROPERTIES LINK_FLAGS "${COLLADA_DOM_LDFLAGS_OTHER}"
#   )
# endif()

ament_export_dependencies(COLLADA_DOM)
ament_export_dependencies(pluginlib)
ament_export_dependencies(urdf_parser_plugin)

install(TARGETS collada_parser collada_parser_plugin
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(
  DIRECTORY include/
  DESTINATION include
)

install(TARGETS collada_parser EXPORT collada_parser-export)
ament_export_targets(collada_parser-export)

pluginlib_export_plugin_description_file(urdf_parser_plugin "collada_parser_plugin_description.xml")


ament_package()
