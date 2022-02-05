# BUILD_SHARED_LIBS = true or STATIC if  = false
# the build directory and  install directory have diffrent include directories
# so we need to specfy them separetly. We'll use generator expression to do that
# thegenerator expression is key value pair: BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}
# CMAKE_CURRENT_SOURCE_DIR}/include - make include directory

# In CMake we have build(kompile target) requirement(If we mark something PRIVATE ), 
# usage(link target) requirement: (if we mark something INTERFACE) , 
# both usage and build requirements (if we mark something PUBLIC)
# Foo-s-d : -s static -d debug

add_library("${PROJECT_NAME}" "")
add_library(Foo::Foo ALIAS "${PROJECT_NAME}")

target_include_directories("${PROJECT_NAME}" PUBLIC
	"$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
	"$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>" #foo_export,h
	"$<INSTALL_INTERFACE:include>" #${CMAKE_INSTALL_PREFIX}/include

)

if(NOT BUILD_SHARED_LIBS)
		set(STATIC_POSTFIX "-s")
		set_target_properties("${PROSJECT_NAME}" PROPERTIES
		COMPILE_FLAGS -DFOO_STATIC_DEFINE)
endif()

#rename target Foo
set_target_properties("${PROJECT_NAME}" PROPERTIES
	DEBUG_POSTFIX "${STATIC_POSTFIX}-d"
	RELEASE_POSTFIX "${STATIC_POSTFIX}"
	MINSIZEREL_POSTFIX "${STATIC_POSTFIX}-mr"
	RELWITHDEBINFO_POSTFIX "${STATIC_POSTFIX}-rd"	
)

include("GenerateExportHeader")
generate_export_header("${PROJECT_NAME}")
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/foo_export.h" DESTINATION "include")



add_subdirectory("include")
add_subdirectory("src")
