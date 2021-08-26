cmake_minimum_required(VERSION 3.16)

project(libtins-windows-seed)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

add_definitions(-DTINS_STATIC)

add_executable(libtins-windows-seed src/main.cpp)

if(WIN32)
  set(WPD WpdPack)
  set(WPD_ZIP ${WPD}_4_1_2.zip)
  file(DOWNLOAD https://www.winpcap.org/install/bin/${WPD_ZIP} ${CMAKE_BINARY_DIR}/${WPD_ZIP})
  file(ARCHIVE_EXTRACT INPUT ${CMAKE_BINARY_DIR}/${WPD_ZIP} DESTINATION ${CMAKE_BINARY_DIR})
  set(PCAP_ROOT_DIR ${CMAKE_BINARY_DIR}/${WPD})
endif()

include(FetchContent)

# Allow sub-project options to be set with cmake `set`.
set(CMAKE_POLICY_DEFAULT_CMP0077 NEW)

# Fetch and set up libtins library.
set(LIBTINS_ENABLE_WPA2 False)
set(LIBTINS_ENABLE_ACK_TRACKER False)
set(LIBTINS_ENABLE_CXX11 True)
set(LIBTINS_ENABLE_TCP_STREAM_CUSTOM_DATA False)
set(LIBTINS_BUILD_SHARED False)
set(LIBTINS_BUILD_EXAMPLES False)
set(LIBTINS_BUILD_TESTS False)
FetchContent_Declare(
  tins
  GIT_REPOSITORY https://github.com/mfontanini/libtins
  GIT_TAG        v4.2
)
FetchContent_MakeAvailable(tins)
target_include_directories(libtins-windows-seed PUBLIC ${PCAP_INCLUDE_DIR})
target_include_directories(libtins-windows-seed PUBLIC ${tins_SOURCE_DIR}/include)

target_link_libraries(libtins-windows-seed PUBLIC tins)
