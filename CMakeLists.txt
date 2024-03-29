#
# Copyright (c) 2006-2014 LOVE Development Team
#
# This software is provided 'as-is', without any express or implied
# warranty.  In no event will the authors be held liable for any damages
# arising from the use of this software.
#
# Permission is granted to anyone to use this software for any purpose,
# including commercial applications, and to alter it and redistribute it
# freely, subject to the following restrictions:
#
# 1. The origin of this software must not be misrepresented; you must not
#    claim that you wrote the original software. If you use this software
#    in a product, an acknowledgment in the product documentation would be
#    appreciated but is not required.
# 2. Altered source versions must be plainly marked as such, and must not be
#    misrepresented as being the original software.
# 3. This notice may not be removed or altered from any source distribution.
#

if(${CMAKE_CURRENT_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_BINARY_DIR})
	# Protip: run cmake like this: cmake -G "<generator>" -H. -Bbuild
	message(FATAL_ERROR "Prevented in-tree build.")
endif()

cmake_minimum_required(VERSION 2.8)

project(love)

if(NOT MEGA)
	message(FATAL_ERROR "
It is currently only possible to build with megasource on Windows.
Please see http://bitbucket.org/rude/megasource
")
endif()

set(LOVE_EXE_NAME love)
set(LOVE_LIB_NAME liblove)

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
	set(LOVE_X64 TRUE)
	set(LOVE_TARGET_PLATFORM x64)
else()
	set(LOVE_X86 TRUE)
	set(LOVE_TARGET_PLATFORM x86)
endif()

option(LOVE_JIT "Use LuaJIT" TRUE)

if(LOVE_JIT)
	message(STATUS "LuaJIT: Enabled")
else()
	message(STATUS "LuaJIT: Disabled")
endif()

message(STATUS "Target platform: ${LOVE_TARGET_PLATFORM}")

find_package(OpenGL)

if(${LOVE_JIT})
	set(MEGA_LUA ${MEGA_LUAJIT_LIB})
	set(MEGA_EXTRA_INCLUDE ${MEGA_LUAJIT_INCLUDE})
	set(MEGA_EXTRA_DLLS ${MEGA_LUAJIT_DLL})
	set(MEGA_EXTRA_DEPENDECIES luajit)
else()
	set(MEGA_LUA ${MEGA_LUA51})
	# MEGA_LUA51 is a CMake target, so includes are handled
	# automatically.
endif()

#
# common
#

set(LOVE_SRC_COMMON
	src/common/b64.cpp
	src/common/b64.h
	src/common/config.h
	src/common/Data.h
	src/common/delay.cpp
	src/common/delay.h
	src/common/EnumMap.h
	src/common/Exception.cpp
	src/common/Exception.h
	src/common/int.h
	src/common/math.h
	src/common/Matrix.cpp
	src/common/Matrix.h
	src/common/Memoizer.cpp
	src/common/Memoizer.h
	src/common/Module.cpp
	src/common/Module.h
	src/common/Object.cpp
	src/common/Object.h
	src/common/Reference.cpp
	src/common/Reference.h
	src/common/runtime.cpp
	src/common/runtime.h
	src/common/StringMap.h
	src/common/types.h
	src/common/utf8.cpp
	src/common/utf8.h
	src/common/Variant.cpp
	src/common/Variant.h
	#src/common/Vector.cpp # Vector.cpp is empty.
	src/common/Vector.h
	src/common/version.h
	src/common/wrap_Data.cpp
	src/common/wrap_Data.h
)

source_group("common" FILES ${LOVE_SRC_COMMON})

#
# love.audio
#

set(LOVE_SRC_MODULE_AUDIO_ROOT
	src/modules/audio/Audio.cpp
	src/modules/audio/Audio.h
	src/modules/audio/Source.cpp
	src/modules/audio/Source.h
	src/modules/audio/wrap_Audio.cpp
	src/modules/audio/wrap_Audio.h
	src/modules/audio/wrap_Source.cpp
	src/modules/audio/wrap_Source.h
)

set(LOVE_SRC_MODULE_AUDIO_NULL
	src/modules/audio/null/Audio.cpp
	src/modules/audio/null/Audio.h
	src/modules/audio/null/Source.cpp
	src/modules/audio/null/Source.h
)

set(LOVE_SRC_MODULE_AUDIO_OPENAL
	src/modules/audio/openal/Audio.cpp
	src/modules/audio/openal/Audio.h
	src/modules/audio/openal/Pool.cpp
	src/modules/audio/openal/Pool.h
	src/modules/audio/openal/Source.cpp
	src/modules/audio/openal/Source.h
)

set(LOVE_SRC_MODULE_AUDIO
	${LOVE_SRC_MODULE_AUDIO_ROOT}
	${LOVE_SRC_MODULE_AUDIO_NULL}
	${LOVE_SRC_MODULE_AUDIO_OPENAL}
)

source_group("modules\\audio" FILES ${LOVE_SRC_MODULE_AUDIO_ROOT})
source_group("modules\\audio\\null" FILES ${LOVE_SRC_MODULE_AUDIO_NULL})
source_group("modules\\audio\\openal" FILES ${LOVE_SRC_MODULE_AUDIO_OPENAL})

#
# love.event
#

set(LOVE_SRC_MODULE_EVENT_ROOT
	src/modules/event/Event.cpp
	src/modules/event/Event.h
)

set(LOVE_SRC_MODULE_EVENT_SDL
	src/modules/event/sdl/Event.cpp
	src/modules/event/sdl/Event.h
	src/modules/event/sdl/wrap_Event.cpp
	src/modules/event/sdl/wrap_Event.h
)

set(LOVE_SRC_MODULE_EVENT
	${LOVE_SRC_MODULE_EVENT_ROOT}
	${LOVE_SRC_MODULE_EVENT_SDL}
)

source_group("modules\\event" FILES ${LOVE_SRC_MODULE_EVENT_ROOT})
source_group("modules\\event\\sdl" FILES ${LOVE_SRC_MODULE_EVENT_SDL})

#
# love.filesystem
#

set(LOVE_SRC_MODULE_FILESYSTEM_ROOT
	src/modules/filesystem/File.cpp
	src/modules/filesystem/File.h
	src/modules/filesystem/FileData.cpp
	src/modules/filesystem/FileData.h
	src/modules/filesystem/wrap_File.cpp
	src/modules/filesystem/wrap_File.h
	src/modules/filesystem/wrap_FileData.cpp
	src/modules/filesystem/wrap_FileData.h
	src/modules/filesystem/wrap_Filesystem.cpp
	src/modules/filesystem/wrap_Filesystem.h
)

set(LOVE_SRC_MODULE_FILESYSTEM_PHYSFS
	src/modules/filesystem/physfs/File.cpp
	src/modules/filesystem/physfs/File.h
	src/modules/filesystem/physfs/Filesystem.cpp
	src/modules/filesystem/physfs/Filesystem.h
)

set(LOVE_SRC_MODULE_FILESYSTEM
	${LOVE_SRC_MODULE_FILESYSTEM_ROOT}
	${LOVE_SRC_MODULE_FILESYSTEM_PHYSFS}
)

source_group("modules\\filesystem" FILES ${LOVE_SRC_MODULE_FILESYSTEM_ROOT})
source_group("modules\\filesystem\\physfs" FILES ${LOVE_SRC_MODULE_FILESYSTEM_PHYSFS})

#
# love.font
#

set(LOVE_SRC_MODULE_FONT_ROOT
	src/modules/font/Font.h
	src/modules/font/GlyphData.cpp
	src/modules/font/GlyphData.h
	src/modules/font/ImageRasterizer.cpp
	src/modules/font/ImageRasterizer.h
	src/modules/font/Rasterizer.cpp
	src/modules/font/Rasterizer.h
	src/modules/font/wrap_GlyphData.cpp
	src/modules/font/wrap_GlyphData.h
	src/modules/font/wrap_Rasterizer.cpp
	src/modules/font/wrap_Rasterizer.h
)

set(LOVE_SRC_MODULE_FONT_FREETYPE
	src/modules/font/freetype/Font.cpp
	src/modules/font/freetype/Font.h
	src/modules/font/freetype/TrueTypeRasterizer.cpp
	src/modules/font/freetype/TrueTypeRasterizer.h
	src/modules/font/freetype/wrap_Font.cpp
	src/modules/font/freetype/wrap_Font.h
)

set(LOVE_SRC_MODULE_FONT
	${LOVE_SRC_MODULE_FONT_ROOT}
	${LOVE_SRC_MODULE_FONT_FREETYPE}
)

source_group("modules\\font" FILES ${LOVE_SRC_MODULE_FONT_ROOT})
source_group("modules\\font\\freetype" FILES ${LOVE_SRC_MODULE_FONT_FREETYPE})

#
# love.graphics
#

set(LOVE_SRC_MODULE_GRAPHICS_ROOT
	src/modules/graphics/Color.h
	src/modules/graphics/Drawable.h
	src/modules/graphics/Graphics.cpp
	src/modules/graphics/Graphics.h
	src/modules/graphics/Quad.cpp
	src/modules/graphics/Quad.h
	src/modules/graphics/Texture.cpp
	src/modules/graphics/Texture.h
	src/modules/graphics/Volatile.cpp
	src/modules/graphics/Volatile.h
)

set(LOVE_SRC_MODULE_GRAPHICS_OPENGL
	src/modules/graphics/opengl/Canvas.cpp
	src/modules/graphics/opengl/Canvas.h
	src/modules/graphics/opengl/Font.cpp
	src/modules/graphics/opengl/Font.h
	src/modules/graphics/opengl/GLee.c
	src/modules/graphics/opengl/GLee.h
	src/modules/graphics/opengl/Graphics.cpp
	src/modules/graphics/opengl/Graphics.h
	src/modules/graphics/opengl/Image.cpp
	src/modules/graphics/opengl/Image.h
	src/modules/graphics/opengl/Mesh.cpp
	src/modules/graphics/opengl/Mesh.h
	src/modules/graphics/opengl/OpenGL.cpp
	src/modules/graphics/opengl/OpenGL.h
	src/modules/graphics/opengl/ParticleSystem.cpp
	src/modules/graphics/opengl/ParticleSystem.h
	src/modules/graphics/opengl/Polyline.cpp
	src/modules/graphics/opengl/Polyline.h
	src/modules/graphics/opengl/Shader.cpp
	src/modules/graphics/opengl/Shader.h
	src/modules/graphics/opengl/SpriteBatch.cpp
	src/modules/graphics/opengl/SpriteBatch.h
	src/modules/graphics/opengl/Texture.h
	src/modules/graphics/opengl/VertexBuffer.cpp
	src/modules/graphics/opengl/VertexBuffer.h
	src/modules/graphics/opengl/wrap_Canvas.cpp
	src/modules/graphics/opengl/wrap_Canvas.h
	src/modules/graphics/opengl/wrap_Font.cpp
	src/modules/graphics/opengl/wrap_Font.h
	src/modules/graphics/opengl/wrap_Graphics.cpp
	src/modules/graphics/opengl/wrap_Graphics.h
	src/modules/graphics/opengl/wrap_Image.cpp
	src/modules/graphics/opengl/wrap_Image.h
	src/modules/graphics/opengl/wrap_Mesh.cpp
	src/modules/graphics/opengl/wrap_Mesh.h
	src/modules/graphics/opengl/wrap_ParticleSystem.cpp
	src/modules/graphics/opengl/wrap_ParticleSystem.h
	src/modules/graphics/opengl/wrap_Quad.cpp
	src/modules/graphics/opengl/wrap_Quad.h
	src/modules/graphics/opengl/wrap_Shader.cpp
	src/modules/graphics/opengl/wrap_Shader.h
	src/modules/graphics/opengl/wrap_SpriteBatch.cpp
	src/modules/graphics/opengl/wrap_SpriteBatch.h
	src/modules/graphics/opengl/wrap_Texture.cpp
	src/modules/graphics/opengl/wrap_Texture.h
)

set(LOVE_SRC_MODULE_GRAPHICS
	${LOVE_SRC_MODULE_GRAPHICS_ROOT}
	${LOVE_SRC_MODULE_GRAPHICS_OPENGL}
)

source_group("modules\\graphics" FILES ${LOVE_SRC_MODULE_GRAPHICS_ROOT})
source_group("modules\\graphics\\opengl" FILES ${LOVE_SRC_MODULE_GRAPHICS_OPENGL})

#
# love.image
#

set(LOVE_SRC_MODULE_IMAGE_ROOT
	src/modules/image/CompressedData.cpp
	src/modules/image/CompressedData.h
	src/modules/image/Image.h
	src/modules/image/ImageData.cpp
	src/modules/image/ImageData.h
	src/modules/image/wrap_CompressedData.cpp
	src/modules/image/wrap_CompressedData.h
	src/modules/image/wrap_Image.cpp
	src/modules/image/wrap_Image.h
	src/modules/image/wrap_ImageData.cpp
	src/modules/image/wrap_ImageData.h
)

set(LOVE_SRC_MODULE_IMAGE_MAGPIE
	src/modules/image/magpie/CompressedData.cpp
	src/modules/image/magpie/CompressedData.h
	src/modules/image/magpie/ddsHandler.cpp
	src/modules/image/magpie/ddsHandler.h
	src/modules/image/magpie/DevilHandler.cpp
	src/modules/image/magpie/DevilHandler.h
	src/modules/image/magpie/FormatHandler.cpp
	src/modules/image/magpie/FormatHandler.h
	src/modules/image/magpie/Image.cpp
	src/modules/image/magpie/Image.h
	src/modules/image/magpie/ImageData.cpp
	src/modules/image/magpie/ImageData.h
)

set(LOVE_SRC_MODULE_IMAGE
	${LOVE_SRC_MODULE_IMAGE_ROOT}
	${LOVE_SRC_MODULE_IMAGE_MAGPIE}
)

source_group("modules\\image" FILES ${LOVE_SRC_MODULE_IMAGE_ROOT})
source_group("modules\\image\\magpie" FILES ${LOVE_SRC_MODULE_IMAGE_MAGPIE})

#
# love.joystick
#

set(LOVE_SRC_MODULE_JOYSTICK_ROOT
	src/modules/joystick/Joystick.cpp
	src/modules/joystick/Joystick.h
	src/modules/joystick/JoystickModule.h
	src/modules/joystick/wrap_Joystick.cpp
	src/modules/joystick/wrap_Joystick.h
	src/modules/joystick/wrap_JoystickModule.cpp
	src/modules/joystick/wrap_JoystickModule.h
)

set(LOVE_SRC_MODULE_JOYSTICK_SDL
	src/modules/joystick/sdl/Joystick.cpp
	src/modules/joystick/sdl/Joystick.h
	src/modules/joystick/sdl/JoystickModule.cpp
	src/modules/joystick/sdl/JoystickModule.h
)

set(LOVE_SRC_MODULE_JOYSTICK
	${LOVE_SRC_MODULE_JOYSTICK_ROOT}
	${LOVE_SRC_MODULE_JOYSTICK_SDL}
)

source_group("modules\\joystick" FILES ${LOVE_SRC_MODULE_JOYSTICK_ROOT})
source_group("modules\\joystick\\sdl" FILES ${LOVE_SRC_MODULE_JOYSTICK_SDL})

#
# love.keyboard
#

set(LOVE_SRC_MODULE_KEYBOARD_ROOT
	src/modules/keyboard/Keyboard.cpp
	src/modules/keyboard/Keyboard.h
	src/modules/keyboard/wrap_Keyboard.cpp
	src/modules/keyboard/wrap_Keyboard.h
)

set(LOVE_SRC_MODULE_KEYBOARD_SDL
	src/modules/keyboard/sdl/Keyboard.cpp
	src/modules/keyboard/sdl/Keyboard.h
)

set(LOVE_SRC_MODULE_KEYBOARD
	${LOVE_SRC_MODULE_KEYBOARD_ROOT}
	${LOVE_SRC_MODULE_KEYBOARD_SDL}
)

source_group("modules\\keyboard" FILES ${LOVE_SRC_MODULE_KEYBOARD_ROOT})
source_group("modules\\keyboard\\sdl" FILES ${LOVE_SRC_MODULE_KEYBOARD_SDL})

#
# love.math
#

set(LOVE_SRC_MODULE_MATH
	src/modules/math/BezierCurve.cpp
	src/modules/math/BezierCurve.h
	src/modules/math/MathModule.cpp
	src/modules/math/MathModule.h
	src/modules/math/RandomGenerator.cpp
	src/modules/math/RandomGenerator.h
	src/modules/math/wrap_BezierCurve.cpp
	src/modules/math/wrap_BezierCurve.h
	src/modules/math/wrap_Math.cpp
	src/modules/math/wrap_Math.h
	src/modules/math/wrap_RandomGenerator.cpp
	src/modules/math/wrap_RandomGenerator.h
)

source_group("modules\\math" FILES ${LOVE_SRC_MODULE_MATH})

#
# love (module)
#
set(LOVE_SRC_MODULE_LOVE
	src/modules/love/love.cpp
	src/modules/love/love.h
)

source_group("modules\\love" FILES ${LOVE_SRC_MODULE_LOVE})

#
# love.mouse
#

set(LOVE_SRC_MODULE_MOUSE_ROOT
	src/modules/mouse/Cursor.cpp
	src/modules/mouse/Cursor.h
	src/modules/mouse/Mouse.cpp
	src/modules/mouse/Mouse.h
	src/modules/mouse/wrap_Cursor.cpp
	src/modules/mouse/wrap_Cursor.h
	src/modules/mouse/wrap_Mouse.cpp
	src/modules/mouse/wrap_Mouse.h
)

set(LOVE_SRC_MODULE_MOUSE_SDL
	src/modules/mouse/sdl/Cursor.cpp
	src/modules/mouse/sdl/Cursor.h
	src/modules/mouse/sdl/Mouse.cpp
	src/modules/mouse/sdl/Mouse.h
)

set(LOVE_SRC_MODULE_MOUSE
	${LOVE_SRC_MODULE_MOUSE_ROOT}
	${LOVE_SRC_MODULE_MOUSE_SDL}
)

source_group("modules\\mouse" FILES ${LOVE_SRC_MODULE_MOUSE_ROOT})
source_group("modules\\mouse\\sdl" FILES ${LOVE_SRC_MODULE_MOUSE_SDL})

#
# love.physics
#

set(LOVE_SRC_MODULE_PHYSICS_ROOT
	src/modules/physics/Body.cpp
	src/modules/physics/Body.h
	src/modules/physics/Joint.cpp
	src/modules/physics/Joint.h
	src/modules/physics/Shape.cpp
	src/modules/physics/Shape.h
)

set(LOVE_SRC_MODULE_PHYSICS_BOX2D
	src/modules/physics/box2d/Body.cpp
	src/modules/physics/box2d/Body.h
	src/modules/physics/box2d/ChainShape.cpp
	src/modules/physics/box2d/ChainShape.h
	src/modules/physics/box2d/CircleShape.cpp
	src/modules/physics/box2d/CircleShape.h
	src/modules/physics/box2d/Contact.cpp
	src/modules/physics/box2d/Contact.h
	src/modules/physics/box2d/DistanceJoint.cpp
	src/modules/physics/box2d/DistanceJoint.h
	src/modules/physics/box2d/EdgeShape.cpp
	src/modules/physics/box2d/EdgeShape.h
	src/modules/physics/box2d/Fixture.cpp
	src/modules/physics/box2d/Fixture.h
	src/modules/physics/box2d/FrictionJoint.cpp
	src/modules/physics/box2d/FrictionJoint.h
	src/modules/physics/box2d/GearJoint.cpp
	src/modules/physics/box2d/GearJoint.h
	src/modules/physics/box2d/Joint.cpp
	src/modules/physics/box2d/Joint.h
	src/modules/physics/box2d/MotorJoint.cpp
	src/modules/physics/box2d/MotorJoint.h
	src/modules/physics/box2d/MouseJoint.cpp
	src/modules/physics/box2d/MouseJoint.h
	src/modules/physics/box2d/Physics.cpp
	src/modules/physics/box2d/Physics.h
	src/modules/physics/box2d/PolygonShape.cpp
	src/modules/physics/box2d/PolygonShape.h
	src/modules/physics/box2d/PrismaticJoint.cpp
	src/modules/physics/box2d/PrismaticJoint.h
	src/modules/physics/box2d/PulleyJoint.cpp
	src/modules/physics/box2d/PulleyJoint.h
	src/modules/physics/box2d/RevoluteJoint.cpp
	src/modules/physics/box2d/RevoluteJoint.h
	src/modules/physics/box2d/RopeJoint.cpp
	src/modules/physics/box2d/RopeJoint.h
	src/modules/physics/box2d/Shape.cpp
	src/modules/physics/box2d/Shape.h
	src/modules/physics/box2d/WeldJoint.cpp
	src/modules/physics/box2d/WeldJoint.h
	src/modules/physics/box2d/WheelJoint.cpp
	src/modules/physics/box2d/WheelJoint.h
	src/modules/physics/box2d/World.cpp
	src/modules/physics/box2d/World.h
	src/modules/physics/box2d/wrap_Body.cpp
	src/modules/physics/box2d/wrap_Body.h
	src/modules/physics/box2d/wrap_ChainShape.cpp
	src/modules/physics/box2d/wrap_ChainShape.h
	src/modules/physics/box2d/wrap_CircleShape.cpp
	src/modules/physics/box2d/wrap_CircleShape.h
	src/modules/physics/box2d/wrap_Contact.cpp
	src/modules/physics/box2d/wrap_Contact.h
	src/modules/physics/box2d/wrap_DistanceJoint.cpp
	src/modules/physics/box2d/wrap_DistanceJoint.h
	src/modules/physics/box2d/wrap_EdgeShape.cpp
	src/modules/physics/box2d/wrap_EdgeShape.h
	src/modules/physics/box2d/wrap_Fixture.cpp
	src/modules/physics/box2d/wrap_Fixture.h
	src/modules/physics/box2d/wrap_FrictionJoint.cpp
	src/modules/physics/box2d/wrap_FrictionJoint.h
	src/modules/physics/box2d/wrap_GearJoint.cpp
	src/modules/physics/box2d/wrap_GearJoint.h
	src/modules/physics/box2d/wrap_Joint.cpp
	src/modules/physics/box2d/wrap_Joint.h
	src/modules/physics/box2d/wrap_MotorJoint.cpp
	src/modules/physics/box2d/wrap_MotorJoint.h
	src/modules/physics/box2d/wrap_MouseJoint.cpp
	src/modules/physics/box2d/wrap_MouseJoint.h
	src/modules/physics/box2d/wrap_Physics.cpp
	src/modules/physics/box2d/wrap_Physics.h
	src/modules/physics/box2d/wrap_PolygonShape.cpp
	src/modules/physics/box2d/wrap_PolygonShape.h
	src/modules/physics/box2d/wrap_PrismaticJoint.cpp
	src/modules/physics/box2d/wrap_PrismaticJoint.h
	src/modules/physics/box2d/wrap_PulleyJoint.cpp
	src/modules/physics/box2d/wrap_PulleyJoint.h
	src/modules/physics/box2d/wrap_RevoluteJoint.cpp
	src/modules/physics/box2d/wrap_RevoluteJoint.h
	src/modules/physics/box2d/wrap_RopeJoint.cpp
	src/modules/physics/box2d/wrap_RopeJoint.h
	src/modules/physics/box2d/wrap_Shape.cpp
	src/modules/physics/box2d/wrap_Shape.h
	src/modules/physics/box2d/wrap_WeldJoint.cpp
	src/modules/physics/box2d/wrap_WeldJoint.h
	src/modules/physics/box2d/wrap_WheelJoint.cpp
	src/modules/physics/box2d/wrap_WheelJoint.h
	src/modules/physics/box2d/wrap_World.cpp
	src/modules/physics/box2d/wrap_World.h
)

set(LOVE_SRC_MODULE_PHYSICS
	${LOVE_SRC_MODULE_PHYSICS_ROOT}
	${LOVE_SRC_MODULE_PHYSICS_BOX2D}
)

source_group("modules\\physics" FILES ${LOVE_SRC_MODULE_PHYSICS_ROOT})
source_group("modules\\physics\\box2d" FILES ${LOVE_SRC_MODULE_PHYSICS_BOX2D})

#
# love.sound
#

set(LOVE_SRC_MODULE_SOUND_ROOT
	src/modules/sound/Decoder.h
	src/modules/sound/Sound.cpp
	src/modules/sound/Sound.h
	src/modules/sound/SoundData.cpp
	src/modules/sound/SoundData.h
	src/modules/sound/wrap_Decoder.cpp
	src/modules/sound/wrap_Decoder.h
	src/modules/sound/wrap_Sound.cpp
	src/modules/sound/wrap_Sound.h
	src/modules/sound/wrap_SoundData.cpp
	src/modules/sound/wrap_SoundData.h
)

set(LOVE_SRC_MODULE_SOUND_LULLABY
	src/modules/sound/lullaby/Decoder.cpp
	src/modules/sound/lullaby/Decoder.h
	src/modules/sound/lullaby/FLACDecoder.cpp
	src/modules/sound/lullaby/FLACDecoder.h
	src/modules/sound/lullaby/GmeDecoder.cpp
	src/modules/sound/lullaby/GmeDecoder.h
	src/modules/sound/lullaby/ModPlugDecoder.cpp
	src/modules/sound/lullaby/ModPlugDecoder.h
	src/modules/sound/lullaby/Mpg123Decoder.cpp
	src/modules/sound/lullaby/Mpg123Decoder.h
	src/modules/sound/lullaby/Sound.cpp
	src/modules/sound/lullaby/Sound.h
	src/modules/sound/lullaby/VorbisDecoder.cpp
	src/modules/sound/lullaby/VorbisDecoder.h
	src/modules/sound/lullaby/WaveDecoder.cpp
	src/modules/sound/lullaby/WaveDecoder.h
)

set(LOVE_SRC_MODULE_SOUND
	${LOVE_SRC_MODULE_SOUND_ROOT}
	${LOVE_SRC_MODULE_SOUND_LULLABY}
)

source_group("modules\\sound" FILES ${LOVE_SRC_MODULE_SOUND_ROOT})
source_group("modules\\sound\\lullaby" FILES ${LOVE_SRC_MODULE_SOUND_LULLABY})

#
# love.system
#

set(LOVE_SRC_MODULE_SYSTEM_ROOT
	src/modules/system/System.cpp
	src/modules/system/System.h
	src/modules/system/wrap_System.cpp
	src/modules/system/wrap_System.h
)

set(LOVE_SRC_MODULE_SYSTEM_SDL
	src/modules/system/sdl/System.cpp
	src/modules/system/sdl/System.h
)

set(LOVE_SRC_MODULE_SYSTEM
	${LOVE_SRC_MODULE_SYSTEM_ROOT}
	${LOVE_SRC_MODULE_SYSTEM_SDL}
)

source_group("modules\\system" FILES ${LOVE_SRC_MODULE_SYSTEM_ROOT})
source_group("modules\\system\\sdl" FILES ${LOVE_SRC_MODULE_SYSTEM_SDL})

#
# love.thread
#

set(LOVE_SRC_MODULE_THREAD_ROOT
	src/modules/thread/Channel.cpp
	src/modules/thread/Channel.h
	src/modules/thread/LuaThread.cpp
	src/modules/thread/LuaThread.h
	src/modules/thread/Thread.h
	src/modules/thread/ThreadModule.cpp
	src/modules/thread/ThreadModule.h
	src/modules/thread/threads.cpp
	src/modules/thread/threads.h
	src/modules/thread/wrap_Channel.cpp
	src/modules/thread/wrap_Channel.h
	src/modules/thread/wrap_LuaThread.cpp
	src/modules/thread/wrap_LuaThread.h
	src/modules/thread/wrap_ThreadModule.cpp
	src/modules/thread/wrap_ThreadModule.h
)

set(LOVE_SRC_MODULE_THREAD_SDL
	src/modules/thread/sdl/Thread.cpp
	src/modules/thread/sdl/Thread.h
	src/modules/thread/sdl/threads.cpp
	src/modules/thread/sdl/threads.h
)

set(LOVE_SRC_MODULE_THREAD
	${LOVE_SRC_MODULE_THREAD_ROOT}
	${LOVE_SRC_MODULE_THREAD_SDL}
)

source_group("modules\\thread" FILES ${LOVE_SRC_MODULE_THREAD_ROOT})
source_group("modules\\thread\\sdl" FILES ${LOVE_SRC_MODULE_THREAD_SDL})

#
# love.timer
#

set(LOVE_SRC_MODULE_TIMER_ROOT
	src/modules/timer/Timer.h
	src/modules/timer/wrap_Timer.cpp
	src/modules/timer/wrap_Timer.h
)

set(LOVE_SRC_MODULE_TIMER_SDL
	src/modules/timer/sdl/Timer.cpp
	src/modules/timer/sdl/Timer.h
)

set(LOVE_SRC_MODULE_TIMER
	${LOVE_SRC_MODULE_TIMER_ROOT}
	${LOVE_SRC_MODULE_TIMER_SDL}
)

source_group("modules\\timer" FILES ${LOVE_SRC_MODULE_TIMER_ROOT})
source_group("modules\\timer\\sdl" FILES ${LOVE_SRC_MODULE_TIMER_SDL})

#
# love.window
#

set(LOVE_SRC_MODULE_WINDOW_ROOT
	src/modules/window/Window.cpp
	src/modules/window/Window.h
	src/modules/window/wrap_Window.cpp
	src/modules/window/wrap_Window.h
)

set(LOVE_SRC_MODULE_WINDOW_SDL
	src/modules/window/sdl/Window.cpp
	src/modules/window/sdl/Window.h
)

set(LOVE_SRC_MODULE_WINDOW
	${LOVE_SRC_MODULE_WINDOW_ROOT}
	${LOVE_SRC_MODULE_WINDOW_SDL}
)

source_group("modules\\window" FILES ${LOVE_SRC_MODULE_WINDOW_ROOT})
source_group("modules\\window\\sdl" FILES ${LOVE_SRC_MODULE_WINDOW_SDL})

###################################
# Third-party libraries
###################################

#
# Box2D
#

set(LOVE_SRC_3P_BOX2D_ROOT
	src/libraries/Box2D/Box2D.h
)

set(LOVE_SRC_3P_BOX2D_COLLISION
	src/libraries/Box2D/Collision/b2BroadPhase.cpp
	src/libraries/Box2D/Collision/b2BroadPhase.h
	src/libraries/Box2D/Collision/b2CollideCircle.cpp
	src/libraries/Box2D/Collision/b2CollideEdge.cpp
	src/libraries/Box2D/Collision/b2CollidePolygon.cpp
	src/libraries/Box2D/Collision/b2Collision.cpp
	src/libraries/Box2D/Collision/b2Collision.h
	src/libraries/Box2D/Collision/b2Distance.cpp
	src/libraries/Box2D/Collision/b2Distance.h
	src/libraries/Box2D/Collision/b2DynamicTree.cpp
	src/libraries/Box2D/Collision/b2DynamicTree.h
	src/libraries/Box2D/Collision/b2TimeOfImpact.cpp
	src/libraries/Box2D/Collision/b2TimeOfImpact.h
)

set(LOVE_SRC_3P_BOX2D_COLLISION_SHAPES
	src/libraries/Box2D/Collision/Shapes/b2ChainShape.cpp
	src/libraries/Box2D/Collision/Shapes/b2ChainShape.h
	src/libraries/Box2D/Collision/Shapes/b2CircleShape.cpp
	src/libraries/Box2D/Collision/Shapes/b2CircleShape.h
	src/libraries/Box2D/Collision/Shapes/b2EdgeShape.cpp
	src/libraries/Box2D/Collision/Shapes/b2EdgeShape.h
	src/libraries/Box2D/Collision/Shapes/b2PolygonShape.cpp
	src/libraries/Box2D/Collision/Shapes/b2PolygonShape.h
	src/libraries/Box2D/Collision/Shapes/b2Shape.h
)

set(LOVE_SRC_3P_BOX2D_COMMON
	src/libraries/Box2D/Common/b2BlockAllocator.cpp
	src/libraries/Box2D/Common/b2BlockAllocator.h
	src/libraries/Box2D/Common/b2Draw.cpp
	src/libraries/Box2D/Common/b2Draw.h
	src/libraries/Box2D/Common/b2GrowableStack.h
	src/libraries/Box2D/Common/b2Math.cpp
	src/libraries/Box2D/Common/b2Math.h
	src/libraries/Box2D/Common/b2Settings.cpp
	src/libraries/Box2D/Common/b2Settings.h
	src/libraries/Box2D/Common/b2StackAllocator.cpp
	src/libraries/Box2D/Common/b2StackAllocator.h
	src/libraries/Box2D/Common/b2Timer.cpp
	src/libraries/Box2D/Common/b2Timer.h
)

set(LOVE_SRC_3P_BOX2D_DYNAMICS
	src/libraries/Box2D/Dynamics/b2Body.cpp
	src/libraries/Box2D/Dynamics/b2Body.h
	src/libraries/Box2D/Dynamics/b2ContactManager.cpp
	src/libraries/Box2D/Dynamics/b2ContactManager.h
	src/libraries/Box2D/Dynamics/b2Fixture.cpp
	src/libraries/Box2D/Dynamics/b2Fixture.h
	src/libraries/Box2D/Dynamics/b2Island.cpp
	src/libraries/Box2D/Dynamics/b2Island.h
	src/libraries/Box2D/Dynamics/b2TimeStep.h
	src/libraries/Box2D/Dynamics/b2World.cpp
	src/libraries/Box2D/Dynamics/b2World.h
	src/libraries/Box2D/Dynamics/b2WorldCallbacks.cpp
	src/libraries/Box2D/Dynamics/b2WorldCallbacks.h
)

set(LOVE_SRC_3P_BOX2D_DYNAMICS_CONTACTS
	src/libraries/Box2D/Dynamics/Contacts/b2ChainAndCircleContact.cpp
	src/libraries/Box2D/Dynamics/Contacts/b2ChainAndCircleContact.h
	src/libraries/Box2D/Dynamics/Contacts/b2ChainAndPolygonContact.cpp
	src/libraries/Box2D/Dynamics/Contacts/b2ChainAndPolygonContact.h
	src/libraries/Box2D/Dynamics/Contacts/b2CircleContact.cpp
	src/libraries/Box2D/Dynamics/Contacts/b2CircleContact.h
	src/libraries/Box2D/Dynamics/Contacts/b2Contact.cpp
	src/libraries/Box2D/Dynamics/Contacts/b2Contact.h
	src/libraries/Box2D/Dynamics/Contacts/b2ContactSolver.cpp
	src/libraries/Box2D/Dynamics/Contacts/b2ContactSolver.h
	src/libraries/Box2D/Dynamics/Contacts/b2EdgeAndCircleContact.cpp
	src/libraries/Box2D/Dynamics/Contacts/b2EdgeAndCircleContact.h
	src/libraries/Box2D/Dynamics/Contacts/b2EdgeAndPolygonContact.cpp
	src/libraries/Box2D/Dynamics/Contacts/b2EdgeAndPolygonContact.h
	src/libraries/Box2D/Dynamics/Contacts/b2PolygonAndCircleContact.cpp
	src/libraries/Box2D/Dynamics/Contacts/b2PolygonAndCircleContact.h
	src/libraries/Box2D/Dynamics/Contacts/b2PolygonContact.cpp
	src/libraries/Box2D/Dynamics/Contacts/b2PolygonContact.h
)

set(LOVE_SRC_3P_BOX2D_DYNAMICS_JOINTS
	src/libraries/Box2D/Dynamics/Joints/b2DistanceJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2DistanceJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2FrictionJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2FrictionJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2GearJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2GearJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2Joint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2Joint.h
	src/libraries/Box2D/Dynamics/Joints/b2MotorJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2MotorJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2MouseJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2MouseJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2PrismaticJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2PrismaticJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2PulleyJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2PulleyJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2RevoluteJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2RevoluteJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2RopeJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2RopeJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2WeldJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2WeldJoint.h
	src/libraries/Box2D/Dynamics/Joints/b2WheelJoint.cpp
	src/libraries/Box2D/Dynamics/Joints/b2WheelJoint.h
)

set(LOVE_SRC_3P_BOX2D_ROPE
	src/libraries/Box2D/Rope/b2Rope.cpp
	src/libraries/Box2D/Rope/b2Rope.h
)

set(LOVE_SRC_3P_BOX2D
	${LOVE_SRC_3P_BOX2D_ROOT}
	${LOVE_SRC_3P_BOX2D_COLLISION}
	${LOVE_SRC_3P_BOX2D_COLLISION_SHAPES}
	${LOVE_SRC_3P_BOX2D_COMMON}
	${LOVE_SRC_3P_BOX2D_DYNAMICS}
	${LOVE_SRC_3P_BOX2D_DYNAMICS_CONTACTS}
	${LOVE_SRC_3P_BOX2D_DYNAMICS_JOINTS}
	${LOVE_SRC_3P_BOX2D_ROPE}
)

add_library(love_3p_box2d ${LOVE_SRC_3P_BOX2D})

#
# ddsparse
#

set(LOVE_SRC_3P_DDSPARSE
	src/libraries/ddsparse/ddsinfo.h
	src/libraries/ddsparse/ddsparse.cpp
	src/libraries/ddsparse/ddsparse.h
)

add_library(love_3p_ddsparse ${LOVE_SRC_3P_DDSPARSE})

#
# enet
#

set(LOVE_SRC_3P_ENET_ROOT
	src/libraries/enet/enet.cpp
	src/libraries/enet/lua-enet.h
)

set(LOVE_SRC_3P_ENET_LIBENET
	src/libraries/enet/libenet/callbacks.c
	src/libraries/enet/libenet/compress.c
	src/libraries/enet/libenet/host.c
	src/libraries/enet/libenet/list.c
	src/libraries/enet/libenet/packet.c
	src/libraries/enet/libenet/peer.c
	src/libraries/enet/libenet/protocol.c
	src/libraries/enet/libenet/unix.c
	src/libraries/enet/libenet/win32.c
)

set(LOVE_SRC_3P_ENET_LIBENET_INCLUDE_ENET
	src/libraries/enet/libenet/include/enet/enet.h
	src/libraries/enet/libenet/include/enet/list.h
	src/libraries/enet/libenet/include/enet/protocol.h
	src/libraries/enet/libenet/include/enet/time.h
	src/libraries/enet/libenet/include/enet/types.h
	src/libraries/enet/libenet/include/enet/unix.h
	src/libraries/enet/libenet/include/enet/utility.h
	src/libraries/enet/libenet/include/enet/win32.h
)

set(LOVE_SRC_3P_ENET
	${LOVE_SRC_3P_ENET_ROOT}
	${LOVE_SRC_3P_ENET_LIBENET}
	${LOVE_SRC_3P_ENET_LIBENET_INCLUDE_ENET}
)

add_library(love_3p_enet ${LOVE_SRC_3P_ENET})
target_link_libraries(love_3p_enet ${MEGA_LUA})
target_include_directories(love_3p_enet PUBLIC src/libraries/enet/libenet/include)

#
# luasocket
#

set(LOVE_SRC_3P_LUASOCKET_ROOT
	src/libraries/luasocket/luasocket.cpp
	src/libraries/luasocket/luasocket.h
)

set(LOVE_SRC_3P_LUASOCKET_LIBLUASOCKET
	src/libraries/luasocket/libluasocket/auxiliar.c
	src/libraries/luasocket/libluasocket/auxiliar.h
	src/libraries/luasocket/libluasocket/buffer.c
	src/libraries/luasocket/libluasocket/buffer.h
	src/libraries/luasocket/libluasocket/except.c
	src/libraries/luasocket/libluasocket/except.h
	src/libraries/luasocket/libluasocket/ftp.lua.h
	src/libraries/luasocket/libluasocket/http.lua.h
	src/libraries/luasocket/libluasocket/inet.c
	src/libraries/luasocket/libluasocket/inet.h
	src/libraries/luasocket/libluasocket/io.c
	src/libraries/luasocket/libluasocket/io.h
	src/libraries/luasocket/libluasocket/ltn12.lua.h
	src/libraries/luasocket/libluasocket/lua.h
	src/libraries/luasocket/libluasocket/luasocket.c
	src/libraries/luasocket/libluasocket/luasocket.h
	src/libraries/luasocket/libluasocket/mime.c
	src/libraries/luasocket/libluasocket/mime.h
	src/libraries/luasocket/libluasocket/mime.lua.h
	src/libraries/luasocket/libluasocket/options.c
	src/libraries/luasocket/libluasocket/options.h
	src/libraries/luasocket/libluasocket/select.c
	src/libraries/luasocket/libluasocket/select.h
	src/libraries/luasocket/libluasocket/smtp.lua.h
	src/libraries/luasocket/libluasocket/socket.h
	src/libraries/luasocket/libluasocket/socket.lua.h
	src/libraries/luasocket/libluasocket/tcp.c
	src/libraries/luasocket/libluasocket/tcp.h
	src/libraries/luasocket/libluasocket/timeout.c
	src/libraries/luasocket/libluasocket/timeout.h
	src/libraries/luasocket/libluasocket/tp.lua.h
	src/libraries/luasocket/libluasocket/udp.c
	src/libraries/luasocket/libluasocket/udp.h
	src/libraries/luasocket/libluasocket/url.lua.h
)

if(MSVC)
	set(LOVE_SRC_3P_LUASOCKET_LIBLUASOCKET
		${LOVE_SRC_3P_LUASOCKET_LIBLUASOCKET}
		src/libraries/luasocket/libluasocket/wsocket.c
		src/libraries/luasocket/libluasocket/wsocket.h
	)
endif()

set(LOVE_SRC_3P_LUASOCKET
	${LOVE_SRC_3P_LUASOCKET_ROOT}
	${LOVE_SRC_3P_LUASOCKET_LIBLUASOCKET}
)

add_library(love_3p_luasocket ${LOVE_SRC_3P_LUASOCKET})
target_link_libraries(love_3p_luasocket ${MEGA_LUA})

#
# noise1234
#

set(LOVE_SRC_3P_NOISE1234
	src/libraries/noise1234/simplexnoise1234.cpp
	src/libraries/noise1234/simplexnoise1234.h
)

add_library(love_3p_noise1234 ${LOVE_SRC_3P_NOISE1234})

#
# utf8
#

set(LOVE_SRC_3P_UTF8_ROOT src/libraries/utf8/utf8.h)

set(LOVE_SRC_3P_UTF8_UTF8
	src/libraries/utf8/utf8/checked.h
	src/libraries/utf8/utf8/core.h
	src/libraries/utf8/utf8/unchecked.h
)

set(LOVE_SRC_3P_UTF8
	${LOVE_SRC_3P_UTF8_ROOT}
	${LOVE_SRC_3P_UTF8_UTF8}
)

# This library is all headers ... so there is no need to
# add_library() here.

#
# Wuff
#

set(LOVE_SRC_3P_WUFF
	src/libraries/Wuff/wuff.c
	src/libraries/Wuff/wuff.h
	src/libraries/Wuff/wuff_config.h
	src/libraries/Wuff/wuff_convert.c
	src/libraries/Wuff/wuff_convert.h
	src/libraries/Wuff/wuff_internal.c
	src/libraries/Wuff/wuff_internal.h
	src/libraries/Wuff/wuff_memory.c
)

add_library(love_3p_wuff ${LOVE_SRC_3P_WUFF})

set(LOVE_3P
	love_3p_box2d
	love_3p_ddsparse
	love_3p_enet
	love_3p_luasocket
	love_3p_noise1234
	love_3p_wuff
)

disable_warnings(love_3p_box2d love_3p_enet love_3p_luasocket)

#
# liblove
#
set(LOVE_LIB_SRC
	${LOVE_SRC_COMMON}
	# Modules
	${LOVE_SRC_MODULE_AUDIO}
	${LOVE_SRC_MODULE_EVENT}
	${LOVE_SRC_MODULE_FILESYSTEM}
	${LOVE_SRC_MODULE_FONT}
	${LOVE_SRC_MODULE_GRAPHICS}
	${LOVE_SRC_MODULE_IMAGE}
	${LOVE_SRC_MODULE_JOYSTICK}
	${LOVE_SRC_MODULE_KEYBOARD}
	${LOVE_SRC_MODULE_LOVE}
	${LOVE_SRC_MODULE_MATH}
	${LOVE_SRC_MODULE_MOUSE}
	${LOVE_SRC_MODULE_PHYSICS}
	${LOVE_SRC_MODULE_SOUND}
	${LOVE_SRC_MODULE_SYSTEM}
	${LOVE_SRC_MODULE_THREAD}
	${LOVE_SRC_MODULE_TIMER}
	${LOVE_SRC_MODULE_WINDOW}
)

include_directories(
	src
	src/libraries
	src/modules
	${MEGA_EXTRA_INCLUDE}
)

# SDL2 links with some DirectX libraries, and we apparently also
# pull those libraries in for linkage because we link with SDL2.
link_directories(${SDL_LINK_DIR})

set(LOVE_MEGA_3P
	${MEGA_DEVIL}
	${MEGA_FREETYPE}
	${MEGA_JASPER}
	${MEGA_JPEG}
	${MEGA_LIBOGG}
	${MEGA_LIBPNG}
	${MEGA_LIBVORBISFILE}
	${MEGA_LIBVORBIS}
	${MEGA_LUA}
	${MEGA_MODPLUG}
	${MEGA_MPEG123}
	${MEGA_OPENAL}
	${MEGA_PHYSFS}
	${MEGA_SDL2MAIN}
	${MEGA_SDL2}
	${MEGA_TIFF}
	${MEGA_ZLIB}
)

set(LOVE_LINK_LIBRARIES
	${OPENGL_gl_LIBRARY}
	${LOVE_MEGA_3P}
)

set(LOVE_RC)

if(MSVC)
	set(LOVE_LINK_LIBRARIES ${LOVE_LINK_LIBRARIES}
		ws2_32.lib
		winmm.lib
	)

	set(LOVE_RC
		extra/windows/love.rc
		extra/windows/love.ico
	)
endif()

add_library(${LOVE_LIB_NAME} SHARED ${LOVE_LIB_SRC} ${LOVE_RC})
target_link_libraries(liblove ${LOVE_LINK_LIBRARIES} ${LOVE_3P})

if(MEGA_EXTRA_DEPENDECIES)
	add_dependencies(${LOVE_LIB_NAME} ${MEGA_EXTRA_DEPENDECIES})
endif()

if(MSVC)
	set_target_properties(${LOVE_LIB_NAME} PROPERTIES RELEASE_OUTPUT_NAME "love" PDB_NAME "liblove")
	set_target_properties(${LOVE_LIB_NAME} PROPERTIES DEBUG_OUTPUT_NAME "love" PDB_NAME "liblove")
endif()

#
# love (executable)
#
add_executable(${LOVE_EXE_NAME} WIN32 src/love.cpp ${LOVE_RC})
target_link_libraries(love liblove)

# Add post build steps to move the DLLs next to the binary. Otherwise
# running/debugging the binary will not work from inside VS.
add_move_dll(love ${MEGA_MPEG123})
add_move_dll(love ${MEGA_SDL2})
add_move_dll(love ${MEGA_OPENAL})
add_move_dll(love ${MEGA_DEVIL})

if(LOVE_JIT)
	add_move_file(love ${MEGA_LUAJIT_DLL})
else()
	add_move_dll(love ${MEGA_LUA51})
endif()

###################################
# Version
###################################

# Extract version.h contents.
file(READ ${CMAKE_CURRENT_SOURCE_DIR}/src/common/version.h LOVE_VERSION_FILE_CONTENTS)

# Extract one of LOVE_VERSION_MAJOR/MINOR/REV.
function(match_version ARG_STRING OUT_VAR)
	string(REGEX MATCH "VERSION_${ARG_STRING} = ([0-9]+);" TMP_VER "${LOVE_VERSION_FILE_CONTENTS}")
	string(REGEX MATCH "[0-9]+" TMP_VER "${TMP_VER}")
	set(${OUT_VAR} ${TMP_VER} PARENT_SCOPE)
endfunction()

match_version("MAJOR" LOVE_VERSION_MAJOR)
match_version("MINOR" LOVE_VERSION_MINOR)
match_version("REV" LOVE_VERSION_REV)

set(LOVE_VERSION_STR "${LOVE_VERSION_MAJOR}.${LOVE_VERSION_MINOR}.${LOVE_VERSION_REV}")

message(STATUS "Version: ${LOVE_VERSION_STR}")

###################################
# CPack
###################################
install(TARGETS love ${LOVE_LIB_NAME} RUNTIME DESTINATION .)

# Extra DLLs.
if(MEGA_EXTRA_DLLS)
	foreach(DLL ${MEGA_EXTRA_DLLS})
		get_filename_component(DLL_NAME ${DLL} NAME)
		message(STATUS "Extra DLL: ${DLL_NAME}")
	endforeach()
	install(FILES ${MEGA_EXTRA_DLLS} DESTINATION .)
endif()

# Dynamic runtime libs.
if(MEGA_MSVC_DLLS)
	foreach(DLL ${MEGA_MSVC_DLLS})
		get_filename_component(DLL_NAME ${DLL} NAME)
		message(STATUS "Runtime DLL: ${DLL_NAME}")
	endforeach()
	install(FILES ${MEGA_MSVC_DLLS} DESTINATION .)
endif()

# Copy a text file from CMAKE_CURRENT_SOURCE_DIR to CMAKE_CURRENT_BINARY_DIR.
# On Windows, this function will convert line endings to CR,LF.
function(copy_text_file ARG_FILE_IN ARG_FILE_OUT)
	file(READ ${CMAKE_CURRENT_SOURCE_DIR}/${ARG_FILE_IN} TMP_TXT_CONTENTS)
	file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/${ARG_FILE_OUT} ${TMP_TXT_CONTENTS})
