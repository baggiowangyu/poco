# Sources
file(GLOB SRCS_G "src/*.cpp")
POCO_SOURCES_AUTO( TEST_SRCS ${SRCS_G})

# Headers
file(GLOB_RECURSE HDRS_G "src/*.h" )
POCO_HEADERS_AUTO( TEST_SRCS ${HDRS_G})

POCO_SOURCES_AUTO_PLAT( TEST_SRCS OFF
    src/WinDriver.cpp
)

POCO_SOURCES_AUTO_PLAT( TEST_SRCS WINCE
    src/WinCEDriver.cpp
)

add_executable(Crypto-testrunner ${TEST_SRCS} )
if(ANDROID)
    add_test(NAME Crypto WORKING_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY} 
            COMMAND ${CMAKE_COMMAND} -DANDROID_NDK=${ANDROID_NDK} -DLIBRARY_DIR=${CMAKE_BINARY_DIR}/lib -DUNITTEST=${CMAKE_BINARY_DIR}/bin/Crypto-testrunner -DTEST_PARAMETER=-all -P ${CMAKE_SOURCE_DIR}/cmake/ExecuteOnAndroid.cmake)
else()
    add_test(NAME Crypto WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR} COMMAND Crypto-testrunner -all)
endif()
target_link_libraries(Crypto-testrunner PUBLIC Poco::NetSSL Poco::XML Poco::CppUnit)

if(NOT "${INTERNAL_OPENSSL_PATH}" STREQUAL "")
	set_tests_properties(Crypto PROPERTIES  ENVIRONMENT "${INTERNAL_OPENSSL_PATH}")
endif()

if(UNIX AND NOT ANDROID)
    target_link_libraries(Crypto-testrunner PUBLIC pthread)
endif(UNIX AND NOT ANDROID)
