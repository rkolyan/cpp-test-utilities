cmake_minimum_required(VERSION 3.2)

project(TestUtilitiesInterface)

# Configure environment
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)


add_library(TestUtilitiesInterface INTERFACE)
target_include_directories(TestUtilitiesInterface INTERFACE "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/TestUtilitiesInterface>" 
	"$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>")