endfunction()

# Text files.
copy_text_file(readme.md readme.txt)
copy_text_file(license.txt license.txt)
copy_text_file(changes.txt changes.txt)

install(FILES
		${CMAKE_CURRENT_BINARY_DIR}/changes.txt
		${CMAKE_CURRENT_BINARY_DIR}/license.txt
		${CMAKE_CURRENT_BINARY_DIR}/readme.txt
		DESTINATION .)

# Icons
install(FILES
		${CMAKE_CURRENT_SOURCE_DIR}/extra/nsis/love.ico
		${CMAKE_CURRENT_SOURCE_DIR}/extra/nsis/game.ico
		DESTINATION .)

set(CPACK_GENERATOR ZIP NSIS)
set(CPACK_PACKAGE_NAME "love")
set(CPACK_PACKAGE_VENDOR "love2d.org")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "LOVE -- It's awesome")
set(CPACK_PACKAGE_VERSION "${LOVE_VERSION_STR}")
set(CPACK_PACKAGE_VERSION_MAJOR "${LOVE_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${LOVE_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${LOVE_VERSION_REV}")
set(CPACK_PACKAGE_INSTALL_DIRECTORY "LOVE")
set(CPACK_PACKAGE_EXECUTABLES "love;LOVE")
set(CPACK_RESOURCE_FILE_README "${CMAKE_CURRENT_SOURCE_DIR}/readme.md")
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/license.txt")

