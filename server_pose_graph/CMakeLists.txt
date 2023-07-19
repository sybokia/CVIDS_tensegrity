# 3.8 required for working with CUDA as a language
cmake_minimum_required(VERSION 3.8)
project(server_pose_graph LANGUAGES CXX CUDA)

# C++14 required for one of deb dependencies on Ubuntu 20.04
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CUDA_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)
set(CMAKE_CUDA_STANDARD_REQUIRED TRUE) 
set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -DNDEBUG")

find_package(catkin REQUIRED COMPONENTS
    roscpp
    std_msgs
    geometry_msgs
    nav_msgs
    tf
    cv_bridge
    camera_model
    agent_msg
    open_chisel
    roslib
)

catkin_package(
  CATKIN_DEPENDS
    roscpp
    camera_model
    roslib
)

find_package(OpenCV REQUIRED)
find_package(Ceres REQUIRED)
find_package(Sophus REQUIRED)
find_package(Eigen3)
find_package(Pangolin)
find_package(octomap REQUIRED)


INCLUDE_DIRECTORIES( 
    ${catkin_INCLUDE_DIRS}
    ${Ceres_INCLUDE_DIRS}
    ${Sophus_INCLUDE_DIR}
    ${EIGEN3_INCLUDE_DIR}
    ${Pangolin_INCLUDE_DIRS}
    ${OCTOMAP_INCLUDE_DIRS} 
)

add_executable(server_pose_graph
    include/fmc/findClique.cpp
    include/fmc/findCliqueHeu.cpp
    include/fmc/graphIO.cpp
    include/fmc/utils.cpp
    include/fmc/pcm.cpp

    src/pcm_graph.cpp
    src/dense_mapping/dense_mapping_parameters.cpp
    src/dense_mapping/sgm_stereo_mapper.cpp
    src/dense_mapping/depth_filter.cpp
    src/dense_mapping/calc_cost.cu
    src/dense_mapping/depth_estimator.cpp
    src/collaborative_server_node.cpp
    src/collaborative_server_system.cpp
    src/parameters.cpp
    src/server_pose_graph.cpp
    src/optimizer/smooth_euler_optimizer.cpp
    src/utility/server_utility.cpp
    src/utility/server_visualization.cpp
    src/server_camera.cpp
    src/server_brief_extractor.cpp
    src/server_keyframe.cpp
    ThirdParty/DBoW/BowVector.cpp
    ThirdParty/DBoW/FBrief.cpp
    ThirdParty/DBoW/FeatureVector.cpp
    ThirdParty/DBoW/QueryResults.cpp
    ThirdParty/DBoW/ScoringObject.cpp
    ThirdParty/DUtils/Random.cpp
    ThirdParty/DUtils/Timestamp.cpp
    ThirdParty/DVision/BRIEF.cpp
    ThirdParty/VocabularyBinary.cpp
)

target_link_libraries(server_pose_graph 
	${catkin_LIBRARIES} 
	${OpenCV_LIBS} 
	${CERES_LIBRARIES}
	${Sophus_LIBRARIES}
    ${Pangolin_LIBRARIES}
    ${OCTOMAP_LIBRARIES}
) 