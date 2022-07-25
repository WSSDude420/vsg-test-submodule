cmake_minimum_required(VERSION 3.7)

project(MyFirstVsgApplication
        VERSION 0.0.0
        DESCRIPTION "Template of how to create a program using VulkanSceneGraph and CMake"
        LANGUAGES CXX
)

# build all examples into the bin directory
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)

# Change the default build type to Release
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel." FORCE)
endif(NOT CMAKE_BUILD_TYPE)

# find the vsg
if (VULKAN_SDK)
    set(ENV{VULKAN_SDK} ${VULKAN_SDK})
endif()

# Vulkan sample renderer specific section (nothing else directly depends on these)
set(DISABLE_EXCEPTIONS ON)
option(ENABLE_EXCEPTIONS_ON_MSVC "Build SPIRV-TOOLS with c++ exceptions enabled in MSVC"                  OFF)
option(ENABLE_RTTI               "Enables RTTI"                                                           OFF)
option(ENABLE_EXCEPTIONS         "Enables Exceptions"                                                     OFF)
option(BUILD_DOCUMENTATION       "Create and install the HTML based API documentation (requires Doxygen)" OFF)
OPTION(BUILD_SHARED_LIBS         "Build package with shared libraries."                                   OFF)
OPTION(BUILD_TESTS               "Build Tests"                                                            OFF)
# Vulkan-Headers (Vulkan Header files and API registry)
add_subdirectory(thirdparty/Vulkan-Headers)
# Vulkan Loader (Vulkan Loader)
add_subdirectory(thirdparty/Vulkan-Loader)
# SPIRV-Headers (external dependency for SPIRV-Tools)
option(SPIRV_HEADERS_SKIP_EXAMPLES "Skip building examples" ON )
option(SPIRV_HEADERS_SKIP_INSTALL  "Skip install"           ON )
add_subdirectory(thirdparty/SPIRV-Headers)
# SPIRV-Tools (external dependency for glslang)
option(SPIRV_SKIP_EXECUTABLES    "Skip building the executable and tests along with the library"                                                                  ON )
option(SPIRV_SKIP_TESTS          "Skip building tests along with the library"                                                                                     ON )
option(SKIP_SPIRV_TOOLS_INSTALL  "Skip installation"                                                                                                              ON )
option(ENABLE_EXCEPTIONS_ON_MSVC "Build SPIRV-TOOLS with c++ exceptions enabled in MSVC"                                                                          OFF)
option(SPIRV_TOOLS_BUILD_STATIC  "Build SPIRV-TOOLS-static target. SPIRV-TOOLS will alias to SPIRV-TOOLS-static or SPIRV-TOOLS-shared based on BUILD_SHARED_LIBS" ON )
option(SPIRV_WERROR              "Enable error on warning"                                                                                                        OFF)
add_subdirectory(thirdparty/SPIRV-Tools)
# glslang (Khronos-reference front end for GLSL/ESSL, partial front end for HLSL, and a SPIR-V generator)
option(SKIP_GLSLANG_INSTALL    "Skip installation"                                                                                     ON )
option(ENABLE_SPVREMAPPER      "Enables building of SPVRemapper"                                                                       OFF)
option(ENABLE_GLSLANG_BINARIES "Builds glslangValidator and spirv-remap"                                                               OFF)
option(ENABLE_GLSLANG_JS       "If using Emscripten, build glslang.js. Otherwise, builds a sample executable for binary-size testing." OFF)
option(ENABLE_OPT              "Enables spirv-opt capability if present"                                                               ON )
option(ENABLE_PCH              "Enables Precompiled header"                                                                            ON )
option(ENABLE_CTEST            "Enables testing"                                                                                       OFF)
option(ENABLE_HLSL             "Enables HLSL input support"                                                                            OFF)
add_subdirectory(thirdparty/glslang)
# Open Asset Import Library (The official Open-Asset-Importer-Library Repository. Loads 40+ 3D-file-formats into one unified and clean data structure.)
OPTION(ASSIMP_BUILD_FRAMEWORK      "Build package as Mac OS X Framework bundle."                                 OFF)
OPTION(ASSIMP_DOUBLE_PRECISION     "Set to ON to enable double precision processing"                             OFF)
OPTION(ASSIMP_OPT_BUILD_PACKAGES   "Set to ON to generate CPack configuration files and packaging targets"       OFF)
OPTION(ASSIMP_ANDROID_JNIIOSYSTEM  "Android JNI IOSystem support is active"                                      OFF)
OPTION(ASSIMP_NO_EXPORT            "Disable Assimp's export functionality."                                      ON )
OPTION(ASSIMP_BUILD_ZLIB           "Build your own zlib"                                                         OFF)
OPTION(ASSIMP_BUILD_ASSIMP_TOOLS   "If the supplementary tools for Assimp are built in addition to the library." OFF)
OPTION(ASSIMP_BUILD_SAMPLES        "If the official samples are built as well (needs Glut)."                     OFF)
OPTION(ASSIMP_BUILD_TESTS          "If the test suite for Assimp is built in addition to the library."           OFF)
OPTION(ASSIMP_COVERALLS            "Enable this to measure test coverage."                                       OFF)
OPTION(ASSIMP_INSTALL              "Disable this if you want to use assimp as a submodule."                      OFF)
OPTION(ASSIMP_WARNINGS_AS_ERRORS   "Treat all warnings as errors."                                               OFF)
OPTION(ASSIMP_ASAN                 "Enable AddressSanitizer."                                                    OFF)
OPTION(ASSIMP_UBSAN                "Enable Undefined Behavior sanitizer."                                        OFF)
OPTION(ASSIMP_BUILD_DOCS           "Build documentation using Doxygen."                                          OFF)
OPTION(ASSIMP_INJECT_DEBUG_POSTFIX "Inject debug postfix in .a/.so/.dll lib names"                               ON )
OPTION(ASSIMP_IGNORE_GIT_HASH      "Don't call git to get the hash."                                             OFF)
OPTION(ASSIMP_BUILD_DRACO          "If the Draco libraries are to be built. Primarily for glTF"                  ON )
add_subdirectory(thirdparty/assimp)

add_subdirectory(thirdparty/vsg)
add_subdirectory(thirdparty/vsgXchange)

# find the optional vsgXchange that can be used for reading and range of image and 3d model formats and shader compilation
find_package(vsgXchange QUIET)

# set the use of C++17 globally as all examples require it
set(CMAKE_CXX_STANDARD 17)

# add clobber build target to clear all the non git registered files/directories
add_custom_target(clobber-my-first-vsg-application
    COMMAND git clean -d -f -x
)

set(SOURCES
    src/main.cpp
)

add_executable(myfirstvsgapplication ${SOURCES})

target_link_libraries(myfirstvsgapplication vsg)

if (TARGET vsgXchange)
    target_compile_definitions(myfirstvsgapplication PRIVATE vsgXchange_FOUND)
    target_link_libraries(myfirstvsgapplication vsgXchange)
endif()

install(TARGETS myfirstvsgapplication
        RUNTIME DESTINATION bin
)

