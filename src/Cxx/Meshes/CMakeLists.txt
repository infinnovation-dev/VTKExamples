project (${WIKI}Meshes)

if(NOT VTK_BINARY_DIR)
  set(VTK_LIBRARIES "")
  find_package(VTK COMPONENTS
    vtkCommonColor
    vtkCommonCore
    vtkCommonDataModel
    vtkCommonExecutionModel
    vtkFiltersCore
    vtkFiltersExtraction
    vtkFiltersGeneral
    vtkFiltersGeometry
    vtkFiltersModeling
    vtkFiltersPoints
    vtkFiltersSources
    vtkFiltersVerdict
    vtkIOExodus
    vtkIOGeometry
    vtkIOLegacy
    vtkIOPLY
    vtkIOXML
    vtkImagingCore
    vtkInteractionStyle
    vtkRenderingContextOpenGL2
    vtkRenderingCore
    vtkRenderingFreeType
    vtkRenderingGL2PSOpenGL2
    vtkRenderingOpenGL2
    OPTIONAL_COMPONENTS
    vtkTestingRendering
    QUIET
    )
  if (VTK_VERSION VERSION_LESS "8.90.0")
    include(${VTK_USE_FILE})
  endif()
endif()

set(KIT_LIBS ${VTK_LIBRARIES})

#
# Build all .cxx files in the directory
file(GLOB ALL_FILES *.cxx)

set(VERSION_MIN "6.0")
Requires_Version(DeformPointSet ${VERSION_MIN} ALL_FILES)

foreach(SOURCE_FILE ${ALL_FILES})
  string(REPLACE ".cxx" "" TMP ${SOURCE_FILE})
  string(REPLACE ${CMAKE_CURRENT_SOURCE_DIR}/ "" EXAMPLE ${TMP})
  add_executable(${WIKI}${EXAMPLE} ${EXECUTABLE_FLAG} ${EXAMPLE}.cxx)
  target_link_libraries(${WIKI}${EXAMPLE} ${KIT_LIBS})
  if (VTK_VERSION VERSION_GREATER "8.8")
    vtk_module_autoinit(
      TARGETS ${WIKI}${EXAMPLE}
      MODULES ${VTK_LIBRARIES}
      )
  endif()
endforeach()

if (BUILD_TESTING)
  # Testing
  set(KIT Meshes)
  set(NEEDS_ARGS
    ClipFrustum
    ClipDataSetWithPolyData
    Decimation
    FillHoles
    IdentifyHoles
    InterpolateFieldDataDemo
    MatrixMathFilter
    OBBDicer
    QuadricClustering
    QuadricDecimation
    SplitPolyData
    SubdivisionDemo
    TableBasedClipDataSetWithPolyData
    )
  set(DATA ${WikiExamples_SOURCE_DIR}/src/Testing/Data)

  add_test(${KIT}-ClipFrustum ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestClipFrustum ${DATA}/Armadillo.ply)

  add_test(${KIT}-Decimation ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestDecimation ${DATA}/Torso.vtp)

  add_test(${KIT}-QuadricDecimation ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestQuadricDecimation ${DATA}/Torso.vtp)

  add_test(${KIT}-QuadricClustering ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestQuadricClustering ${DATA}/Torso.vtp)

  add_test(${KIT}-ClipDataSetWithPolyData ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestClipDataSetWithPolyData -E 25)

  add_test(${KIT}-FillHoles ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestFillHoles ${DATA}/Torso.vtp)

  add_test(${KIT}-IdentifyHoles ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestIdentifyHoles ${DATA}/Torso.vtp)

  add_test(${KIT}-InterpolateFieldDataDemo ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestInterpolateFieldDataDemo ${DATA}/coarseGrid.e ${DATA}/fineGrid.e-s002)

  add_test(${KIT}-OBBDicer ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestOBBDicer ${DATA}/Bunny.vtp)

  add_test(${KIT}-MatrixMathFilter ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestMatrixMathFilter ${DATA}/tensors.vtk)

  add_test(${KIT}-SplitPolyData ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestSplitPolyData ${DATA}/cowHead.vtp)

  add_test(${KIT}-SubdivisionDemo ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestSubdivisionDemo ${DATA}/fsu/footbones.ply 2)

  add_test(${KIT}-TableBasedClipDataSetWithPolyData ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
    TestTableBasedClipDataSetWithPolyData -E 25)

  include(${WikiExamples_SOURCE_DIR}/CMake/ExamplesTesting.cmake)
endif()
