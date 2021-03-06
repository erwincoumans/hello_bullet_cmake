cmake_minimum_required(VERSION 3.0)

PROJECT(test_bullet)

if (DEFINED Bullet_DIR)
   message("Finding local Bullet")
   Find_Package(Bullet CONFIG)
   SET(BULLET_INCLUDE_DIR ${Bullet_DIR}/${BULLET_ROOT_DIR}/${BULLET_INCLUDE_DIR})
   SET(BLA ${Bullet_DIR}/${BULLET_ROOT_DIR}/${BULLET_LIBRARY_DIRS})
else()
   message("Finding system installed Bullet")
   Find_Package(Bullet)
endif()


# show all cmake variables, for debugging
#get_cmake_property(_variableNames VARIABLES)
#list (SORT _variableNames)
#foreach (_variableName ${_variableNames})
#   message(STATUS "${_variableName}=${${_variableName}}")
#endforeach()

#MESSAGE("BULLET_ROOT_DIR:")
#MESSAGE(${BULLET_ROOT_DIR})

#MESSAGE("Bullet_DIR:")
#MESSAGE(${Bullet_DIR})

MESSAGE("BULLET_INCLUDE_DIR:")
MESSAGE(${BULLET_INCLUDE_DIR})
MESSAGE("Bullet Libraries:")
MESSAGE(${BULLET_LIBRARIES})
MESSAGE("BULLET_LIBRARY_DIRS=",${BULLET_LIBRARY_DIRS})


