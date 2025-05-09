
remove_definitions(-D_WIN32_WINNT=0x502)
add_definitions(-D_WIN32_WINNT=0x600)

add_definitions(-DENTRY_PREFIX=avifil32_)
spec2def(avifil32.dll avifil32.spec ADD_IMPORTLIB)
add_rpcproxy_files(avifil32.idl)

list(APPEND SOURCE
    acmstream.c
    api.c
    avifile.c
    editstream.c
    extrachunk.c
    factory.c
    getframe.c
    icmstream.c
    tmpfile.c
    wavfile.c)

list(APPEND PCH_SKIP_SOURCE
    guid.c
    ${CMAKE_CURRENT_BINARY_DIR}/avifil32_p.c)

add_library(avifil32 MODULE
    ${SOURCE}
    ${PCH_SKIP_SOURCE}
    rsrc.rc
    ${CMAKE_CURRENT_BINARY_DIR}/proxy.dlldata.c
    ${CMAKE_CURRENT_BINARY_DIR}/avifil32.def)

if(MSVC)
    # Disable warning C4146: unary minus operator applied to unsigned type, result still unsigned
    target_compile_options(avifil32 PRIVATE /wd4146)
endif()

set_module_type(avifil32 win32dll)
target_link_libraries(avifil32 wine ${PSEH_LIB})
add_importlibs(avifil32 msacm32 msvfw32 winmm ole32 user32 advapi32 rpcrt4 msvcrt kernel32 ntdll)
add_pch(avifil32 precomp.h "${PCH_SKIP_SOURCE}")
add_cd_file(TARGET avifil32 DESTINATION reactos/system32 FOR all)
set_wine_module_FIXME(avifil32) # CORE-5743: No ARRAY_SIZE nor CONST_VTABLE
