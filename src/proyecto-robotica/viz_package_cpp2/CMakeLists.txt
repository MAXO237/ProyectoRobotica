cmake_minimum_required(VERSION 3.8)
project(viz_package_cpp2)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# 1. ENCONTRAR PAQUETES
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(visualization_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED) # Agregado porque lo usas en las dependencias
find_package(geometry_msgs REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)

# 2. DEFINICIÓN DE EJECUTABLES Y DEPENDENCIAS

# --- viz_node ---
add_executable(viz_node src/viz_node.cpp)
target_include_directories(viz_node PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include/${PROJECT_NAME}>)
target_compile_features(viz_node PUBLIC c_std_99 cxx_std_17)
ament_target_dependencies(viz_node 
  rclcpp 
  visualization_msgs 
  std_msgs
)

# --- move_node (EL QUE TE DABA ERROR) ---
add_executable(move_node src/move_node.cpp)
target_include_directories(move_node PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include/${PROJECT_NAME}>)
target_compile_features(move_node PUBLIC c_std_99 cxx_std_17)
# Aquí unifiqué todas tus dependencias en una sola llamada
ament_target_dependencies(move_node
  rclcpp
  sensor_msgs
  geometry_msgs
  std_msgs
  tf2
  tf2_ros            # Necesario para transform_broadcaster.hpp
  tf2_geometry_msgs
)

# --- circle_control_node ---
add_executable(circle_control_node src/circle_control_node.cpp)
target_include_directories(circle_control_node PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include/${PROJECT_NAME}>)
target_compile_features(circle_control_node PUBLIC c_std_99 cxx_std_17)
ament_target_dependencies(circle_control_node 
  rclcpp 
  sensor_msgs 
  geometry_msgs 
  std_msgs 
  tf2_ros 
  tf2_geometry_msgs
)

# --- ps2_joy_node ---
add_executable(ps2_joy_node src/ps2_joy_node.cpp)
ament_target_dependencies(ps2_joy_node 
  rclcpp 
  sensor_msgs
)

# --- ps2_control_node ---
add_executable(ps2_control_node src/ps2_control_node.cpp)
target_include_directories(ps2_control_node PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include/${PROJECT_NAME}>)
target_compile_features(ps2_control_node PUBLIC c_std_99 cxx_std_17)
ament_target_dependencies(ps2_control_node 
  rclcpp 
  sensor_msgs 
  tf2_ros 
  tf2_geometry_msgs
)

# --- wheel_publisher ---
add_executable(wheel_publisher src/wheel_velocity_publisher.cpp)
target_include_directories(wheel_publisher PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include/${PROJECT_NAME}>)
ament_target_dependencies(wheel_publisher 
  rclcpp 
  geometry_msgs 
  std_msgs
)

# --- wheel_subscriber ---
add_executable(wheel_subscriber src/wheel_velocity_subscriber.cpp) 
target_include_directories(wheel_subscriber PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include/${PROJECT_NAME}>)
ament_target_dependencies(wheel_subscriber 
  rclcpp 
  std_msgs
)

# 3. INSTALACIÓN
install(TARGETS
  viz_node
  move_node
  circle_control_node
  ps2_joy_node
  ps2_control_node
  wheel_publisher
  wheel_subscriber
  DESTINATION lib/${PROJECT_NAME}
)

install(DIRECTORY
  launch
  urdf
  rviz
  DESTINATION share/${PROJECT_NAME}
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  set(ament_cmake_copyright_FOUND TRUE)
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()