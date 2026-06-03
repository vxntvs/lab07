cmake_minimum_required(VERSION 3.10)

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/cpp-pm/hunter/archive/v0.23.251.tar.gz"
    SHA1 "5659b15dc0884d4b03dbd95710e6a1fa0fc3258d"
    FILEPATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Hunter/config.cmake"
)

project(print)

set(PRINT_VERSION_MAJOR 0)
set(PRINT_VERSION_MINOR 1)
set(PRINT_VERSION_PATCH 0)
set(PRINT_VERSION "${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}")
set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)

option(BUILD_TESTS "Build tests" OFF)

add_library(print STATIC sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_TESTS)
  enable_testing()
  add_executable(check tests/test1.cpp)
  
  # Поиск путей к заголовочным файлам (уже успешно работает)
  find_path(REAL_GTEST_INCLUDE_DIR gtest/gtest.h)
  target_include_directories(check PRIVATE ${REAL_GTEST_INCLUDE_DIR})
  
  # Линкуем строго через заполненные Hunter-ом переменные путей к файлам библиотек
  target_link_libraries(check print ${GTEST_LIBRARIES} ${GTEST_MAIN_LIBRARIES})
  add_test(NAME check COMMAND check)
endif()

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)

install(TARGETS print EXPORT print-config DESTINATION lib)
install(DIRECTORY include/ DESTINATION include)
install(EXPORT print-config DESTINATION lib/cmake/print)