set(CPACK_NSIS_EXECUTABLES_DIRECTORY .)
set(CPACK_NSIS_PACKAGE_NAME "LOVE")
set(CPACK_NSIS_DISPLAY_NAME "LÖVE ${LOVE_VERSION_STR}")
set(CPACK_NSIS_MODIFY_PATH OFF)

if(LOVE_X64)
	set(CPACK_NSIS_INSTALL_ROOT "$PROGRAMFILES64")
else()
	set(CPACK_NSIS_INSTALL_ROOT "$PROGRAMFILES")
endif()

set(CPACK_NSIS_MENU_LINKS "http://love2d.org/wiki" "Documentation")

# Some bug somewhere in NSIS requires "\\\\" somewhere in the path,
# according to The Internet. (And sure enough, it does not work
# without it).
set(NSIS_LEFT_BMP "${CMAKE_CURRENT_SOURCE_DIR}/extra/nsis\\\\left.bmp")
set(NSIS_TOP_BMP "${CMAKE_CURRENT_SOURCE_DIR}/extra/nsis\\\\top.bmp")
set(NSIS_MUI_ICON "${CMAKE_CURRENT_SOURCE_DIR}/extra/nsis\\\\love.ico")
set(NSIS_MUI_UNICON "${CMAKE_CURRENT_SOURCE_DIR}/extra/nsis\\\\love.ico")

