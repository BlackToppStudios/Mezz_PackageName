# © Copyright 2010 - 2018 BlackTopp Studios Inc.
# This file is part of The Mezzanine Engine.
#
#    The Mezzanine Engine is free software: you can redistribute it and/or modify
#    it under the terms of the GNU General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    The Mezzanine Engine is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU General Public License for more details.
#
#    You should have received a copy of the GNU General Public License
#    along with The Mezzanine Engine.  If not, see <http://www.gnu.org/licenses/>.
#
#   The original authors have included a copy of the license specified above in the
#   'Docs' folder. See 'gpl.txt'
#
#   We welcome the use of the Mezzanine engine to anyone, including companies who wish to
#   Build professional software and charge for their product.
#
#   However there are some practical restrictions, so if your project involves
#   any of the following you should contact us and we will try to work something
#   out:
#    - DRM or Copy Protection of any kind(except Copyrights)
#    - Software Patents You Do Not Wish to Freely License
#    - Any Kind of Linking to Non-GPL licensed Works
#    - Are Currently In Violation of Another Copyright Holder's GPL License
#    - If You want to change our code and not add a few hundred MB of stuff to
#        your distribution
#
#   These and other limitations could cause serious legal problems if you ignore
#   them, so it is best to simply contact us or the Free Software Foundation, if
#   you have any questions.
#
#   Joseph Toppi - toppij@gmail.com
#   John Blackwood - makoenergy02@gmail.com

########################################################################################################################
cmake_minimum_required(VERSION 3.0)
project(PackageName)
message(STATUS "${PROJECT_NAME} - Starting Configuration.")

############################################################################|###########################################
# Download, Verify and Setup the Jagati
if(NOT JAGATI_File)
    set(JAGATI_File "${${PROJECT_NAME}_BINARY_DIR}/Jagati.cmake" CACHE FILEPATH
        "The file to load the Jagati from and potentially to download it to.")
endif(NOT JAGATI_File)
if(NOT JAGATI_Download)
    option(JAGATI_Download "Should the Jagati be downloaded automatically" ON)
endif(NOT JAGATI_Download)
if(JAGATI_Download)
    set(JAGATI_Checksum "92f7a77ef66a65969fbe0bb4b1c575018a500b66ee756f7bbcb\
f25365195e01ca357455e7adb2648f40f1cd1b0776ee83d246d3e3fa6bd6e4baad71369dc5883"
        CACHE STRING "Check that when the Jagati is downloaded the right one is used (for consistency and security).")
    set(JAGATI_Url "https://raw.githubusercontent.com/BlackToppStudios/Jagati/0.27.2/Jagati.cmake"
        CACHE STRING "Where to download the Jagati from.")
    file(DOWNLOAD "${JAGATI_Url}" "${JAGATI_File}" EXPECTED_HASH SHA512=${JAGATI_Checksum})
endif(JAGATI_Download)
include("${JAGATI_File}")

########################################################################################################################
# The rest of the Jagati setup

StandardJagatiSetup()
IncludeJagatiPackage("StaticFoundation")
IncludeJagatiPackage("Test")

########################################################################################################################
# Source files
message(STATUS "Determining Source Files.")

AddHeaderFile("SampleHeader.h")
ShowList("Header Files:" "\t" "${PackageNameFiles}")

AddSourceFile("SampleSource.cpp")
ShowList("Source Files:" "\t" "${PackageNameSourceFiles}")

AddJagatiDoxInput("Dox.h")
ShowList("Doxygen Input Files:" "\t" "${PackageNameDoxFiles}")

########################################################################################################################
# Basic Targets and Test declaration
AddJagatiLibrary()
CreateCoverageTarget(${PackageNameLib} "${PackageNameSourceFiles}")

# Uncomment this if you want to include files in lib/ in your source. Some Libraries can get away
# with this simple scheme, some need much more sophistication.
#target_include_directories(${PackageNameLib} PRIVATE "${PackageNameLibDir}")

AddTestFile("SampleTest.h")
EmitTestCode()
AddTestTarget()

SetCodeCoverage()
AddIDEVisibility("Jenkinsfile")
message(STATUS "${PROJECT_NAME} - Ending Configuration.")
