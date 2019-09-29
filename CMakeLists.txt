cmake_minimum_required(VERSION 3.12.4)
get_directory_property(hasParent PARENT_DIRECTORY)
if(NOT hasParent)
	option(unittests "unittests" OFF)
	get_filename_component(_PARENT_DIR ${CMAKE_CURRENT_SOURCE_DIR} DIRECTORY)
	set_property(GLOBAL PROPERTY GLOBAL_FETCHDEPS_BASE ${_PARENT_DIR}/al2o3 )
	include(FetchContent)
	FetchContent_Declare( al2o3 GIT_REPOSITORY https://github.com/DeanoC/al2o3 GIT_TAG master )
	FetchContent_GetProperties(al2o3)
	if(NOT al2o3_POPULATED)
		FetchContent_Populate(al2o3)
		add_subdirectory(${al2o3_SOURCE_DIR} ${al2o3_BINARY_DIR})
	endif()
	INIT_AL2O3(${CMAKE_CURRENT_SOURCE_DIR})
endif ()

SET_MIN_VERSIONS()
set(LibName gfx_glslang)

set(Interface
        ../glslang/Public/ShaderLang.h
        )

set(Src
#        glslang/MachineIndependent/glslang.y
        glslang/MachineIndependent/glslang_tab.cpp
        glslang/MachineIndependent/attribute.cpp
        glslang/MachineIndependent/Constant.cpp
        glslang/MachineIndependent/iomapper.cpp
        glslang/MachineIndependent/InfoSink.cpp
        glslang/MachineIndependent/Initialize.cpp
        glslang/MachineIndependent/IntermTraverse.cpp
        glslang/MachineIndependent/Intermediate.cpp
        glslang/MachineIndependent/ParseContextBase.cpp
        glslang/MachineIndependent/ParseHelper.cpp
        glslang/MachineIndependent/PoolAlloc.cpp
        glslang/MachineIndependent/RemoveTree.cpp
        glslang/MachineIndependent/Scan.cpp
        glslang/MachineIndependent/ShaderLang.cpp
        glslang/MachineIndependent/SymbolTable.cpp
        glslang/MachineIndependent/Versions.cpp
        glslang/MachineIndependent/intermOut.cpp
        glslang/MachineIndependent/limits.cpp
        glslang/MachineIndependent/linkValidate.cpp
        glslang/MachineIndependent/parseConst.cpp
        glslang/MachineIndependent/reflection.cpp
        glslang/MachineIndependent/preprocessor/Pp.cpp
        glslang/MachineIndependent/preprocessor/PpAtom.cpp
        glslang/MachineIndependent/preprocessor/PpContext.cpp
        glslang/MachineIndependent/preprocessor/PpScanner.cpp
        glslang/MachineIndependent/preprocessor/PpTokens.cpp
        glslang/MachineIndependent/propagateNoContraction.cpp
        glslang/GenericCodeGen/CodeGen.cpp
        glslang/GenericCodeGen/Link.cpp
        SPIRV/GlslangToSpv.cpp
        SPIRV/InReadableOrder.cpp
        SPIRV/Logger.cpp
        SPIRV/SpvBuilder.cpp
        SPIRV/SpvPostProcess.cpp
        SPIRV/doc.cpp
        SPIRV/SpvTools.cpp
        SPIRV/disassemble.cpp
        SPIRV/SPVRemapper.cpp
        SPIRV/doc.cpp
        hlsl/hlslAttributes.cpp
        hlsl/hlslParseHelper.cpp
        hlsl/hlslScanContext.cpp
        hlsl/hlslOpMap.cpp
        hlsl/hlslTokenStream.cpp
        hlsl/hlslGrammar.cpp
        hlsl/hlslParseables.cpp

        glslang/Include/arrays.h
        glslang/Include/BaseTypes.h
        glslang/Include/Common.h
        glslang/Include/ConstantUnion.h
        glslang/Include/InfoSink.h
        glslang/Include/InitializeGlobals.h
        glslang/Include/intermediate.h
        glslang/Include/PoolAlloc.h
        glslang/Include/ResourceLimits.h
        glslang/Include/revision.h
        glslang/Include/ShHandle.h
        glslang/Include/Types.h
        glslang/MachineIndependent/attribute.h
        glslang/MachineIndependent/glslang_tab.cpp.h
        glslang/MachineIndependent/gl_types.h
        glslang/MachineIndependent/Initialize.h
        glslang/MachineIndependent/iomapper.h
        glslang/MachineIndependent/LiveTraverser.h
        glslang/MachineIndependent/localintermediate.h
        glslang/MachineIndependent/ParseHelper.h
        glslang/MachineIndependent/reflection.h
        glslang/MachineIndependent/RemoveTree.h
        glslang/MachineIndependent/Scan.h
        glslang/MachineIndependent/ScanContext.h
        glslang/MachineIndependent/SymbolTable.h
        glslang/MachineIndependent/Versions.h
        glslang/MachineIndependent/parseVersions.h
        glslang/MachineIndependent/propagateNoContraction.h
        glslang/MachineIndependent/preprocessor/PpContext.h
        glslang/MachineIndependent/preprocessor/PpTokens.h
        glslang/OSDependent/osinclude.h
        SPIRV/bitutils.h
        SPIRV/spirv.hpp
        SPIRV/GLSL.std.450.h
        SPIRV/GLSL.ext.EXT.h
        SPIRV/GLSL.ext.KHR.h
        SPIRV/GlslangToSpv.h
        SPIRV/hex_float.h
        SPIRV/Logger.h
        SPIRV/SpvBuilder.h
        SPIRV/spvIR.h
        SPIRV/doc.h
        SPIRV/SpvTools.h
        SPIRV/disassemble.h
        SPIRV/SPVRemapper.h
        SPIRV/doc.h
        SPIRV/GLSL.ext.AMD.h
        SPIRV/GLSL.ext.NV.h
        hlsl/hlslAttributes.h
        hlsl/hlslParseHelper.h
        hlsl/hlslTokens.h
        hlsl/hlslScanContext.h
        hlsl/hlslOpMap.h
        hlsl/hlslTokenStream.h
        hlsl/hlslGrammar.h
        hlsl/hlslParseables.h
        OGLCompilersDLL/InitializeDll.cpp

        )


if(WIN32)
    list(APPEND Src glslang/OSDependent/Windows/ossource.cpp)
    add_definitions(-DGLSLANG_OSINCLUDE_WIN32)
else()
    list(APPEND Src glslang/OSDependent/Unix/ossource.cpp)
    add_definitions(-DGLSLANG_OSINCLUDE_UNIX)
endif()

set(Deps
        gfx_spirvtools)

ADD_LIB(${LibName} "${Interface}" "${Src}" "${Deps}")
target_include_directories(${LibName} PUBLIC src) # public for shaderc

target_compile_definitions(${LibName}
        PUBLIC
        AMD_EXTENSIONS
        NV_EXTENSIONS
        ENABLE_HLSL
        ENABLE_OPT=1)

