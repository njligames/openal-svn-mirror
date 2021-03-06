# cmake project file by Prakash Punnoor
CMAKE_MINIMUM_REQUIRED(VERSION 2.4)

SET(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/admin/CMakeModules")
PROJECT(OpenAL C)
SET(PACKAGE "openal")
SET(PACKAGE_TARNAME "openal")
SET(PACKAGE_NAME "openal")
SET(PACKAGE_MAJOR_VERSION "1")
SET(PACKAGE_MINOR_VERSION "1")
SET(PACKAGE_BUILD_VERSION "0")
SET(PACKAGE_VERSION "${PACKAGE_MAJOR_VERSION}.${PACKAGE_MINOR_VERSION}.${PACKAGE_BUILD_VERSION}")

SET(SO_MAJOR_VERSION "1")
SET(SO_MINOR_VERSION "0")
SET(SO_BUILD_VERSION "0")
SET(SO_VERSION "${SO_MAJOR_VERSION}.${SO_MINOR_VERSION}.${SO_BUILD_VERSION}")

SET(PACKAGE_STRING "openal")
SET(PACKAGE_BUGREPORT "openal-devel@opensource.creative.com")
SET(OPERATING_SYSTEM "${CMAKE_SYSTEM_NAME}")

INCLUDE(${CMAKE_ROOT}/Modules/CheckCSourceCompiles.cmake)
INCLUDE(${CMAKE_ROOT}/Modules/CheckIncludeFile.cmake)
INCLUDE(${CMAKE_ROOT}/Modules/CheckLibraryExists.cmake)
INCLUDE(${CMAKE_ROOT}/Modules/TestBigEndian.cmake)
INCLUDE(${CMAKE_MODULE_PATH}/ConfigHelper.cmake)
INCLUDE(${CMAKE_MODULE_PATH}/CompilerVisibility.cmake)
INCLUDE(${CMAKE_MODULE_PATH}/CompilerAttribute.cmake)
INCLUDE(${CMAKE_MODULE_PATH}/CompilerFlagsSet.cmake)
INCLUDE(${CMAKE_MODULE_PATH}/CompilerSIMD.cmake)
INCLUDE(${CMAKE_MODULE_PATH}/DetectCompiler.cmake)


ADD_DEFINE("PACKAGE_VERSION \"${PACKAGE_VERSION}\"")

# We need ISO C99 features (e.g. snprintf) and POSIX IEEE Std 1003.1b-1993
# features (e.g. struct timespec, used in <alsa/asoundlib.h>)
# struct timeval used in esd and solaris backend is actually defined in POSIX 2001
ADD_DEFINE(_ISOC99_SOURCE)
ADD_DEFINE("_POSIX_C_SOURCE 200112L")

# Without __NO_CTYPE tolower and friends are macros which introduce a GLIBC 2.3
# dependency.
ADD_DEFINE(__NO_CTYPE)

ADD_DEFINE("SIZEOF_VOID_P ${CMAKE_SIZEOF_VOID_P}")


INCLUDE_DIRECTORIES(${OpenAL_SOURCE_DIR}/common/include)
INCLUDE_DIRECTORIES(${OpenAL_SOURCE_DIR}/include)
INCLUDE_DIRECTORIES(${OpenAL_SOURCE_DIR}/src)
INCLUDE_DIRECTORIES(${OpenAL_SOURCE_DIR}/src/arch/i386)


SET(AL_OBJS              src/al_bpool.c
                         src/al_buffer.c
                         src/config/al_config.c
                         src/al_distance.c
                         src/al_dlopen.c
                         src/al_error.c
                         src/al_ext.c
                         src/al_filter.c
                         src/al_listen.c
                         src/al_main.c
                         src/al_mixer.c
                         src/al_mixfunc.c
                         src/al_mixmanager.c
                         src/al_mspool.c
                         src/al_mutexlib.c
                         src/al_queue.c
                         src/config/al_rctree.c
                         src/config/al_rcvar.c
                         src/al_source.c
                         src/al_spool.c
                         src/al_state.c
                         src/al_threadlib.c
                         src/al_vector.c
                         src/al_matrix.c
                         src/mixaudio16.c
                         src/backends/alc_backend.c)

SET(AL_DEBUG_OBJS        src/al_debug.c)

SET(ALC_OBJS             src/alc/alc_context.c
                         src/alc/alc_speaker.c
                         src/alc/alc_error.c
                         src/alc/alc_device.c)

SET(AC_OBJS              src/audioconvert/ac_misc.c
                         src/audioconvert/ac_freq.c
                         src/audioconvert/ac_bits.c
                         src/audioconvert/ac_channels.c
                         src/audioconvert/ac_helper.c
                         src/audioconvert/ac_adpcm.c
                         src/audioconvert/ac_endian.c)

SET(EXT_OBJS             src/extensions/al_ext_loki.c)

SET(MP3_OBJS             src/extensions/al_ext_mp3.c)
SET(VORBIS_OBJS          src/extensions/al_ext_vorbis.c)

SET(DARWIN_OBJS          src/backends/alc_backend_darwin.c)
SET(DMEDIA_OBJS          src/backends/alc_backend_irix.c)
SET(OSS_OBJS             src/backends/alc_backend_oss.c)
SET(SOLARIS_OBJS         src/backends/alc_backend_solaris.c)
SET(WINDOWS_OBJS         src/backends/alc_backend_windows.c)
SET(ALSA_OBJS            src/backends/alc_backend_alsa.c)
SET(ESD_OBJS             src/backends/alc_backend_esd.c)
SET(NULL_OBJS            src/backends/alc_backend_null.c)
SET(SDL_OBJS             src/backends/alc_backend_sdl.c)
SET(WAVEOUT_OBJS         src/backends/alc_backend_wave.c)

SET(x86_OBJS             src/arch/i386/x86_floatmul.c
                         src/arch/i386/x86_cpu_caps_prk.c)
SET(x86_MMX_OBJS         src/arch/i386/mixaudio16_mmx_prk.c
                         src/arch/i386/x86_floatmul_mmx.c)
SET(x86_SSE2_OBJS        src/arch/i386/mixaudio16_sse2_prk.c
                         src/arch/i386/x86_floatmul_sse2.c)
SET(x86_NASM_OBJS        src/arch/i386/x86_cpu_caps_detect_prk.nasm)
SET(x86_64_LINUX_NASM_OBJS src/arch/i386/x86_64_linux_cpu_caps_detect_prk.yasm)
SET(x86_MMX_NASM_OBJS    src/arch/i386/memcpy_mmx_prk.nasm)

SET(PORTABLE_OBJS        src/arch/portable/floatmul.c)


SET(ADD_CFLAGS "")
SET(ADD_LIBS "")
SET(ADD_LDFLAGS "")
SET(ADD_WFLAGS "")

IF(CMAKE_UNAME)
  EXEC_PROGRAM(uname ARGS -m OUTPUT_VARIABLE CMAKE_SYSTEM_MACHINE)
ELSE(CMAKE_UNAME)
  MESSAGE("Could not detect machine type")
  IF(CMAKE_SYSTEM MATCHES "Windows")
    MESSAGE(STATUS "Assuming i386 machine")
    SET(CMAKE_SYSTEM_MACHINE i386)
  ENDIF(CMAKE_SYSTEM MATCHES "Windows")
ENDIF(CMAKE_UNAME)

DETECT_COMPILER()

IF(CMAKE_SYSTEM MATCHES CYGWIN OR CMAKE_SYSTEM MATCHES MINGW)
  SET(CMAKE_SYSTEM Windows)
  IF(CMAKE_SYSTEM MATCHES CYGWIN)
    SET(CYGWIN ON)
  ELSE(CMAKE_SYSTEM MATCHES CYGWIN)
    SET(MINGW ON)
  ENDIF(CMAKE_SYSTEM MATCHES CYGWIN)
ENDIF(CMAKE_SYSTEM MATCHES CYGWIN OR CMAKE_SYSTEM MATCHES MINGW)

SET(CMAKE_WORDS_BIGENDIAN 0)
TEST_BIG_ENDIAN(CMAKE_WORDS_BIGENDIAN)

IF(CMAKE_WORDS_BIGENDIAN)
  ADD_DEFINE(WORDS_BIGENDIAN)
ENDIF(CMAKE_WORDS_BIGENDIAN)


OPTION(BUILD_STATIC "build static library too" OFF)
OPTION(BUILD_TESTS  "build the test-suite"     OFF)
OPTION(PROFILE      "enable profile"           OFF)
OPTION(OPTIMIZATION "enable optimization"      ON)
OPTION(DEBUG        "enable debug"             OFF)
OPTION(WARNINGS     "enable warnings"          OFF)

# sound backends
IF(CMAKE_SYSTEM MATCHES "Linux")
  OPTION(ALSA        "alsa back-end"         ON)
  OPTION(ALSA_DLOPEN "alsa dynamic loading"  ON)
ENDIF(CMAKE_SYSTEM MATCHES "Linux")

#IF(WIN32)
#  OPTION(DSOUND      "directsound back-end"  OFF)
#ENDIF(WIN32)

OPTION(NATIVE      "native back-end"      ON)
OPTION(DMEDIA      "DMedia/Irix back-end" ON)
OPTION(ESD         "esd back-end"         ON)
OPTION(ESD_DLOPEN  "esd dynamic loading"  ON)
OPTION(OSS         "oss back-end"         ON)
OPTION(SDL         "sdl back-end"         ON)
OPTION(SDL_DLOPEN  "sdl dynamic loading"  ON)
OPTION(WAVEOUT     "wave out back-end"    ON)
OPTION(NULLOUT     "null out back-end"    ON)

# additional codecs
OPTION(VORBIS         "vorbis support"          ON)
OPTION(VORBIS_DLOPEN  "vorbis dynamic loading"  ON)
OPTION(MP3            "mp3 support"             ON)
OPTION(MP3_DLOPEN     "mp3 dynamic loading"     ON)

IF(DEBUG)
  ADD_DEFINE(DEBUG)
  SET(AL_OBJS ${AL_OBJS} ${AL_DEBUG_OBJS})
  IF(WARNINGS)
    OPTION(MORE_WARNINGS "enable more warnings"         OFF)
    OPTION(USE_WERROR    "enable fail on all warning"   OFF)
  ENDIF(WARNINGS)

  OPTION(EFENCE          "enable efence support"        OFF)
  OPTION(DEBUG_MAXIMUS   "enable all debugging"         OFF)
  OPTION(DEBUG_STUB      "enable stub debugging"        OFF)
  OPTION(DEBUG_LOOP      "enable looping debugging"     OFF)
  OPTION(DEBUG_CONVERT   "enable convertion debugging"  OFF)
  OPTION(DEBUG_CONFIG    "enable config file debugging" OFF)
  OPTION(DEBUG_LOCK      "enable lock debugging"        OFF)
  OPTION(DEBUG_EXT       "enable extension debugging"   OFF)
  OPTION(DEBUG_BUFFER    "enable buffer debugging"      OFF)
  OPTION(DEBUG_SOURCE    "enable source debugging"      OFF)
  OPTION(DEBUG_MIXER     "enable mixer debugging"       OFF)
  OPTION(DEBUG_STREAMING "enable streaming debugging"   OFF)
  OPTION(DEBUG_MATH      "enable math debugging"        OFF)
  OPTION(DEBUG_MEM       "enable mem debugging"         OFF)
  OPTION(DEBUG_CONTEXT   "enable context debugging"     OFF)
  OPTION(DEBUG_QUEUE     "enable queue debugging"       OFF)
  OPTION(DEBUG_FILTER    "enable filter debugging"      OFF)

  IF(EFENCE)
    FIND_LIBRARY(EFENCE_LIB efence PATHS /usr/lib /usr/local/lib)
    IF(EFENCE_LIB MATCHES "NOTFOUND")
      MESSAGE("Electric Fence not installed - disabled.")
      SET(EFENCE OFF CACHE BOOL "" FORCE)
    ELSE(EFENCE_LIB MATCHES "NOTFOUND")

      SET(ADD_LIBS ${ADD_LIBS} ${EFENCE_LIB})
    ENDIF(EFENCE_LIB MATCHES "NOTFOUND")
  ENDIF(EFENCE)

  MACRO(CHECK_DEBUG DEBUG_OPTION)
  IF(${DEBUG_OPTION})
    ADD_DEFINE(${DEBUG_OPTION})
  ENDIF(${DEBUG_OPTION})
  ENDMACRO(CHECK_DEBUG)

  CHECK_DEBUG(DEBUG_MAXIMUS)
  CHECK_DEBUG(DEBUG_STUB)
  CHECK_DEBUG(DEBUG_LOOP)
  CHECK_DEBUG(DEBUG_CONVERT)
  CHECK_DEBUG(DEBUG_CONFIG)
  CHECK_DEBUG(DEBUG_LOCK)
  CHECK_DEBUG(DEBUG_EXT)
  CHECK_DEBUG(DEBUG_BUFFER)
  CHECK_DEBUG(DEBUG_SOURC)
  CHECK_DEBUG(DEBUG_MIXER)
  CHECK_DEBUG(DEBUG_STREAMING)
  CHECK_DEBUG(DEBUG_MATH)
  CHECK_DEBUG(DEBUG_MEM)
  CHECK_DEBUG(DEBUG_CONTEXT)
  CHECK_DEBUG(DEBUG_QUEUE)
  CHECK_DEBUG(DEBUG_FILTER)
ELSE(DEBUG)

  ADD_DEFINE(NDEBUG)
ENDIF(DEBUG)


# library stuff
IF(UNIX)
  SET(ADD_LIBS ${ADD_LIBS} m)
ENDIF(UNIX)

CHECK_INCLUDE_FILE("dlfcn.h" HAVE_DL)
IF(HAVE_DL)
  ADD_DEFINE(USE_DLOPEN)
  CHECK_LIBRARY_EXISTS("dl" "dlopen" "" HAVE_LIB_DL)
  IF(HAVE_LIB_DL)
    SET(ADD_LIBS ${ADD_LIBS} dl)
  ENDIF(HAVE_LIB_DL)
ELSE(HAVE_DL)
  MESSAGE("Dynamic loading of libraries is not available.")
ENDIF(HAVE_DL)


# Special OS objs for target systems
IF(NATIVE)
IF(CMAKE_SYSTEM MATCHES "SunOS")
  ADD_DEFINE(USE_BACKEND_NATIVE_SOLARIS)
  SET(OS_OBJS ${SOLARIS_OBJS})
ELSE(CMAKE_SYSTEM MATCHES "SunOS")

IF(CMAKE_SYSTEM MATCHES "Windows")
  ADD_DEFINE(USE_BACKEND_NATIVE_WINDOWS)
  SET(OS_OBJS ${WINDOWS_OBJS})
  # need winmm library for waveOutOpen etc.
  SET(ADD_LIBS ${ADD_LIBS} winmm)
ELSE(CMAKE_SYSTEM MATCHES "Windows")

IF(CMAKE_SYSTEM MATCHES "Darwin")
  ADD_DEFINE(USE_BACKEND_NATIVE_DARWIN)
  SET(OS_OBJS ${DARWIN_OBJS})
  #FIXME
  SET(ADD_LDFLAGS "${ADD_LDFLAGS} -I/System/Library/Frameworks/CoreAudio.framework/Headers/ -framework CoreAudio")
ELSE(CMAKE_SYSTEM MATCHES "Darwin")

ENDIF(CMAKE_SYSTEM MATCHES "Darwin")
ENDIF(CMAKE_SYSTEM MATCHES "Windows")
ENDIF(CMAKE_SYSTEM MATCHES "SunOS")
ENDIF(NATIVE)

# threads handling
FIND_PACKAGE(Threads)
IF(CMAKE_USE_WIN32_THREADS_INIT)
  ADD_DEFINE(USE_WINDOWSTHREADING)
ELSE(CMAKE_USE_WIN32_THREADS_INIT)

IF(CMAKE_USE_PTHREADS_INIT)
  ADD_DEFINE(USE_POSIXTHREADING)
  SET(ADD_LIBS ${ADD_LIBS} ${CMAKE_THREAD_LIBS_INIT})
ELSE(CMAKE_USE_PTHREADS_INIT)

  ADD_DEFINE(NO_THREADING)
ENDIF(CMAKE_USE_PTHREADS_INIT)
ENDIF(CMAKE_USE_WIN32_THREADS_INIT)

FIND_PACKAGE(SleepFunction)
FIND_PACKAGE(SelectArgtypes)
CHECK_INCLUDE_FILE_DEFINE(sys/stat.h HAVE_SYS_STAT_H)
CHECK_INCLUDE_FILE_DEFINE(strings.h HAVE_STRINGS_H)
CHECK_INCLUDE_FILE_DEFINE(stdint.h HAVE_STDINT_H)
CHECK_INCLUDE_FILE_DEFINE(stropts.h HAVE_STROPTS_H)
CHECK_INCLUDE_FILE_DEFINE(sys/ioctl.h HAVE_SYS_IOCTL_H)

CHECK_FUNCTION_DEFINE("#include <stdio.h>" snprintf "((char *)0, 0, \"\")" HAVE_SNPRINTF)
IF(NOT HAVE_SNPRINTF)
  CHECK_FUNCTION_DEFINE("#include <stdio.h>" _snprintf "((char *)0, 0, \"\")" HAVE__SNPRINTF)
ENDIF(NOT HAVE_SNPRINTF)

IF(CMAKE_SYSTEM MATCHES "Windows")
  # needed for TryEnterCriticalSection
  ADD_DEFINITIONS(-D_WIN32_WINNT=0x0400)
  # don't spit out deprecation warnings
  ADD_DEFINITIONS(-D_CRT_SECURE_NO_DEPRECATE)

  # check for __int8 type
  SET(SOURCE
"int main(){__int8 foo;return 0;}
")
  FILE(WRITE "${CMAKE_BINARY_DIR}/CMakeTmp/src.c" "${SOURCE}")

  TRY_COMPILE(HAVE___INT8
          ${CMAKE_BINARY_DIR}
          ${CMAKE_BINARY_DIR}/CMakeTmp/src.c
          CMAKE_FLAGS
          OUTPUT_VARIABLE OUTPUT)
  IF(HAVE___INT8)
    ADD_DEFINE("HAVE___INT8 1")
  ENDIF(HAVE___INT8)
ENDIF(CMAKE_SYSTEM MATCHES "Windows")

# sound backends
FIND_PACKAGE(OpenALBackends)

#             OPT    PKG-NAME   LIB        HEADER              TEST-FUNCTION
IF(CMAKE_SYSTEM MATCHES "Linux")
  CHECK_BACKEND(ALSA   alsa       asound     alsa/asoundlib.h    snd_pcm_info_sizeof)
ENDIF(CMAKE_SYSTEM MATCHES "Linux")
CHECK_BACKEND(DMEDIA ""         audio      dmedia/audio.h      alGetResourceByName)
CHECK_BACKEND(ESD    esd        esd        esd.h               esd_audio_open)
CHECK_BACKEND(OSS    ""         ""         sys/soundcard.h     "")
CHECK_BACKEND(SDL    sdl        sdl        SDL.h               SDL_LockAudio)
#IF(WIN32)
#  CHECK_BACKEND(DSOUND ""       dsound     dsound.h     ???)
#ENDIF(WIN32)

CHECK_BACKEND(MP3    smpeg      smpeg      smpeg.h             SMPEG_play)
CHECK_BACKEND(VORBIS vorbisfile vorbisfile vorbis/vorbisfile.h ov_clear)

IF(WAVEOUT)
  SET(OS_OBJS ${OS_OBJS} ${WAVEOUT_OBJS})
  ADD_DEFINE(USE_BACKEND_WAVEOUT)
ENDIF(WAVEOUT)

IF(NULLOUT)
  SET(OS_OBJS ${OS_OBJS} ${NULL_OBJS})
  ADD_DEFINE(USE_BACKEND_NULL)
ENDIF(NULLOUT)


# Special objs for architectures
IF(CMAKE_SYSTEM_MACHINE MATCHES "i.86" OR CMAKE_SYSTEM_MACHINE MATCHES "x86_64")
  INCLUDE_DIRECTORIES(${OpenAL_SOURCE_DIR}/src/arch/i386)
  SET(ARCH_OBJS ${x86_OBJS})
  ADD_DEFINE(HAVE_X86)

  CHECK_MMX()
  IF(HAVE_MMX)
    CHECK_SSE2()
  ENDIF(HAVE_MMX)

  IF(HAVE_MMX OR HAVE_SSE2)
    IF(HAVE_MMX)
      SET(ARCH_OBJS ${ARCH_OBJS} ${x86_MMX_OBJS})
      FOREACH(SRC ${x86_MMX_OBJS})
        SET_SOURCE_FILES_PROPERTIES(${SRC} PROPERTIES COMPILE_FLAGS "${MMX_FLAGS}")
      ENDFOREACH(SRC)
      ADD_DEFINE(HAVE_MMX)
    ENDIF(HAVE_MMX)

    IF(HAVE_SSE2)
      SET(ARCH_OBJS ${ARCH_OBJS} ${x86_SSE2_OBJS})
      FOREACH(SRC ${x86_SSE2_OBJS})
        SET_SOURCE_FILES_PROPERTIES(${SRC} PROPERTIES COMPILE_FLAGS "${SSE2_FLAGS}")
      ENDFOREACH(SRC)
      ADD_DEFINE(HAVE_SSE2)
    ENDIF(HAVE_SSE2)

    IF(CMAKE_SYSTEM_MACHINE MATCHES "i.86" OR UNIX)
      FIND_PROGRAM(HAVE_NASM NAMES yasm nasm)

      IF(HAVE_NASM MATCHES "NOTFOUND")
          IF(CMAKE_SYSTEM_MACHINE MATCHES "x86_64")
            MESSAGE("It's safe for you to not detect MMX nor SSE2, as x86_64 will have both.")
          ELSE(CMAKE_SYSTEM_MACHINE MATCHES "x86_64")

            MESSAGE(FATAL_ERROR "Neither yasm nor nasm found - needed for SIMD detection")
          ENDIF(CMAKE_SYSTEM_MACHINE MATCHES "x86_64")
      ELSE(HAVE_NASM MATCHES "NOTFOUND")

        PROJECT(OpenAL ASM)
        MESSAGE(STATUS "Using YASM/NASM")
        ADD_DEFINE(HAVE_CPU_CAPS_DETECTION)

        IF(CMAKE_SYSTEM_MACHINE MATCHES "x86_64")
          SET(ARCH_OBJS ${ARCH_OBJS} ${x86_64_LINUX_NASM_OBJS})
        ELSE(CMAKE_SYSTEM_MACHINE MATCHES "x86_64")

          SET(ARCH_OBJS ${ARCH_OBJS} ${x86_NASM_OBJS})

          IF(HAVE_MMX)
            SET(ARCH_OBJS ${ARCH_OBJS} ${x86_MMX_NASM_OBJS})
            ADD_DEFINE(HAVE_MMX_MEMCPY 1)
          ENDIF(HAVE_MMX)
        ENDIF(CMAKE_SYSTEM_MACHINE MATCHES "x86_64")

        TEST_NASM_COMPILER_VISIBILITY()
      ENDIF(HAVE_NASM MATCHES "NOTFOUND")
    ENDIF(CMAKE_SYSTEM_MACHINE MATCHES "i.86" OR UNIX)

    IF(CMAKE_SYSTEM_MACHINE MATCHES "i686" OR CMAKE_SYSTEM_MACHINE MATCHES "x86_64")
      OPTION(OPTIM_GENERIC "enable full generic mixing despite MMX optimized routines" OFF)
    ELSE(CMAKE_SYSTEM_MACHINE MATCHES "i686" OR CMAKE_SYSTEM_MACHINE MATCHES "x86_64")

      OPTION(OPTIM_GENERIC "enable full generic mixing" ON)
    ENDIF(CMAKE_SYSTEM_MACHINE MATCHES "i686" OR CMAKE_SYSTEM_MACHINE MATCHES "x86_64")

    IF(NOT OPTIM_GENERIC)
      ADD_DEFINE(USE_LIGHT_GEN_MIXING)
    ENDIF(NOT OPTIM_GENERIC)
  ENDIF(HAVE_MMX OR HAVE_SSE2)
ELSE(CMAKE_SYSTEM_MACHINE MATCHES "i.86" OR CMAKE_SYSTEM_MACHINE MATCHES "x86_64")

  SET(ARCH_OBJS ${PORTABLE_OBJS})
ENDIF(CMAKE_SYSTEM_MACHINE MATCHES "i.86" OR CMAKE_SYSTEM_MACHINE MATCHES "x86_64")


# compiler specific settings
TEST_COMPILER_ATTRIBUTE()
TEST_COMPILER_VISIBILITY()
SET_COMPILER_FLAGS()

ADD_DEFINE(AL_SUPPORT_BYTE_LOKI_SOURCE_ATTR_)
ADD_DEFINE(AL_BUILD_LIBRARY)


GENERATE_CONFIG_H()

SET(CMAKE_C_FLAGS "${ADD_WFLAGS} ${ADD_CFLAGS} ${CMAKE_C_FLAGS}")
SET(CMAKE_CXX_FLAGS "${ADD_CFLAGS} ${CMAKE_CXX_FLAGS}")
SET(CMAKE_SHARED_LINKER_FLAGS "${ADD_LDFLAGS} ${CMAKE_SHARED_LINKER_FLAGS}")
SET(CMAKE_MODULE_LINKER_FLAGS "${ADD_LDFLAGS} ${CMAKE_MODULE_LINKER_FLAGS}")

SET(OPENAL_OBJS ${AL_OBJS}
                ${OS_OBJS}
                ${ARCH_OBJS}
                ${ALC_OBJS}
                ${AC_OBJS}
                ${EXT_OBJS})

IF(BUILD_STATIC)
  # we can't create a static library with the same name
  # as the shared one, so we copy it over after creation
  ADD_LIBRARY(openal_static STATIC ${OPENAL_OBJS})
  #SET_TARGET_PROPERTIES(openal_static PROPERTIES OUTPUT_NAME openal)
  SET_TARGET_PROPERTIES(openal_static PROPERTIES LINKER_LANGUAGE C)
  TARGET_LINK_LIBRARIES(openal_static ${ADD_LIBS})
  IF(NOT WIN32)
    ADD_CUSTOM_COMMAND(
      TARGET openal_static
      POST_BUILD
      COMMAND ${CMAKE_COMMAND}
      ARGS -E copy
        ${CMAKE_BINARY_DIR}/${CMAKE_STATIC_LIBRARY_PREFIX}openal_static${CMAKE_STATIC_LIBRARY_SUFFIX}
        ${CMAKE_BINARY_DIR}/${CMAKE_STATIC_LIBRARY_PREFIX}openal${CMAKE_STATIC_LIBRARY_SUFFIX})
    INSTALL_FILES(/lib FILES ${CMAKE_STATIC_LIBRARY_PREFIX}openal${CMAKE_STATIC_LIBRARY_SUFFIX})
  ENDIF(NOT WIN32)
ENDIF(BUILD_STATIC)

ADD_LIBRARY(openal SHARED ${OPENAL_OBJS})
SET_TARGET_PROPERTIES(openal PROPERTIES LINKER_LANGUAGE C)
SET_TARGET_PROPERTIES(openal PROPERTIES VERSION ${SO_VERSION} SOVERSION ${SO_MAJOR_VERSION})
TARGET_LINK_LIBRARIES(openal ${ADD_LIBS})
INSTALL_TARGETS(/lib openal)
INSTALL_FILES(/include/AL FILES common/include/AL/al.h
                                common/include/AL/alc.h
                                include/AL/alext.h)

# build test-suite
IF(BUILD_TESTS)
  # need CXX for testcpp only
  PROJECT(OpenAL CXX)

  IF(DEFINED ALUT_LIB_DIR)
    FIND_LIBRARY(ALUT_LIB alut PATHS ${ALUT_LIB_DIR}
      NO_CMAKE_ENVIRONMENT_PATH
      NO_CMAKE_PATH
      NO_SYSTEM_PATH
      NO_CMAKE_SYSTEM_PATH)
  ENDIF(DEFINED ALUT_LIB_DIR)

  IF(NOT DEFINED ALUT_LIB_DIR OR ALUT_LIB MATCHES "NOTFOUND")
    FIND_LIBRARY(ALUT_LIB alut PATHS /usr/lib /usr/local/lib)
  ENDIF(NOT DEFINED ALUT_LIB_DIR OR ALUT_LIB MATCHES "NOTFOUND")

  IF(DEFINED ALUT_INCLUDE_DIR)
    FIND_FILE(ALUT_H AL/alut.h PATHS ${ALUT_INCLUDE_DIR}
      NO_CMAKE_ENVIRONMENT_PATH
      NO_CMAKE_PATH
      NO_SYSTEM_PATH
      NO_CMAKE_SYSTEM_PATH)
  ENDIF(DEFINED ALUT_INCLUDE_DIR)

  IF(NOT DEFINED ALUT_INCLUDE_DIR OR ALUT_H MATCHES "NOTFOUND")
    FIND_FILE(ALUT_H AL/alut.h)
  ENDIF(NOT DEFINED ALUT_INCLUDE_DIR OR ALUT_H MATCHES "NOTFOUND")

  IF(ALUT_LIB MATCHES "NOTFOUND" OR ALUT_H MATCHES "NOTFOUND")
    MESSAGE("alut not found, cannot build tests")
  ELSE(ALUT_LIB MATCHES "NOTFOUND" OR ALUT_H MATCHES "NOTFOUND")

    MESSAGE(STATUS "Using alut header ${ALUT_H}")
    MESSAGE(STATUS "Using alut lib ${ALUT_LIB}")

    IF(DEFINED ALUT_INCLUDE_DIR)
      INCLUDE_DIRECTORIES(${ALUT_INCLUDE_DIR})
      SET(ALUT_INCLUDE_DIR "${ALUT_INCLUDE_DIR}" CACHE INTERNAL "")
    ENDIF(DEFINED ALUT_INCLUDE_DIR)

    ADD_LIBRARY(testlib test/testlib.c)
    TARGET_LINK_LIBRARIES(testlib ${ADD_LIBS} openal ${ALUT_LIB})

    SET(TESTS rotatesound
              reverb
              da
              multi
              multinothread
              alut
              time
              multialut
              alinitexit
              single
              nothread
              pitch
              pitch2
              pitch3
              pitch4
              doppler
              sourcestop
              ext
              stream
              callback
              orient
              relative
              channel
              channel2
              pause
              pitchnothread
              pitchnothread2
              gain
              loop
              listener
              listener2
              listener3
              listener4
              bufferuniq
              sourceuniq
              gendelbuffer_error
              gendelsource_error
              multichannel
              mp3
              mp32
              vorbis
              gainlinear
              multimp3
              queue
              queue2
              queue3
              capture2
              leak
              multicontext
              strings
              attributes)

    FOREACH(TEST ${TESTS})
      SET(TEST_SRC test/test${TEST}.c)
      # gets added at the beginning thus having no effect
      IF(CMAKE_COMPILER_IS_GNUCC)
        SET_SOURCE_FILES_PROPERTIES(${TEST_SRC} PROPERTIES COMPILE_FLAGS "-Wno-unused")
      ENDIF(CMAKE_COMPILER_IS_GNUCC)
      ADD_EXECUTABLE(test${TEST} ${TEST_SRC})
      TARGET_LINK_LIBRARIES(test${TEST} testlib openal ${ALUT_LIB})
    ENDFOREACH(TEST)

    IF(CMAKE_COMPILER_IS_GNUCC)
      SET_SOURCE_FILES_PROPERTIES(test/testcpp.cc PROPERTIES COMPILE_FLAGS "-Wno-unused")
    ENDIF(CMAKE_COMPILER_IS_GNUCC)
    ADD_EXECUTABLE(testcpp test/testcpp.cc)
    TARGET_LINK_LIBRARIES(testcpp testlib openal ${ALUT_LIB})

    # copy over testdata, so test-suite can be used in binary dir
    SET(TESTDATA boom.mp3
                 boom.ogg
                 boom.wav
                 fire2.wav
                 multiboom.wav
                 multisample.wav
                 sample.wav)

    FOREACH(TESTDATUM ${TESTDATA})
      CONFIGURE_FILE(${CMAKE_SOURCE_DIR}/test/${TESTDATUM} ${CMAKE_BINARY_DIR}/${TESTDATUM} COPYONLY)
    ENDFOREACH(TESTDATUM)
  ENDIF(ALUT_LIB MATCHES "NOTFOUND" OR  ALUT_H MATCHES "NOTFOUND")
ENDIF(BUILD_TESTS)


# needed for openal.pc.in and openal-config.in
SET(prefix ${CMAKE_INSTALL_PREFIX})
SET(exec_prefix "\${prefix}")
SET(libdir "\${exec_prefix}/lib")
SET(bindir "\${exec_prefix}/bin")
SET(includedir "\${prefix}/include")
SET(requirements "")
SET(PTHREAD_LIBS ${CMAKE_THREAD_LIBS_INIT})

CONFIGURE_FILE(${CMAKE_SOURCE_DIR}/admin/pkgconfig/openal.pc.in ${CMAKE_BINARY_DIR}/admin/pkgconfig/openal.pc @ONLY)
CONFIGURE_FILE(${CMAKE_SOURCE_DIR}/admin/pkgconfig/openal-config.in ${CMAKE_BINARY_DIR}/admin/pkgconfig/openal-config @ONLY)
INSTALL_PROGRAMS(/bin FILES admin/pkgconfig/openal-config)
INSTALL_FILES(/lib/pkgconfig FILES admin/pkgconfig/openal.pc)


# info file
IF(UNIX)
  FIND_PROGRAM(MAKEINFO_PRG makeinfo)
  IF(NOT MAKEINFO_PRG MATCHES "NOTFOUND")
    IF(NOT EXISTS ${CMAKE_BINARY_DIR}/doc)
      MAKE_DIRECTORY(${CMAKE_BINARY_DIR}/doc)
    ENDIF(NOT EXISTS ${CMAKE_BINARY_DIR}/doc)

    ADD_CUSTOM_COMMAND(
      OUTPUT    ${CMAKE_BINARY_DIR}/doc/openal.info
      DEPENDS   ${CMAKE_SOURCE_DIR}/doc/openal.texi
      COMMAND   cd ${CMAKE_BINARY_DIR}/doc && ${MAKEINFO_PRG}
      ARGS      ${CMAKE_SOURCE_DIR}/doc/openal.texi)

    ADD_CUSTOM_TARGET(InfoFile ALL echo DEPENDS ${CMAKE_BINARY_DIR}/doc/openal.info)
    INSTALL_FILES(/share/info FILES doc/openal.info)
  ENDIF(NOT MAKEINFO_PRG MATCHES "NOTFOUND")
ENDIF(UNIX)

