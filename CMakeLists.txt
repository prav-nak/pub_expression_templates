cmake_minimum_required(VERSION 3.0)

project (ExpressionTemplates)
set(target ExpressionTemplates)

set(FEMSOURCES
src/Main2.cpp
)

set(FEMINCLUDES
include/Expr.hpp
include/Ops.hpp
include/ExprOps.hpp
include/NumericTraits.hpp
)

find_package(Boost 1.65.0 COMPONENTS REQUIRED graph regex system filesystem program_options)
IF (Boost_FOUND)
    message("BOOST is found: ${Boost_INCLUDE_DIR}")
    include_directories(${Boost_INCLUDE_DIR})
    set (Boost_USE_MULTITHREAD ON)  # enable multithreading
    find_package (Boost 1.65.0 COMPONENTS REQUIRED graph regex system filesystem program_options)
endif()
find_package(Boost 1.65.0 COMPONENTS graph filesystem system program_options REQUIRED)
add_definitions(-DBOOST_SYSTEM_NO_DEPRECATED)
add_definitions(-DBOOST_NO_CXX11_SCOPED_ENUMS)
add_definitions(-DBOOST_ERROR_CODE_HEADER_ONLY)

include_directories(include ${BOOST_ROOT}/include)

#include_directories(include)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2 -pg -std=c++17 -march=native")

add_executable (ExpressionTemplates ${FEMSOURCES} ${FEMINCLUDES})
#add_library (ExpressionTemplates ${FEMSOURCES} ${FEMINCLUDES})

# linking
target_link_libraries(ExpressionTemplates PUBLIC ${Boost_LIBRARIES} emon)

# install
install(TARGETS ExpressionTemplates DESTINATION ${CMAKE_INSTALL_PREFIX})
