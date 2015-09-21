cmake_minimum_required(VERSION 3.3)
project(nids)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} \
-g -O2 \
-D__APPLE__ \
-DENABLE_TCPREASM \
-Wno-deprecated-declarations \
-D_BSD_SOURCE \
-DLIBNET_VER=1 \
-DHAVE_ICMPHDR=0 \
-DHAVE_TCP_STATES=0 \
-DHAVE_BSD_UDPHDR=1 \
-DHAVE_SOCKADDR_SA_LEN \
-DLIBNET_BSDISH_OS \
-DLIBNET_BSD_BYTE_SWAP \
-D_REENTRANT")

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


find_path(GLIB_INCLUDE_DIR1 glib.h PATH_SUFFIXES glib-2.0)
find_path(GLIB_INCLUDE_DIR2 glibconfig.h PATH_SUFFIXES lib/glib-2.0/include)

include_directories(SYSTEM ./ ${GLIB_INCLUDE_DIR1} ${GLIB_INCLUDE_DIR2})


add_library(nids SHARED ${SOURCE_FILES})
add_library(nids_static STATIC ${SOURCE_FILES})
target_link_libraries(nids pcap net gthread-2.0 glib-2.0)
target_link_libraries(nids_static pcap net gthread-2.0 glib-2.0)
set_target_properties(nids_static PROPERTIES OUTPUT_NAME nids)
