cmake_minimum_required(VERSION 3.4.3 FATAL_ERROR)

PROJECT( DDParsers LANGUAGES NONE)

#################
# Setup version #
#################

SET( DD4hep_VERSION_MAJOR 1 )
SET( DD4hep_VERSION_MINOR 0 )
SET( DD4hep_VERSION_PATCH 0 )

#######################
# Basic project setup #
#######################

set ( CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake )

IF(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  SET( CMAKE_INSTALL_PREFIX ${CMAKE_SOURCE_DIR} CACHE PATH
       "install prefix path  - overwrite with -D CMAKE_INSTALL_PREFIX = ..."
       FORCE )
 MESSAGE(STATUS "CMAKE_INSTALL_PREFIX is ${CMAKE_INSTALL_PREFIX} - overwrite with -D CMAKE_INSTALL_PREFIX" )
ENDIF(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)

#############################################################
# Enable CXX as project language to perform compiler checks #
#############################################################

ENABLE_LANGUAGE(CXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 11 CACHE STRING "C++ standard used for compiling")
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

###############################
# Define DD4hep build options #
###############################

option(DD4HEP_USE_XERCESC "Enable 'Detector Builders' based on XercesC"   OFF)
option(DD4HEP_USE_GEANT4  "Enable the simulation part based on Geant4"    OFF)
option(DD4HEP_USE_GEAR    "Build gear wrapper for backward compatibility" OFF)
option(DD4HEP_USE_LCIO    "Build lcio extensions"     OFF)
option(BUILD_TESTING      "Enable and build tests"    ON)
option(CMAKE_MACOSX_RPATH "Build with rpath on macos" ON)

#####################
# Configure version #
#####################

include ( DD4hepBuild )
dd4hep_set_version(DD4hep
        MAJOR ${DD4hep_VERSION_MAJOR}
        MINOR ${DD4hep_VERSION_MINOR}
        PATCH ${DD4hep_VERSION_PATCH} )

configure_file (
        "${PROJECT_SOURCE_DIR}/cmake/Version.h.in"
        "${PROJECT_SOURCE_DIR}/DDParsers/include/Parsers/Version.h"
)

dd4hep_configure_output( OUTPUT "${PROJECT_BINARY_DIR}" INSTALL "${CMAKE_INSTALL_PREFIX}" )

########################
# Resolve dependencies #
########################

# Configure BOOST
find_package( Boost 1.49 REQUIRED )
add_definitions( -DBOOST_SPIRIT_USE_PHOENIX_V3 )

######################
# Set compiler flags #
######################

dd4hep_set_compiler_flags()

####################
# Include packages #
####################


include(DD4hep)
include(DD4hepMacros) 

add_subdirectory(DDParsers)

#######################
# Treatment for Apple #
#######################

if( APPLE )
  set( USE_DYLD TRUE)
  set( CMAKE_MACOSX_RPATH TRUE)
endif()

#########################
# Configure and install #
#########################

install(DIRECTORY cmake
  DESTINATION ${CMAKE_INSTALL_PREFIX}
  FILES_MATCHING PATTERN "*.cmake" EXCLUDE
  )

###############################
# Dispaly final configuration #
###############################

display_std_variables()

######################################################
# generate and install following configuration files #
######################################################

dd4hep_generate_package_configuration_files( DDParsersConfig.cmake )
