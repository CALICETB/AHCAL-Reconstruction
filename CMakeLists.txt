########################################################
# cmake file for building
# @author Eldwan Brianne
CMAKE_MINIMUM_REQUIRED(VERSION 2.6 FATAL_ERROR)
########################################################

# project name
PROJECT( AHCALQtReco )

### PACKAGE VERSIONS ########################################################
SET( ${PROJECT_NAME}_VERSION_MAJOR 1 )
SET( ${PROJECT_NAME}_VERSION_MINOR 0 )
SET( ${PROJECT_NAME}_VERSION_PATCH 0 )

###############################
# Require C++11
INCLUDE( CheckCXXCompilerFlag )

CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)

IF( COMPILER_SUPPORTS_CXX11 )
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
ELSE()
	MESSAGE( SEND_ERROR "AHCALQtReco requires C++11 support. Please upgrade your compiler !" )
ENDIF()

OPTION( USE_MASTER "Whether to use master version of subpackages" OFF )

###############################
# versions of AHCAL Reconstruction subpackages
# don't forget to update when releasing 
# e.g. SET( QtReco_version "v01-00-00" )

IF( USE_MASTER )
	SET( xdrstream_version           "master" )
	SET( QtReco_version              "master" )
#	SET( QtReco-Client_version       "master" )
ELSE()
	SET( xdrstream_version           "v01-00-01" )
    SET( QtReco_version              "v01-00-00" )
#	SET( QtReco-Client_version       "v01-00-00" )
ENDIF()

# ----- download settings -----
SET( xdrstream_repository         "https://github.com/CALICETB/xdrstream.git" )
SET( QtReco_repository            "https://github.com/CALICETB/QtReco.git" )
#SET( QtReco-Client_repository     "https://github.com/CALICETB/QtReco-Client.git" )

# the external project in used are
# set gradually along the CMakeLists.txt
# When a project is added with ExternalProject_Add(...)
# these variables are set to the package name
# Mandatory packages are directly set

SET( xdrstream_package_depends "xdrstream" )
SET( QtReco_package_depends "" )
#SET( QtReco-Client_package_depends "" )

# ----- dependencies -----
FIND_PACKAGE( ILCUTIL COMPONENTS ILCSOFT_CMAKE_MODULES QUIET )

LIST( APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake )
INCLUDE( default_settings )

# ----------------------------------------------------------------------------

INCLUDE( ExternalProject )

# In order to pass semicolon-separated lists over the command line to all packages,
# we need to separate list elements with '%' instead of the standard cmake list separator ';'
# The list separator needs also to be redefined in the ExternalProject_Add calls by setting
# the variable LIST_SEPARATOR

FOREACH( _path ${CMAKE_PREFIX_PATH} )
    SET( CMAKE_PREFIX_PATH_FIXED ${CMAKE_PREFIX_PATH_FIXED}%${_path} )
ENDFOREACH()

FOREACH( _path ${CMAKE_MODULE_PATH} )
    SET( CMAKE_MODULE_PATH_FIXED ${CMAKE_MODULE_PATH_FIXED}%${_path} )
ENDFOREACH()

# ----- cmake arguments common to ALL cmake packages -------------------------
SET( common_cmake_args
     "-DCMAKE_PREFIX_PATH=${CMAKE_PREFIX_PATH_FIXED}"
     "-DCMAKE_MODULE_PATH=${CMAKE_MODULE_PATH_FIXED}"
     "-DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}"
)

# ----- package options -----------------------------------------------------------------------

OPTION( BUILD_DOCUMENTATION "Set to OFF to skip build/install Documentation" OFF )
MESSAGE( STATUS "BUILD_DOCUMENTATION: ${BUILD_DOCUMENTATION}" )

# ----------------------------------------------------------------------------

IF( MySQL_DIR )
	SET( common_cmake_args
		${common_cmake_args}
		-DMySQL_DIR=${MySQL_DIR}
	)
ENDIF()

# ----- xdrstream package -----
ExternalProject_Add( xdrstream
    GIT_REPOSITORY ${xdrstream_repository}
    GIT_TAG ${xdrstream_version}
    CMAKE_ARGS ${common_cmake_args}
    PREFIX xdrstream
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/xdrstream
    LIST_SEPARATOR %
)

# ---- Qt Reco ----

SET( QtReco_additional_cmake_args
	${QtReco_additional_cmake_args}
	-DBUILD_DOCUMENTATION=${BUILD_DOCUMENTATION}
)

# ---- Qt Reco Client ----

#SET( QtReco-Client_additional_cmake_args
#	${QtReco-Client_additional_cmake_args}
#	-DBUILD_DOCUMENTATION=${BUILD_DOCUMENTATION}
#)

# ----- QtReco package -----
ExternalProject_Add( QtReco
    DEPENDS ${xdrstream_package_depends}
    GIT_REPOSITORY ${QtReco_repository}
    GIT_TAG ${QtReco_version}
    CMAKE_ARGS ${common_cmake_args} ${QtReco_additional_cmake_args}
    PREFIX QtReco
    LIST_SEPARATOR %
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/QtReco
)

# ----- QtReco package -----
#ExternalProject_Add( QtReco-Client
#    DEPENDS ${xdrstream_package_depends}
#    GIT_REPOSITORY ${QtReco-Client_repository}
#    GIT_TAG ${QtReco-Client_version}
#    CMAKE_ARGS ${common_cmake_args} ${QtReco-Client_additional_cmake_args}
#    PREFIX QtReco-Client
#    LIST_SEPARATOR %
#    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/QtReco-Client
#)


# display some variables and write them to cache
DISPLAY_STD_VARIABLES()
