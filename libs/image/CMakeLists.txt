cmake_minimum_required(VERSION 3.1)
project(image)

set(TARGET image)
set(PUBLIC_HDR_DIR include)

# ==================================================================================================
# Sources and headers
# ==================================================================================================
set(PUBLIC_HDRS
        include/image/ColorTransform.h
        include/image/LinearImage.h
        include/image/ImageSampler.h
        include/image/ImageOps.h
)

set(SRCS
        src/LinearImage.cpp
        src/ImageSampler.cpp
        src/ImageOps.cpp
)

# ==================================================================================================
# Include and target definitions
# ==================================================================================================
include_directories(${PUBLIC_HDR_DIR})

add_library(${TARGET} STATIC ${PUBLIC_HDRS} ${SRCS})

target_link_libraries(${TARGET} PUBLIC math utils)

target_include_directories(${TARGET} PUBLIC ${PUBLIC_HDR_DIR})

# ==================================================================================================
# Compiler flags
# ==================================================================================================
target_compile_options(${TARGET} PRIVATE -Wno-deprecated-register)

if (MSVC OR CLANG_CL)
    target_compile_options(${TARGET} PRIVATE $<$<CONFIG:Release>:/fp:fast>)
else()
    target_compile_options(${TARGET} PRIVATE $<$<CONFIG:Release>:-ffast-math>)
endif()

# ==================================================================================================
# Expose header-only utilities to lean-and-mean client projects.
# ==================================================================================================
add_library(image_headers INTERFACE)
target_include_directories(image_headers INTERFACE ${PUBLIC_HDR_DIR})

# ==================================================================================================
# Tests
# ==================================================================================================
if (NOT ANDROID)
    add_executable(test_${TARGET} tests/test_image.cpp)
    target_link_libraries(test_${TARGET} PRIVATE image imageio gtest)
endif()
