#set(HTSLIB_SOURCES
#   bcf_sr_sort.c
#   bgzf.c
#   bgzip.c
#   errmod.c
#   faidx.c
#   hfile.c
#   hfile_gcs.c
#   hfile_libcurl.c
#   hfile_net.c
#   hfile_s3.c
#   hts.c
#   htsfile.c
#   hts_os.c
#   kfunc.c
#   knetfile.c
#   kstring.c
#   md5.c
#   multipart.c
#   plugin.c
#   probaln.c
#   realn.c
#   regidx.c
#   sam.c
#   synced_bcf_reader.c
#   tbx.c
#   textutils.c
#   thread_pool.c
#   vcf.c
#   vcf_sweep.c
#   vcfutils.c
#)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -pthread")
if (NOT "${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang") # clang Doesnt use pthread
   #set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
endif()
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fpermissive")

set(HTSLIB_SOURCES
	kfunc.c
	knetfile.c
	kstring.c
	bcf_sr_sort.c
	bgzf.c
	errmod.c
	faidx.c
	hfile.c
	hfile_net.c
	hts.c
	hts_os.c
	md5.c
	multipart.c
	probaln.c
	realn.c
	regidx.c
	sam.c
	synced_bcf_reader.c
	vcf_sweep.c
	tbx.c
	textutils.c
	thread_pool.c
	vcf.c
	vcfutils.c
	cram/cram_codecs.c
	cram/cram_decode.c
	cram/cram_encode.c
	cram/cram_external.c
	cram/cram_index.c
	cram/cram_io.c
	cram/cram_samtools.c
	cram/cram_stats.c
	cram/files.c
	cram/mFILE.c
	cram/open_trace_file.c
	cram/pooled_alloc.c
	cram/rANS_static.c
	cram/sam_header.c
	cram/string_alloc.c
)

SET(BGZIP_SRC
    bgzip.c
)

SET(TABIX_SRC
    tabix.c
)

SET(HTSFILE_SRC
    htsfile.c
)

add_library(htslib_core STATIC
  ${HTSLIB_SOURCES}
)

SET(HTSLIBCORE_LIB htslib_core CACHE STRING "THE CORE LIBRARY") #add graphite_core and graphite_utils to the list of core libs

add_executable(bgzip
	${BGZIP_SRC}
)

target_link_libraries(bgzip
    ${HTSLIBCORE_LIB}
	${CMAKE_THREAD_LIBS_INIT}
	${ZLIB_LIBRARY}
	${TABIX_LIB}
)

add_executable(tabix
	${TABIX_SRC}
)

target_link_libraries(tabix
    ${HTSLIBCORE_LIB}
	${CMAKE_THREAD_LIBS_INIT}
	${ZLIB_LIBRARY}
)

add_executable(htsfile
	${HTSFILE_SRC}
)

target_link_libraries(htsfile
    ${HTSLIBCORE_LIB}
	${CMAKE_THREAD_LIBS_INIT}
	${ZLIB_LIBRARY}
)

add_dependencies(htsfile ${HTSLIB_EXTERNAL_PROJECT})
add_dependencies(tabix ${HTSLIB_EXTERNAL_PROJECT})
add_dependencies(bgzip ${HTSLIB_EXTERNAL_PROJECT})


add_custom_command(TARGET tabix POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
	"${CMAKE_BINARY_DIR}/core/tabix"
	${CMAKE_BINARY_DIR})

add_custom_command(TARGET bgzip POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
	"${CMAKE_BINARY_DIR}/core/bgzip"
	${CMAKE_BINARY_DIR})

add_custom_command(TARGET htsfile POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
	"${CMAKE_BINARY_DIR}/core/htsfile"
	${CMAKE_BINARY_DIR})