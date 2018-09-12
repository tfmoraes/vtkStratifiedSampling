cmake_minimum_required(VERSION 2.6)

Project(StratifiedSampling)
# set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake_scripts/  ${CMAKE_MODULE_PATH})
# ENABLE_TESTING()

FIND_PACKAGE(VTK REQUIRED)
INCLUDE(${VTK_USE_FILE})

# include_directories("${PYTHON_INCLUDE_DIR}")
# set(NAME StratifiedSampling CACHE STRING "vtk-gdcm lib name")


# SET(BUILD_PARAVIEW_PLUGIN OFF CACHE BOOL "Build Paraview plugin?")

SET(sources boxbox.cpp
diffuse.cc
edgeflip.cc
faceflip.cc
filter.cc
ICP.cc
KDtree.cc
lmsmooth.cc
pointsample.cpp
remove.cc
reorder_verts.cc
sample.cpp
stratify.cpp
subdiv.cc
tribox.cpp
TriMesh.cc
TriMesh_bounding.cc
TriMesh_connectivity.cc
TriMesh_curvature.cc
TriMesh_grid.cc
TriMesh_normals.cc
TriMesh_pointareas.cc
TriMesh_stats.cc
TriMesh_tstrips.cc)

# if(BUILD_PARAVIEW_PLUGIN)
# FIND_PACKAGE(ParaView REQUIRED)
# INCLUDE(${PARAVIEW_USE_FILE})

# INCLUDE_DIRECTORIES(${INCLUDE_DIRECTORIES} ../)

# ADD_PARAVIEW_PLUGIN(StratifiedSamplingPlugin "1.0"
  # SERVER_MANAGER_XML vtkStratifiedSampling.xml
# SERVER_MANAGER_SOURCES vtkStratifiedSampling.cxx  
# SERVER_SOURCES boxbox.cpp diffuse.cc edgeflip.cc faceflip.cc filter.cc ICP.cc KDtree.cc lmsmooth.cc pointsample.cpp remove.cc reorder_verts.cc sample.cpp stratify.cpp subdiv.cc tribox.cpp TriMesh.cc TriMesh_bounding.cc TriMesh_connectivity.cc TriMesh_curvature.cc TriMesh_grid.cc TriMesh_normals.cc TriMesh_pointareas.cc TriMesh_stats.cc TriMesh_tstrips.cc )

# endif(BUILD_PARAVIEW_PLUGIN)

add_library(vtkStratifiedSampling SHARED ${sources} vtkStratifiedSampling.cxx)
target_link_libraries(vtkStratifiedSampling ${VTK_LIBRARIES})

# ADD_EXECUTABLE(StratifiedSampling  vtkStratifiedSampling.cxx main.cpp
# ${sources}
# )

# TARGET_LINK_LIBRARIES(StratifiedSampling ${VTK_LIBRARIES})

# ##############
# ADD_EXECUTABLE(Test Test.cxx vtkStratifiedSampling.cxx 
# ${sources})
# TARGET_LINK_LIBRARIES(Test ${VTK_LIBRARIES})

# ADD_TEST(StratifiedSamplingTest Test)

include(${VTK_CMAKE_DIR}/vtkWrapPython.cmake)
        include_directories(
          ${PYTHON_INCLUDE_DIR}
          ${CMAKE_CURRENT_SOURCE_DIR}
        )
# set(WRAP_LIST StratifiedSampling)
VTK_WRAP_PYTHON3(vtkStratifiedSamplingPython vtkStratifiedSamplingPython_SRCS vtkStratifiedSampling.cxx)

add_library(vtkStratifiedSamplingPythonD SHARED ${vtkStratifiedSamplingPython_SRCS} vtkStratifiedSampling.cxx)
target_link_libraries(vtkStratifiedSamplingPythonD
        ${VTK_LIBRARIES}
        vtkStratifiedSampling
        vtkWrappingPythonCore
        ${VTK_PYTHON_LIBRARIES})

    add_library(vtkStratifiedSamplingPython SHARED vtkStratifiedSamplingPythonInit.cxx)

set(VTK_MODULES_USED
    vtkCommonDataModel
    vtkCommonCore
    vtkImagingCore
    vtkImagingSources
    vtkIOImage
    vtkIOLegacy)
set(VTK_PYTHOND_LIBS)
foreach(TMP_LIB ${VTK_MODULES_USED})
   set(VTK_PYTHOND_LIBS ${VTK_PYTHOND_LIBS} ${TMP_LIB}PythonD)
endforeach()

target_link_libraries(vtkStratifiedSamplingPython vtkStratifiedSampling vtkStratifiedSamplingPythonD ${VTK_PYTHOND_LIBS} ${VTK_LIBRARIES})

set_target_properties(vtkStratifiedSamplingPython PROPERTIES PREFIX "")
if(WIN32 AND NOT CYGWIN)
    set_target_properties(vtkStratifiedSamplingPython PROPERTIES SUFFIX ".pyd")
endif(WIN32 AND NOT CYGWIN)
