cmake_minimum_required(VERSION 3.10)

project(SyntheticData)

find_package(unofficial-sqlite3 CONFIG REQUIRED)

add_library(csv-parser STATIC
    3rdParty/csv-parser/csv_parser.hpp
    3rdParty/csv-parser/csv_parser.cpp
)
target_include_directories(csv-parser INTERFACE 3rdParty/csv-parser)

add_library(irig106lib STATIC
    3rdParty/irig106lib/src/i106_data_stream.c
    3rdParty/irig106lib/src/i106_decode_1553f1.c
    3rdParty/irig106lib/src/i106_decode_16pp194.c
    3rdParty/irig106lib/src/i106_decode_arinc429.c
    3rdParty/irig106lib/src/i106_decode_can.c
    3rdParty/irig106lib/src/i106_decode_discrete.c
    3rdParty/irig106lib/src/i106_decode_ethernet.c
    3rdParty/irig106lib/src/i106_decode_index.c
    3rdParty/irig106lib/src/i106_decode_pcmf1.c
    3rdParty/irig106lib/src/i106_decode_time.c
    3rdParty/irig106lib/src/i106_decode_tmats.c
    3rdParty/irig106lib/src/i106_decode_tmats_b.c
    3rdParty/irig106lib/src/i106_decode_tmats_c.c
    3rdParty/irig106lib/src/i106_decode_tmats_d.c
    3rdParty/irig106lib/src/i106_decode_tmats_g.c
    3rdParty/irig106lib/src/i106_decode_tmats_m.c
    3rdParty/irig106lib/src/i106_decode_tmats_p.c
    3rdParty/irig106lib/src/i106_decode_tmats_r.c
    3rdParty/irig106lib/src/i106_decode_uart.c
    3rdParty/irig106lib/src/i106_decode_video.c
    3rdParty/irig106lib/src/i106_index.c
    3rdParty/irig106lib/src/i106_time.c
    3rdParty/irig106lib/src/irig106ch10.c
    3rdParty/irig106lib/src/sha-256.c
)
target_include_directories(irig106lib INTERFACE 3rdParty/irig106lib/src)

add_executable(BM6toDB
    BM6toDB/BM6toDB.cpp
    SynthCh10Gen/Source_BMNavTxt.cpp
)
target_include_directories(BM6toDB PRIVATE SynthCh10Gen)
target_link_libraries(BM6toDB PRIVATE csv-parser unofficial::sqlite3::sqlite3)

add_executable(BM6toXPlaneVideo
    BM6toXPlaneVideo/DXGIManager.cpp
    BM6toXPlaneVideo/MpegEncoder.cpp
    XPlaneControl/XPlaneControl.cpp
    SynthCh10Gen/Source_BMNavDB.cpp
    SynthCh10Gen/Source_BMNavTxt.cpp
    BM6toXPlaneVideo/BM6toXPlaneVideo.cpp
)
find_package(FFMPEG REQUIRED)
target_include_directories(BM6toXPlaneVideo PRIVATE SynthCh10Gen XPlaneControl ${FFMPEG_INCLUDE_DIRS})
target_link_libraries(BM6toXPlaneVideo PRIVATE csv-parser unofficial::sqlite3::sqlite3 ${FFMPEG_LIBRARIES})

add_executable(SynthCh10Gen-BM6
    SynthCh10Gen/Ch10Format_A429.cpp
    SynthCh10Gen/Ch10Format_A429_AR100.cpp
    SynthCh10Gen/Ch10Format_PCM.cpp
    SynthCh10Gen/Ch10Writer_A429.cpp
    SynthCh10Gen/Ch10Writer_Index.cpp
    SynthCh10Gen/Ch10Writer_PCM.cpp
    SynthCh10Gen/Ch10Writer_Video.cpp
    SynthCh10Gen/Source_BMNavDB.cpp
    SynthCh10Gen/Source_BMNavTxt.cpp
    SynthCh10Gen/Source_CsvTxt.cpp
    SynthCh10Gen/Source_VideoDB.cpp
    SynthCh10Gen/SynthCh10Gen.cpp
    SynthCh10Gen/Ch10Format_1553.cpp
    SynthCh10Gen/Ch10Format_1553_Nav.cpp
    SynthCh10Gen/Ch10Writer_1553.cpp
    SynthCh10Gen/Ch10Writer_Time.cpp
)
target_include_directories(SynthCh10Gen-BM6 PRIVATE)
target_compile_definitions(SynthCh10Gen-BM6 PRIVATE COMPILE_BLUEMAX6)
target_link_libraries(SynthCh10Gen-BM6 PRIVATE csv-parser irig106lib unofficial::sqlite3::sqlite3)

add_executable(SynthCh10Gen-NASA
    SynthCh10Gen/Ch10Format_A429.cpp
    SynthCh10Gen/Ch10Format_A429_AR100.cpp
    SynthCh10Gen/Ch10Format_PCM.cpp
    SynthCh10Gen/Ch10Writer_A429.cpp
    SynthCh10Gen/Ch10Writer_Index.cpp
    SynthCh10Gen/Ch10Writer_PCM.cpp
    SynthCh10Gen/Ch10Writer_Video.cpp
    SynthCh10Gen/Source_CsvTxt.cpp
    SynthCh10Gen/Source_NasaNavTxt.cpp
    SynthCh10Gen/SynthCh10Gen.cpp
    SynthCh10Gen/Ch10Format_1553.cpp
    SynthCh10Gen/Ch10Format_1553_Nav.cpp
    SynthCh10Gen/Ch10Writer_1553.cpp
    SynthCh10Gen/Ch10Writer_Time.cpp
)
target_include_directories(SynthCh10Gen-NASA PRIVATE)
target_link_libraries(SynthCh10Gen-NASA PRIVATE csv-parser irig106lib unofficial::sqlite3::sqlite3)
target_compile_definitions(SynthCh10Gen-NASA PRIVATE COMPILE_NASA)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
find_package(Qt5 CONFIG COMPONENTS Widgets)
add_executable(XPlaneControl
    XPlaneControl/main.cpp
    XPlaneControl/XPlaneControl_gui.h
    XPlaneControl/XPlaneControl_gui.cpp
    XPlaneControl/XPlaneControl.h
    XPlaneControl/XPlaneControl.cpp
    XPlaneControl/XPlaneControl.qrc
    XPlaneControl/XPlaneControl.ui
)
target_link_libraries(XPlaneControl PRIVATE Qt5::Widgets)