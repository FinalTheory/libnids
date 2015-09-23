cmake_minimum_required(VERSION 3.3)
project(nids)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -O2 -fPIC")

add_definitions(
        -D__APPLE__
        -D_BSD_SOURCE
        -DLIBNET_VER=1
        -DHAVE_ICMPHDR=0
        -DHAVE_TCP_STATES=0
        -DHAVE_BSD_UDPHDR=1
        -DHAVE_SOCKADDR_SA_LEN
        -DLIBNET_BSDISH_OS
        -DLIBNET_BSD_BYTE_SWAP
        -D_REENTRANT
)

set(SOURCE_FILES
        allpromisc.c
        checksum.c
        checksum.h
        hash.c
        hash.h
        ip_fragment.c
        ip_fragment.h
        ip_options.c
        killtcp.c
        libnids.c
        nids.h
        scan.c
        scan.h
        tcp.c
        tcp.h
        util.c
        util.h)


# if you could not build successfully or error occured when importing nids
# try to replace here with your real python version
set(PYVER "2.7.10_2")


string(SUBSTRING ${PYVER} 0 3 PYVER_2)
set(HOMEBREW_PY /usr/local/Cellar/python/${PYVER}/Frameworks/Python.framework/Versions/${PYVER_2})

find_path(GLIB_INCLUDE_DIR1 glib.h PATH_SUFFIXES glib-2.0)
find_path(GLIB_INCLUDE_DIR2 glibconfig.h PATH_SUFFIXES lib/glib-2.0/include)

# first try to find hombrew version of Python.h
find_path(HOME_BREW_PYHDR Python.h
        PATHS ${HOMEBREW_PY}/include/python${PYVER_2}
        NO_DEFAULT_PATH
)
# if not found, use system version
find_path(HOME_BREW_PYHDR Python.h)

include_directories(
        BEFORE SYSTEM ./
        ${GLIB_INCLUDE_DIR1}
        ${GLIB_INCLUDE_DIR2}
        ${HOME_BREW_PYHDR}
)

# first try to find hombrew version of libpython
find_library(HOMEBREW_PYLIB
        python${PYVER_2}
        PATHS ${HOMEBREW_PY}/lib
        NO_DEFAULT_PATH
)
# if not found, use system version
find_library(HOMEBREW_PYLIB python${PYVER_2})


add_library(nids SHARED ${SOURCE_FILES})
add_library(nids_static STATIC ${SOURCE_FILES})
add_library(pynids SHARED nidsmodule.c)
target_link_libraries(nids pcap net glib-2.0)
target_link_libraries(nids_static pcap net glib-2.0)
target_link_libraries(pynids nids_static ${HOMEBREW_PYLIB})
set_target_properties(nids_static PROPERTIES OUTPUT_NAME nids)
set_target_properties(pynids PROPERTIES OUTPUT_NAME "nids" PREFIX "" SUFFIX ".so")
