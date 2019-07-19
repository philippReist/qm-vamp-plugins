cmake_minimum_required(VERSION 3.15 FATAL_ERROR)

project(lib-qm-vamp)

add_definitions(-D_USRDLL)
add_definitions(-DQMVAMPPLUGINS_EXPORTS)
add_definitions(-D_USE_MATH_DEFINES)
add_definitions(-DNOMINMAX)
add_definitions(-DADD_)
add_definitions(-Dkiss_fft_scalar=double)

if (NOT WIN32)
  add_definitions(-DUSE_PTHREADS)
endif()

aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/base BASE_SRC)

# collect all src files for dsp
file(GLOB_RECURSE DSP_SRC ${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/dsp/*.cpp)

# collect all src files for ext
file(GLOB_RECURSE EXT_SRC ${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/ext/*.c)

# add all include directories for ext
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/ext/cblas/include
                    ${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/ext/clapack/include
                    ${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/ext/kissfft/tools
                    ${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/ext/kissfft)
                    
# collect all src files for hmm
file(GLOB_RECURSE HMM_SRC ${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/hmm/*.c)

# collect all src files for maths:
file(GLOB_RECURSE M_SRC ${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/maths/*.cpp)
set(MATHS_SRC ${M_SRC} ${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/maths/pca)

# collect all src files for thread:
file(GLOB_RECURSE THREAD_SRC ${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/thread/*.cpp)

add_library(lib-qm-dsp STATIC
            ${BASE_SRC}
            ${DSP_SRC}
            ${EXT_SRC}
            ${HMM_SRC}
            ${MATHS_SRC}
            ${THREAD_SRC})

target_include_directories(lib-qm-dsp PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/ext/clapack/include
${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/qm-dsp/ext/kissfft
${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/ext/cblas/include
${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp
${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp/ext/kissfft/tools
)

aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/plugins PLUG_SRC)

set(VMPSDK_SRC
${CMAKE_CURRENT_SOURCE_DIR}/lib/vamp-plugin-sdk/src/vamp-sdk/FFT.cpp
${CMAKE_CURRENT_SOURCE_DIR}/lib/vamp-plugin-sdk/src/vamp-sdk/PluginAdapter.cpp
${CMAKE_CURRENT_SOURCE_DIR}/lib/vamp-plugin-sdk/src/vamp-sdk/RealTime.cpp
)

add_library(lib-qm-vamp
            ${VMPSDK_SRC}
            ${PLUG_SRC})

target_link_libraries(lib-qm-vamp lib-qm-dsp)

target_include_directories(lib-qm-vamp PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/plugins)
target_include_directories(lib-qm-vamp PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/lib/vamp-plugin-sdk)
