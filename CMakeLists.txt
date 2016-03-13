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

set(SOURCE_FILES_NIDS
        nidsmodule.c
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


# first try to find hombrew version of Python.h
find_path(
        HOME_BREW_PYHDR Python.h
        PATHS ${HOMEBREW_PY}/include/python${PYVER_2}
        NO_DEFAULT_PATH
)
# if not found, use system version
find_path(HOME_BREW_PYHDR Python.h)

include_directories(
        BEFORE SYSTEM ./
        /usr/local/include
        ${GLIB_INCLUDE_DIR1}
        ${GLIB_INCLUDE_DIR2}
        ${HOME_BREW_PYHDR}
)

# first try to find hombrew version of libpython
find_library(
        HOMEBREW_PYLIB
        python${PYVER_2}
        PATHS ${HOMEBREW_PY}/lib
        NO_DEFAULT_PATH
)
# if not found, use system version
find_library(HOMEBREW_PYLIB python${PYVER_2})


add_library(nids STATIC ${SOURCE_FILES_NIDS})
target_link_libraries(nids INTERFACE ${HOMEBREW_PYLIB})