set(CPACK_NSIS_INSTALLER_MUI_ICON_CODE "
	!define MUI_WELCOMEPAGE_TITLE \\\"LÖVE ${LOVE_VERSION_STR} Setup\\\"
	!define MUI_WELCOMEFINISHPAGE_BITMAP \\\"${NSIS_LEFT_BMP}\\\"
	!define MUI_HEADERIMAGE_BITMAP \\\"${NSIS_TOP_BMP}\\\"
	!define MUI_ICON \\\"${NSIS_MUI_ICON}\\\"
	!define MUI_UNICON \\\"${NSIS_MUI_UNICON}\\\"
")

set(CPACK_NSIS_EXTRA_INSTALL_COMMANDS "
	WriteRegStr HKCR \\\".love\\\" \\\"\\\" \\\"LOVE\\\"
	WriteRegStr HKCR \\\"LOVE\\\" \\\"\\\" \\\"LOVE Game File\\\"
	WriteRegStr HKCR \\\"LOVE\\\\DefaultIcon\\\" \\\"\\\" \\\"$INSTDIR\\\\game.ico\\\"
	WriteRegStr HKCR \\\"LOVE\\\\shell\\\" \\\"\\\" \\\"open\\\"
	WriteRegStr HKCR \\\"LOVE\\\\shell\\\\open\\\" \\\"\\\" \\\"Open in LOVE\\\"
	WriteRegStr HKCR \\\"LOVE\\\\shell\\\\open\\\\command\\\" \\\"\\\" \\\"$INSTDIR\\\\love.exe $\\\\\\\"%1$\\\\\\\"\\\"
	System::Call 'shell32.dll::SHChangeNotify(i, i, i, i) v  (0x08000000, 0, 0, 0)'
")

set(CPACK_NSIS_EXTRA_UNINSTALL_COMMANDS "
	DeleteRegKey HKCR \\\"LOVE\\\"
	DeleteRegKey HKCR \\\".love\\\"
	System::Call 'shell32.dll::SHChangeNotify(i, i, i, i) v  (0x08000000, 0, 0, 0)'
")

include(CPack)
