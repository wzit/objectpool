cmake_minimum_required(VERSION 2.8.12)

project(OBJECT_POOL)

if(MSVC)
	if((MSVC_VERSION LESS 1800))
		message(FATAL_ERROR "Only MSVC12 and newer are supported.")
	else()
		add_compile_options(-Zi -W4 -WX)
	endif()
else()
	add_compile_options(-g -std=c++11 -Wall -Wextra -Werror)
endif()

set(CPPSRCS
	src/object_pool.cpp
	)

set(CPPHDRS
	src/object_pool.hpp
	)

add_executable(runtests ${CPPHDRS} ${CPPSRCS} src/catch.hpp src/runtests.cpp)
target_compile_definitions(runtests PRIVATE -DUNIT_TESTS)
add_executable(runbench ${CPPHDRS} ${CPPSRCS} src/bench.hpp src/runbench.cpp)
target_compile_definitions(runbench PRIVATE -DBENCHMARKS)

# compare performance against boost object_pool if available
set(Boost_USE_STATIC_LIBS ON)
set(Boost_USE_STATIC_RUNTIME ON)
find_package(Boost COMPONENTS system)
if(Boost_FOUND)
	target_compile_definitions(runbench PRIVATE -DBENCH_BOOST_POOL)
	target_include_directories(runbench PRIVATE ${Boost_INCLUDE_DIRS})
	target_link_libraries(runbench PRIVATE ${Boost_LIBRARIES})
endif()