OPTION(USE_MSVC_INCREMENTAL_LINKING "Use MSVC Incremental Linking" OFF)
OPTION(USE_MSVC_RUNTIME_LIBRARY_DLL "Use MSVC Runtime Library DLL (/MD or /MDd)" OFF)
IF(MSVC)
	IF (NOT USE_MSVC_INCREMENTAL_LINKING)
		#MESSAGE("MSVC_INCREMENTAL_DEFAULT"+${MSVC_INCREMENTAL_DEFAULT})
		SET( MSVC_INCREMENTAL_YES_FLAG "/INCREMENTAL:NO")
		STRING(REPLACE "INCREMENTAL:YES" "INCREMENTAL:NO" replacementFlags ${CMAKE_EXE_LINKER_FLAGS_DEBUG})
		SET(CMAKE_EXE_LINKER_FLAGS_DEBUG "/INCREMENTAL:NO ${replacementFlags}" )
		MESSAGE("CMAKE_EXE_LINKER_FLAGS_DEBUG=${CMAKE_EXE_LINKER_FLAGS_DEBUG}")
		STRING(REPLACE "INCREMENTAL:YES" "INCREMENTAL:NO" replacementFlags2 ${CMAKE_EXE_LINKER_FLAGS})
		SET(CMAKE_EXE_LINKER_FLAGS ${replacementFlag2})
		STRING(REPLACE "INCREMENTAL:YES" "" replacementFlags3 "${CMAKE_EXTRA_LINK_FLAGS}")
		SET(CMAKE_EXTRA_LINK_FLAGS ${replacementFlag3})
		STRING(REPLACE "INCREMENTAL:YES" "INCREMENTAL:NO" replacementFlags3 "${CMAKE_EXE_LINKER_FLAGS_RELWITHDEBINFO}")
		SET(CMAKE_EXE_LINKER_FLAGS_RELWITHDEBINFO ${replacementFlags3})
		SET(CMAKE_EXE_LINKER_FLAGS_RELWITHDEBINFO "/INCREMENTAL:NO ${replacementFlags3}" )
	ENDIF (NOT USE_MSVC_INCREMENTAL_LINKING)
	OPTION(USE_MSVC_RUNTIME_LIBRARY_DLL "Use MSVC Runtime Library DLL (/MD or /MDd)" OFF)
	IF (NOT USE_MSVC_RUNTIME_LIBRARY_DLL)
		#We statically link to reduce dependencies
		FOREACH(flag_var CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO CMAKE_C_FLAGS CMAKE_C_FLAGS_DEBUG CMAKE_C_FLAGS_RELEASE CMAKE_C_FLAGS_MINSIZEREL CMAKE_C_FLAGS_RELWITHDEBINFO )
			IF(${flag_var} MATCHES "/MD")
				STRING(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
			ENDIF(${flag_var} MATCHES "/MD")
			IF(${flag_var} MATCHES "/MDd")
				STRING(REGEX REPLACE "/MDd" "/MTd" ${flag_var} "${${flag_var}}")
			ENDIF(${flag_var} MATCHES "/MDd")
		ENDFOREACH(flag_var)
	ENDIF (NOT USE_MSVC_RUNTIME_LIBRARY_DLL)

	IF (CMAKE_CL_64)
	  ADD_DEFINITIONS(-D_WIN64)
	ELSE()
	  OPTION(USE_MSVC_SSE "Use MSVC /arch:sse option"	OFF)
	  option(USE_MSVC_SSE2 "Compile your program with SSE2 instructions" ON)
	  IF (USE_MSVC_SSE)
		  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /arch:SSE")
	  ENDIF()
	  IF (USE_MSVC_SSE2)
		  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /arch:SSE2")
	  ENDIF()
	ENDIF()
	option(USE_MSVC_AVX "Compile your program with AVX instructions"  OFF)
	IF(USE_MSVC_AVX)
		add_definitions(/arch:AVX)
	ENDIF()
	OPTION(USE_MSVC_FAST_FLOATINGPOINT "Use MSVC /fp:fast option"	ON)
	IF (USE_MSVC_FAST_FLOATINGPOINT)
		  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /fp:fast")
	ENDIF()
	OPTION(USE_MSVC_STRING_POOLING "Use MSVC /GF string pooling option"	ON)
	IF (USE_MSVC_STRING_POOLING)
		SET(CMAKE_C_FLAGS "/GF ${CMAKE_C_FLAGS}")
		SET(CMAKE_CXX_FLAGS "/GF ${CMAKE_CXX_FLAGS}")
	ENDIF()
	OPTION(USE_MSVC_FUNCTION_LEVEL_LINKING "Use MSVC /Gy function level linking option"	ON)
	IF(USE_MSVC_FUNCTION_LEVEL_LINKING)
		SET(CMAKE_C_FLAGS "/Gy ${CMAKE_C_FLAGS}")
		SET(CMAKE_CXX_FLAGS "/Gy ${CMAKE_CXX_FLAGS}")
		set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} /OPT:REF")
		set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /OPT:REF")
	ENDIF(USE_MSVC_FUNCTION_LEVEL_LINKING)
	OPTION(USE_MSVC_EXEPTIONS "Use MSVC C++ exceptions option"	OFF)
	OPTION(USE_MSVC_COMDAT_FOLDING "Use MSVC /OPT:ICF COMDAT folding option"	ON)
	IF(USE_MSVC_COMDAT_FOLDING)
		set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} /OPT:ICF")
		set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /OPT:ICF")
	ENDIF()
	OPTION(USE_MSVC_DISABLE_RTTI "Use MSVC /GR- disabled RTTI flags option"	ON)
	IF(USE_MSVC_DISABLE_RTTI)
	  STRING(REGEX REPLACE "/GR" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS}) # Disable RTTI
		SET(CMAKE_C_FLAGS "/GR- ${CMAKE_C_FLAGS}")
		SET(CMAKE_CXX_FLAGS "/GR- ${CMAKE_CXX_FLAGS}")
	ENDIF(USE_MSVC_DISABLE_RTTI)
	SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4244 /wd4267")
ENDIF(MSVC)

add_executable(hello_bullet_cmake test.cpp)
target_include_directories(hello_bullet_cmake PUBLIC ${BULLET_INCLUDE_DIR})
target_compile_definitions(hello_bullet_cmake PUBLIC ${BULLET_DEFINITIONS})
target_link_directories(hello_bullet_cmake PUBLIC ${Bullet_DIR}/${BULLET_ROOT_DIR}/${BULLET_LIBRARY_DIRS})
target_link_libraries(hello_bullet_cmake PUBLIC BulletDynamics BulletCollision LinearMath)

