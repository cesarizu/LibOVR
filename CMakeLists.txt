cmake_minimum_required(VERSION 2.6)
project(LibOVR)

add_definitions(-DUNICODE -D_UNICODE -DMINGW_HAS_SECURE_API -DNOMINMAX)
add_definitions(-DOVR_BUILD_SHIPPING)
# add_definitions(-DOVR_DLL_BUILD) # shared only

file(GLOB LIBOVR_FILES
        LibOVR/Include/*.h
        LibOVR/Include/Extras/*.h
        LibOVR/Src/*.cpp
        LibOVR/Src/*.c

        LibOVRKernel/*/*/*.cpp
        LibOVRKernel/*/*/*.c
        LibOVRKernel/*/*/*.h

        Logging/src/*.cpp
        Logging/src/internal/*.cpp
        Logging/src/include/*.h
        )

add_library(LibOVR STATIC ${LIBOVR_FILES})

target_include_directories(LibOVR PUBLIC LibOVR/Include Logging/Include)
target_include_directories(LibOVR PRIVATE LibOVR/Src)

file(GLOB OVR_KERNEL_INCLUDES "${CMAKE_CURRENT_SOURCE_DIR}/LibOVRKernel/Src/*/")

target_include_directories(LibOVR PRIVATE ${OVR_KERNEL_INCLUDES} LibOVRKernel/Src)

if(CMAKE_COMPILER_IS_GNUCXX OR APPLE)
    set(CMAKE_CXX_FLAGS " -std=c++11 ${CMAKE_CXX_FLAGS} -fpermissive ")
endif()

if(WIN32)
    target_link_libraries(
        LibOVR winmm setupapi ws2_32 imagehlp wbemuuid opengl32 psapi
        advapi32 version ntdll shlwapi
    )
endif()

file(COPY "${CMAKE_CURRENT_SOURCE_DIR}/LibOVR/Include"
     DESTINATION "${CMAKE_CURRENT_BINARY_DIR}")

#install(DIRECTORY "LibOVR/Include" DESTINATION .)
#install(TARGETS LibOVR DESTINATION .)

if(",${CMAKE_CURRENT_BINARY_DIR}" STREQUAL ",")
    set(CMAKE_INSTALL_PREFIX "${CMAKE_CURRENT_BINARY_DIR}/install" CACHE PATH "" FORCE)
endif()
