cmake_minimum_required(VERSION 2.8)

## 有意声明一个 project，主要是为了让后面的 CMAKE_SYSTEM 有值，CMake 要求 CMAKE_SYSTEM 必须在 project 之后。
project(3rdparty)

## 展开第三方依赖库直接编译进来，以简化安装和使用
if("${CMAKE_SYSTEM}" MATCHES "Linux")
    include(ExternalProject)
    set(3RDPARTY_DIR ${CMAKE_CURRENT_SOURCE_DIR})
    ExternalProject_Add(
        libxml2
        PREFIX ${3RDPARTY_DIR}/libxml2
        SOURCE_DIR ${3RDPARTY_DIR}/libxml2
        CONFIGURE_COMMAND ${3RDPARTY_DIR}/libxml2/configure --prefix=${3RDPARTY_DIR}/libxml2 --without-python --with-pic
        BUILD_COMMAND ${MAKE}
        )
    ExternalProject_Add(
        libuuid
        PREFIX ${3RDPARTY_DIR}/libuuid
        SOURCE_DIR ${3RDPARTY_DIR}/libuuid
        UPDATE_COMMAND sh -c "autoreconf --force --install"
        CONFIGURE_COMMAND ${3RDPARTY_DIR}/libuuid/configure --prefix=${3RDPARTY_DIR}/libuuid --with-pic
        BUILD_COMMAND ${MAKE}
        )
    ExternalProject_Add(
        libiconv
        PREFIX ${3RDPARTY_DIR}/libiconv-1.14
        DOWNLOAD_DIR ${3RDPARTY_DIR}
        DOWNLOAD_COMMAND sh -c "curl -O http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.14.tar.gz && tar xf libiconv-1.14.tar.gz"
        SOURCE_DIR ${3RDPARTY_DIR}/libiconv-1.14
        UPDATE_COMMAND sh -c "sed -i'' -e '682s/@GNULIB_GETS@/0/' ${3RDPARTY_DIR}/libiconv-1.14/srclib/stdio.in.h"
        CONFIGURE_COMMAND ${3RDPARTY_DIR}/libiconv-1.14/configure --prefix=${3RDPARTY_DIR}/libiconv-1.14/local --enable-static --with-pic --with-gnu-ld
        BUILD_COMMAND ${MAKE}
        )
endif()

if("${CMAKE_SYSTEM}" MATCHES "Linux")
    set (CMAKE_C_FLAGS "-fPIC")
    set (CMAKE_CXX_FLAGS "-fPIC")
endif()

add_subdirectory(pcre)
add_subdirectory(zlib)

if(WIN32)
    add_subdirectory(libiconv)
    add_subdirectory(libxml2/win32)
    add_subdirectory(c99-snprintf)
endif()
