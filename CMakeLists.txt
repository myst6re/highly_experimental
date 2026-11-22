cmake_minimum_required(VERSION 3.25)

option(USE_STATIC_MSVC_RUNTIME "Use static MSVC runtime (/MT)" ON)

if(USE_STATIC_MSVC_RUNTIME AND NOT DEFINED CMAKE_MSVC_RUNTIME_LIBRARY)
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
endif()

project(highly_experimental LANGUAGES C)

option(HE_SHARED_OBJECT "Use Shared Object" OFF)

if(HE_SHARED_OBJECT)
    add_library(highly_experimental OBJECT)
else()
    add_library(highly_experimental)
endif()

target_sources(highly_experimental
    PRIVATE
        src/bios.c
        src/iop.c
        src/ioptimer.c
        src/psx.c
        src/r3000.c
        src/r3000asm.c
        src/r3000dis.c
        src/spu.c
        src/spucore.c
        src/vfs.c
)

target_include_directories(highly_experimental
    PUBLIC
        "${CMAKE_CURRENT_SOURCE_DIR}/include"
)

target_compile_definitions(highly_experimental
    PRIVATE
        NDEBUG
        _LIB
        EMU_COMPILE
        EMU_LITTLE_ENDIAN
)

# WIN32 selects the __fastcall calling convention in emuconfig.h (MSVC only):
# it must be defined the same way here and in every target including the headers.
if(WIN32)
    target_compile_definitions(highly_experimental
        PRIVATE
            _WIN32_WINNT=0x501
            WIN32
            _CRT_SECURE_NO_WARNINGS
            UNICODE
            _UNICODE
    )
endif()

if(MSVC)
    target_compile_options(highly_experimental
        PRIVATE
            /W3
    )
endif()

