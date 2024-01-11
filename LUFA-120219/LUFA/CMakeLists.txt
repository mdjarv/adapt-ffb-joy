cmake_minimum_required(VERSION 3.10)

project(LUFA)

set(ARCH AVR8)

set(LUFA_SRC_USB Drivers/USB/Core/${ARCH}/Device_${ARCH}.c
        Drivers/USB/Core/${ARCH}/Endpoint_${ARCH}.c
        Drivers/USB/Core/${ARCH}/Host_${ARCH}.c
        Drivers/USB/Core/${ARCH}/Pipe_${ARCH}.c
        Drivers/USB/Core/${ARCH}/USBController_${ARCH}.c
        Drivers/USB/Core/${ARCH}/USBInterrupt_${ARCH}.c
        Drivers/USB/Core/${ARCH}/EndpointStream_${ARCH}.c
        Drivers/USB/Core/${ARCH}/PipeStream_${ARCH}.c
        Drivers/USB/Core/ConfigDescriptor.c
        Drivers/USB/Core/DeviceStandardReq.c
        Drivers/USB/Core/Events.c
        Drivers/USB/Core/HostStandardReq.c
        Drivers/USB/Core/USBTask.c
        Drivers/USB/Class/Common/HIDParser.c)

set(LUFA_SRC_USBCLASS Drivers/USB/Class/Device/AudioClassDevice.c
        Drivers/USB/Class/Device/CDCClassDevice.c
        Drivers/USB/Class/Device/HIDClassDevice.c
        Drivers/USB/Class/Device/MassStorageClassDevice.c
        Drivers/USB/Class/Device/MIDIClassDevice.c
        Drivers/USB/Class/Device/RNDISClassDevice.c
        Drivers/USB/Class/Host/AndroidAccessoryClassHost.c
        Drivers/USB/Class/Host/AudioClassHost.c
        Drivers/USB/Class/Host/CDCClassHost.c
        Drivers/USB/Class/Host/HIDClassHost.c
        Drivers/USB/Class/Host/MassStorageClassHost.c
        Drivers/USB/Class/Host/MIDIClassHost.c
        Drivers/USB/Class/Host/PrinterClassHost.c
        Drivers/USB/Class/Host/RNDISClassHost.c
        Drivers/USB/Class/Host/StillImageClassHost.c)

set(LUFA_SRC_TEMPERATURE Drivers/Board/Temperature.c)
set(LUFA_SRC_SERIAL Drivers/Peripheral/${ARCH}/Serial_${ARCH}.c)
set(LUFA_SRC_TWI Drivers/Peripheral/${ARCH}/TWI_${ARCH}.c)
set(LUFA_SRC_SCHEDULER Scheduler/Scheduler.c)


#LUFA_OPTS  = -D USB_DEVICE_ONLY
#LUFA_OPTS += -D FIXED_CONTROL_ENDPOINT_SIZE=8
#LUFA_OPTS += -D FIXED_NUM_CONFIGURATIONS=1
#LUFA_OPTS += -D USE_FLASH_DESCRIPTORS
#LUFA_OPTS += -D USE_STATIC_OPTIONS="(USB_DEVICE_OPT_FULLSPEED | USB_OPT_REG_ENABLED | USB_OPT_AUTO_PLL)"

add_definitions(
        -D USB_DEVICE_ONLY
        -D FIXED_CONTROL_ENDPOINT_SIZE=8
        -D FIXED_NUM_CONFIGURATIONS=1
        -D USE_FLASH_DESCRIPTORS
        -D USE_STATIC_OPTIONS="(USB_DEVICE_OPT_FULLSPEED | USB_OPT_REG_ENABLED | USB_OPT_AUTO_PLL)"
)

add_library(LUFA STATIC ${LUFA_SRC_USB} ${LUFA_SRC_USBCLASS} ${LUFA_SRC_TEMPERATURE} ${LUFA_SRC_SERIAL} ${LUFA_SRC_TWI} ${LUFA_SRC_SCHEDULER})
