cmake_minimum_required(VERSION 3.10)

project(adapt-ffb-joy C)

#add_subdirectory(LUFA-120219/LUFA)

set(ARCH AVR8)

set(F_CPU 16000000)
add_definitions(
        -DF_CPU=${F_CPU}
        -DF_USB=${F_CPU}
)

# CPU, you can find the list here:
# https://gcc.gnu.org/onlinedocs/gcc/AVR-Options.html
set(MCU atmega32u4)
set(BOARD TEENSY2)
set(BAUD 115200)

# The programmer to use, read avrdude manual for list
set(PROG_TYPE arduino)

# AVR Fuses, must be in concordance with your hardware and F_CPU
# http://eleccelerator.com/fusecalc/fusecalc.php?chip=atmega328p
#set(E_FUSE 0xfd)
#set(H_FUSE 0xda)
#set(L_FUSE 0xfd)
#set(LOCK_BIT 0xff)

set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_CXX_COMPILER avr-g++)
set(CMAKE_C_COMPILER avr-gcc)
set(CMAKE_ASM_COMPILER avr-gcc)


add_definitions(
        -DF_CPU=${F_CPU}
        -DBAUD=${BAUD}
        -DBOARD=${BOARD}
)

# LUFA defs
add_definitions(
        -DUSB_DEVICE_ONLY
        -DFIXED_CONTROL_ENDPOINT_SIZE=8
        -DFIXED_NUM_CONFIGURATIONS=1
        -DUSE_FLASH_DESCRIPTORS
        -DUSE_STATIC_OPTIONS="\(USB_DEVICE_OPT_FULLSPEED | USB_OPT_REG_ENABLED | USB_OPT_AUTO_PLL\)"
)

add_compile_options(
        -mmcu=${MCU} # MCU
        -std=gnu99 # C99 standard
        -Os # optimize
        -funsigned-char
        -funsigned-bitfields
        -ffunction-sections
        -fno-inline-small-functions
        -fpack-struct
        -fshort-enums
        -fno-strict-aliasing
        -Wall
        -Wstrict-prototypes

        #        -Wall # enable warnings
        #        -Wno-main
        #        -Wundef
        #        -pedantic
        #        -Wstrict-prototypes
        #        -Werror
        #        -Wfatal-errors
        #        -Wl,--relax,--gc-sections
        #        -g
        #        -gdwarf-2
        #        -funsigned-char # a few optimizations
        #        -funsigned-bitfields
        #        -fpack-struct
        #        -fshort-enums
        #        -ffunction-sections
        #        -fdata-sections
        #        -fno-split-wide-types
        #        -fno-tree-scev-cprop
)

set(SRC_FILES
        Joystick.c
        Descriptors.c
        main.c
        ffb.c
        ffb-pro.c
        ffb-wheel.c
        3DPro.c
        debug.c
)

# LUFA sources
set(LUFA_PATH ${CMAKE_CURRENT_SOURCE_DIR}/LUFA-120219)
set(LUFA_ROOT_PATH ${LUFA_PATH}/LUFA)

set(LUFA_SRC_USB
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/${ARCH}/Device_${ARCH}.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/${ARCH}/Endpoint_${ARCH}.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/${ARCH}/Host_${ARCH}.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/${ARCH}/Pipe_${ARCH}.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/${ARCH}/USBController_${ARCH}.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/${ARCH}/USBInterrupt_${ARCH}.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/${ARCH}/EndpointStream_${ARCH}.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/${ARCH}/PipeStream_${ARCH}.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/ConfigDescriptor.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/DeviceStandardReq.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/Events.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/HostStandardReq.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Core/USBTask.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Common/HIDParser.c
)

set(LUFA_SRC_USBCLASS
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Device/AudioClassDevice.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Device/CDCClassDevice.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Device/HIDClassDevice.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Device/MassStorageClassDevice.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Device/MIDIClassDevice.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Device/RNDISClassDevice.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Host/AndroidAccessoryClassHost.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Host/AudioClassHost.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Host/CDCClassHost.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Host/HIDClassHost.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Host/MassStorageClassHost.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Host/MIDIClassHost.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Host/PrinterClassHost.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Host/RNDISClassHost.c
        ${LUFA_ROOT_PATH}/Drivers/USB/Class/Host/StillImageClassHost.c
)

set(LUFA_SRC_TEMPERATURE ${LUFA_ROOT_PATH}/Drivers/Board/Temperature.c)
set(LUFA_SRC_SERIAL ${LUFA_ROOT_PATH}/Drivers/Peripheral/${ARCH}/Serial_${ARCH}.c)
set(LUFA_SRC_TWI ${LUFA_ROOT_PATH}/Drivers/Peripheral/${ARCH}/TWI_${ARCH}.c)
set(LUFA_SRC_SCHEDULER ${LUFA_ROOT_PATH}/Scheduler/Scheduler.c)

include_directories(${LUFA_ROOT_PATH}/..)


# Create one target
add_executable(${PROJECT_NAME} ${SRC_FILES} ${LUFA_SRC_USB} ${LUFA_SRC_USBCLASS} ${LUFA_SRC_TEMPERATURE} ${LUFA_SRC_SERIAL} ${LUFA_SRC_TWI} ${LUFA_SRC_SCHEDULER})
#target_link_libraries(${PROJECT_NAME} LUFA)
# include lufa root path

# Rename the output to .elf as we will create multiple files
set_target_properties(${PROJECT_NAME} PROPERTIES OUTPUT_NAME ${PROJECT_NAME}.elf)

# Strip binary for upload
add_custom_target(strip ALL avr-strip ${PROJECT_NAME}.elf DEPENDS ${PROJECT_NAME})

# Transform binary into hex file, we ignore the eeprom segments in the step
add_custom_target(hex ALL avr-objcopy -R .eeprom -O ihex ${PROJECT_NAME}.elf ${PROJECT_NAME}.hex DEPENDS strip)
# Transform binary into hex file, this is the eeprom part (empty if you don't
# use eeprom static variables)
add_custom_target(eeprom avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" --change-section-lma .eeprom=0 -O ihex ${PROJECT_NAME}.elf ${PROJECT_NAME}.eep DEPENDS strip)

# Upload the firmware with avrdude
add_custom_target(upload avrdude -c ${PROG_TYPE} -p ${MCU} -U flash:w:${PROJECT_NAME}.hex DEPENDS hex)

# Upload the eeprom with avrdude
add_custom_target(upload_eeprom avrdude -c ${PROG_TYPE} -p ${MCU} -U eeprom:w:${PROJECT_NAME}.eep DEPENDS eeprom)

# Burn fuses
add_custom_target(fuses avrdude -c ${PROG_TYPE} -p ${MCU} -U lfuse:w:${L_FUSE}:m -U hfuse:w:${H_FUSE}:m -U efuse:w:${E_FUSE}:m -U lock:w:${LOCK_BIT}:m)

# Clean extra files
set_directory_properties(PROPERTIES ADDITIONAL_MAKE_CLEAN_FILES "${PROJECT_NAME}.hex;${PROJECT_NAME}.eeprom;${PROJECT_NAME}.lst")
