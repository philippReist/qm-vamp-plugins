cmake_minimum_required(VERSION 3.15 FATAL_ERROR)

project(lib-qm-vamp)

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/lib/qm-dsp)

add_definitions(-D_USRDLL)
add_definitions(-DQMVAMPPLUGINS_EXPORTS)
add_definitions(-D_USE_MATH_DEFINES)
add_definitions(-DNOMINMAX)
add_definitions(-DADD_)
add_definitions(-Dkiss_fft_scalar=double)

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