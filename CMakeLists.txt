cmake_minimum_required(VERSION 3.25)
project(tml LANGUAGES CXX)

add_library(tml INTERFACE)

# Require C++20 for the spaceship operator
target_compile_features(tml INTERFACE cxx_std_20)

# Expose the include directory
target_include_directories(tml INTERFACE
        "${CMAKE_CURRENT_SOURCE_DIR}/include"
)