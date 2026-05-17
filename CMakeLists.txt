cmake_minimum_required(VERSION 3.10)
project(print)

set(PRINT_VERSION_MAJOR 0)
set(PRINT_VERSION_MINOR 1)
set(PRINT_VERSION_PATCH 0)
set(PRINT_VERSION_TWEAK 0)
set(PRINT_VERSION "${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK}")
set(PRINT_VERSION_STRING "v${PRINT_VERSION}")

# Собираем статическую библиотеку formatter
add_library(formatter STATIC formatter_lib/formatter.cpp formatter_lib/formatter.h)

# Собираем исполняемый файл solver
add_executable(solver solver_application/main.cpp)

# Связываем solver с библиотекой formatter
target_link_libraries(solver formatter)

# Указываем правило установки для solver (для CPack)
install(TARGETS solver RUNTIME DESTINATION bin)

# Подключаем конфиг CPack
enable_testing()
add_test(NAME dummy_test COMMAND solver)

include(CPackConfig.cmake)
