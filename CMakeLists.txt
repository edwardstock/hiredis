cmake_minimum_required(VERSION 2.8)
cmake_policy(SET CMP0048 NEW)
# versioning
file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/hiredis.h" HIREDIS_H_CONTENTS)

string(REGEX MATCH "HIREDIS_MAJOR[ \t]+([0-9]+)"
       HIREDIS_VERSION_MAJOR ${HIREDIS_H_CONTENTS})
string(REGEX MATCH "([0-9]+)"
       HIREDIS_VERSION_MAJOR ${HIREDIS_VERSION_MAJOR})
string(REGEX MATCH "HIREDIS_MINOR[ \t]+([0-9]+)"
       HIREDIS_VERSION_MINOR ${HIREDIS_H_CONTENTS})
string(REGEX MATCH "([0-9]+)"
       HIREDIS_VERSION_MINOR ${HIREDIS_VERSION_MINOR})
string(REGEX MATCH "HIREDIS_PATCH[ \t]+([0-9]+)"
       HIREDIS_VERSION_PATCH ${HIREDIS_H_CONTENTS})
string(REGEX MATCH "([0-9]+)"
       HIREDIS_VERSION_PATCH ${HIREDIS_VERSION_PATCH})

set(HIREDIS_VERSION
    ${HIREDIS_VERSION_MAJOR}.${HIREDIS_VERSION_MINOR}.${HIREDIS_VERSION_PATCH})

project(hiredis VERSION ${HIREDIS_VERSION} LANGUAGES "C")

set(CMAKE_MACOSX_RPATH 1)

# redis-server configuration used for testing
set(REDIS_PORT 56379)
set(REDIS_SERVER "redis-server")
set(REDIS_TEST_CONFIG
    "# redis-server configuration generated by CMake
daemonize yes
port ${REDIS_PORT}
bind 127.0.0.1
pidfile ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-test.pid
unixsocket /tmp/redis.sock"
    )
file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/redis.conf ${REDIS_TEST_CONFIG})

# compiler options
set(CMAKE_C_FLAGS "-Wall -Wextra -std=c99")

# compiler include paths
include_directories(
	"${CMAKE_CURRENT_SOURCE_DIR}"
)

# sources
set(HIREDIS_SRC
    async.c
    hiredis.c
    net.c
    read.c
    sds.c
    )

option(ENABLE_STATIC "Enables static library or shared" ON)

if (ENABLE_STATIC)
	add_library(${PROJECT_NAME} STATIC ${HIREDIS_SRC})
else ()
	add_library(${PROJECT_NAME} SHARED ${HIREDIS_SRC})
endif ()