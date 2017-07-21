# =================================
# Yep, that's right, CMakeified htslib
#
# htslib/CMakeLists.tx
# =================================

#Set up min CMake version
CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

#Set project name
PROJECT(htslib)

SET(VERSION_MAJOR "1")
SET(VERSION_MINOR "3")
SET(VERSION_PATCH "1-61-ge87ae87")
SET(VERSION "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")

IF("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")
    MESSAGE(SEND_ERROR "In-source builds are not allowed, please build this in the bin directory!")
ENDIF("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")

#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -pthread")
if (NOT "${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang") # clang Doesnt use pthread
   #set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
endif()

find_package(Threads)
find_package(OpenSSL REQUIRED)
if (OPENSSL_FOUND)
   include_directories(${OPENSSL_INCLUDE_DIR})
   add_definitions( -DHAVE_HMAC=1 )
   #target_link_libraries(htslib ${ZLIB_LIBRARIES})
else(OPENSSL_FOUND)
   MESSAGE(FATAL_ERROR "Build failed, OpenSSL is required")
endif(OPENSSL_FOUND)

if(MSVS OR MSYS OR MINGW)
   add_definitions(-DPLUGIN_EXT=".dll")
endif ()
if (UNIX)
   add_definitions(-DPLUGIN_EXT=".so")
endif ()

INCLUDE_DIRECTORIES(config/)
INCLUDE_DIRECTORIES(core/)

ADD_SUBDIRECTORY(cmakefiles)
ADD_SUBDIRECTORY(config)
ADD_SUBDIRECTORY(core)


#dummy exe for now
#add_executable(tabix
#    ${TABIX_SRCS}
#)

# link libraries to executables
#target_link_libraries(htslib
#  ${HTSLIBCORE_LIB}
#)