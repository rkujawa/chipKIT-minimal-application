# You need to set the five variables below correctly for your system and chipKIT board.

# Path to chipKIT C compiler
TOOLCHAIN_PREFIX=/Applications/mpide.app/Contents/Resources/Java/hardware/pic32/compiler/pic32-tools
# Path to chipKIT avrdude
AVRTOOLS_PREFIX=/Applications/Mpide.app/Contents/Resources/Java/hardware/tools/avr/
# Serial port used to upload image with avrdude
SERIAL_PORT=/dev/tty.usbserial-A600CJ0F
# linker script
LDSCRIPT=chipKIT-MAX32-application-32MX795F512L.ld
# PIC32 CPU model
CPUTYPE=32MX795F512L


# optimization for C source disabled with -O0 for better dissassembly from gdb
CFLAGS=-O0  -c  -mno-smart-io  -w  -fno-exceptions  -ffunction-sections  -fdata-sections  -g  -mdebugger  -Wcast-align  -fno-short-double  -mprocessor=32MX795F512L  -DF_CPU=80000000L
ASFLAGS=-g1  -c  -mprocessor=32MX795F512L  -DF_CPU=80000000L
LDFLAGS=-Os  -Wl,--gc-sections  -mdebugger  -mprocessor=$(CPUTYPE)

CC=$(TOOLCHAIN_PREFIX)/bin/pic32-gcc
LD=$(CC)
AR=$(TOOLCHAIN_PREFIX)/bin/pic32-ar
BIN2HEX=$(TOOLCHAIN_PREFIX)/bin/pic32-bin2hex

AVRDUDE=$(AVRTOOLS_PREFIX)/bin/avrdude 
AVRDUDEFLAGS=-C$(AVRTOOLS_PREFIX)/etc/avrdude.conf -c stk500v2 -p pic32 -P $(SERIAL_PORT) -b 115200 -v -U 

OBJDIR=obj

all: build arstartup link hex burn

build: $(OBJDIR)/crt0.o $(OBJDIR)/crti.o $(OBJDIR)/crtn.o $(OBJDIR)/main.o

$(OBJDIR)/crt0.o:
	$(CC) $(CFLAGS) -o $(OBJDIR)/crt0.o startup/crt0.S

$(OBJDIR)/crti.o:
	$(CC) $(CFLAGS) -o $(OBJDIR)/crti.o startup/crti.S

$(OBJDIR)/crtn.o:
	$(CC) $(CFLAGS) -o $(OBJDIR)/crtn.o startup/crtn.S

$(OBJDIR)/main.o:
	$(CC) $(CFLAGS) -o $(OBJDIR)/main.o main.c

clean:
	rm -rf $(OBJDIR)/*
	rm -f main.hex
	rm -f main.elf

arstartup:
	$(AR) rcs $(OBJDIR)/startup.a $(OBJDIR)/crt0.o
	$(AR) rcs $(OBJDIR)/startup.a $(OBJDIR)/crti.o
	$(AR) rcs $(OBJDIR)/startup.a $(OBJDIR)/crtn.o

link:
	$(LD) $(LDFLAGS) -o main.elf $(OBJDIR)/main.o $(OBJDIR)/startup.a -lm -T $(LDSCRIPT)

hex:
	$(BIN2HEX) -a main.elf

burn:
	$(AVRDUDE) $(AVRDUDEFLAGS) flash:w:main.hex:i

