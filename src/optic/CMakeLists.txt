PROJECT(optic)

set(DOXYFILE_TARGET_SUFFIX optic)
include(UseDoxygen OPTIONAL)

find_package(PkgConfig REQUIRED)
pkg_check_modules(cbc cbc)


set(CMAKE_CXX_FLAGS "-Wall ${CMAKE_CXX_FLAGS}")

if(STATIC_OPTIC)
set(CMAKE_CXX_LINK_FLAGS "-static ${CMAKE_CXX_LINK_FLAGS}")
endif(STATIC_OPTIC)

include_directories(${val_SOURCE_DIR} ${val_SOURCE_DIR}/parsing ${val_BINARY_DIR} ${cbc_INCLUDE_DIRS} ${CPLEX_INCLUDES} ${CONCERT_INCLUDES}  ${GSL_INCLUDES})

set(optic_build_srcs
    compressionsafescheduler.cpp
    totalordertransformer.cpp
    partialordertransformer.cpp
    FFSolver.cpp
    FFEvent.cpp
    globals.cpp
    lpscheduler.cpp
    RPGBuilder.cpp
    RPGBuilderAnalysis.cpp
    RPGBuilderEvaluation.cpp
    RPGBuilderNumerics.cpp
    temporalanalysis.cpp
    minimalstate.cpp
    temporalconstraints.cpp
    numericanalysis.cpp
    solver.cpp
    NNF.cpp
    PreferenceHandler.cpp
    choosepreconditions.cpp)

add_library(OpticCommon STATIC ${optic_build_srcs})
target_link_libraries(OpticCommon ParsePDDL Inst)
SET_TARGET_PROPERTIES(OpticCommon PROPERTIES COMPILE_FLAGS "-DPOPF3ANALYSIS")

if (cbc_INCLUDE_DIRS)

    ADD_EXECUTABLE(optic-clp opticMain.cpp solver-clp.cpp)
    
    target_link_libraries(optic-clp
        OpticCommon
        ${cbc_LIBRARIES}
    )
    SET_TARGET_PROPERTIES(optic-clp PROPERTIES COMPILE_FLAGS "-DPOPF3ANALYSIS")

endif(cbc_INCLUDE_DIRS)

if(CPLEX AND ILOCPLEX AND CONCERT AND CPLEX_INCLUDES AND CONCERT_INCLUDES)

    ADD_EXECUTABLE(optic-cplex opticMain.cpp solver-cpx.cpp)
    
    target_link_libraries(optic-cplex    
            OpticCommon
            ${ILOCPLEX} ${CPLEX} ${CONCERT} pthread
    )
    SET_TARGET_PROPERTIES(optic-cplex PROPERTIES COMPILE_FLAGS "-DPOPF3ANALYSIS")
    
endif(CPLEX AND ILOCPLEX AND CONCERT AND CPLEX_INCLUDES AND CONCERT_INCLUDES)

