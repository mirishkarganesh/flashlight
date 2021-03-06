cmake_minimum_required(VERSION 3.10)

# ----------------------------- Dependencies -----------------------------
# CBLAS
if (FL_LIBRARIES_USE_MKL)
  find_package(MKL REQUIRED)
  set(CBLAS_LIBRARIES ${MKL_LIBRARIES})
  set(CBLAS_INCLUDE_DIR ${MKL_INCLUDE_DIR})
  setup_install_find_module(${CMAKE_MODULE_PATH}/FindMKL.cmake)
else ()
  find_package(CBLAS REQUIRED)
endif ()
message(STATUS "CBLAS found (include: ${CBLAS_INCLUDE_DIR}, library: ${CBLAS_LIBRARIES})")

# FFTW
find_package(FFTW3 REQUIRED)
if (FFTW3_FOUND)
  message(STATUS "FFTW found")
  setup_install_find_module(${CMAKE_MODULE_PATH}/FindFFTW3.cmake)
else()
  message(FATAL_ERROR "FFTW not found")
endif()

# OpenMP
find_package(OpenMP REQUIRED)
set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)

# Threads/OpenMP pollutes other compilers (e.g. nvcc) with flags
target_compile_options(fl-libraries PRIVATE $<$<COMPILE_LANGUAGE:C>:${OpenMP_C_FLAGS}>)
target_compile_options(fl-libraries PRIVATE $<$<COMPILE_LANGUAGE:CXX>:${OpenMP_CXX_FLAGS}>)
target_compile_options(fl-libraries PRIVATE $<$<COMPILE_LANGUAGE:CXX>:${CMAKE_THREAD_LIBS_INIT}>)

# ----------------------------- Lib -----------------------------
target_sources(
  fl-libraries
  PRIVATE
  ${CMAKE_CURRENT_LIST_DIR}/Ceplifter.cpp
  ${CMAKE_CURRENT_LIST_DIR}/Dct.cpp
  ${CMAKE_CURRENT_LIST_DIR}/Derivatives.cpp
  ${CMAKE_CURRENT_LIST_DIR}/Dither.cpp
  ${CMAKE_CURRENT_LIST_DIR}/Mfcc.cpp
  ${CMAKE_CURRENT_LIST_DIR}/Mfsc.cpp
  ${CMAKE_CURRENT_LIST_DIR}/PowerSpectrum.cpp
  ${CMAKE_CURRENT_LIST_DIR}/PreEmphasis.cpp
  ${CMAKE_CURRENT_LIST_DIR}/SpeechUtils.cpp
  ${CMAKE_CURRENT_LIST_DIR}/TriFilterbank.cpp
  ${CMAKE_CURRENT_LIST_DIR}/Windowing.cpp
  )

target_link_libraries(
  fl-libraries
  PRIVATE
  ${CBLAS_LIBRARIES}
  FFTW3::fftw3
  )

target_include_directories(
  fl-libraries
  PUBLIC
  ${CBLAS_INCLUDE_DIR}
  )
