# General makefile for generic unix & linux platforms

TARGET = sdlpal

INTER=.unix

HOST =

DEPFLAGS = -MT $@ -MMD -MP -MF $*$(INTER).Td

MIDI_CFILES = $(wildcard ../native_midi/*.c) native_midi_alsa.c
MIDI_CPPFILES =
MIDI_LDFLAGS = -lasound

SOURCES = .. ../adplug ../liboggvorbis/src ../libopusfile/src ../libopusfile/celt ../libopusfile/silk ../libopusfile/silk/float ../libmad
CFILES = $(foreach dir, $(SOURCES), $(wildcard $(dir)/*.c)) $(MIDI_CFILES)
CPPFILES = $(foreach dir, $(SOURCES), $(wildcard $(dir)/*.cpp)) unix.cpp $(MIDI_CPPFILES)
TOBJFILES = $(CFILES:.c=$(INTER).o) $(CPPFILES:.cpp=$(INTER).o)
DEPFILES = $(OBJFILES:.o=.d)
SDL_CONFIG = sdl2-config

CC = $(HOST)gcc
CXX = $(HOST)g++
override CCFLAGS += `$(SDL_CONFIG) --cflags` -D_GNU_SOURCE -g -Wall -O2 -fno-strict-aliasing $(EXTRA_CCFLAGS)
CXXFLAGS = $(CCFLAGS) -std=c++11
CFLAGS = $(CCFLAGS) -std=gnu99
LDFLAGS = `$(SDL_CONFIG) --libs` $(MIDI_LDFLAGS) -lstdc++ -lm

LDFLAGS += -lGL -pthread
OBJFILES = $(TOBJFILES)

POSTCOMPILE = @mv -f $*$(INTER).Td $*$(INTER).d && touch $@

.PHONY : all clean

all: $(TARGET)

$(TARGET): $(OBJFILES)
	@echo [LD] $@
	@$(CXX) $^ -o $@ $(LDFLAGS)

%$(INTER).o: %.c %$(INTER).d
	@echo [CC] $<
	@$(CC) $(DEPFLAGS) $(CFLAGS) -c $< -o $@
	$(POSTCOMPILE)

%$(INTER).o: %.cpp %$(INTER).d
	@echo [CC] $<
	@$(CXX) $(DEPFLAGS) $(CXXFLAGS) -c $< -o $@
	$(POSTCOMPILE)

clean:
	-@rm -f $(TARGET) $(OBJFILES) $(DEPFILES)

%.d: ;
.PRECIOUS: %.d

-include $(DEPFILES)

../liboggvorbis/%$(INTER).o: EXTRA_CCFLAGS := -I../liboggvorbis/include -I../liboggvorbis/src
../libopusfile/%$(INTER).o: EXTRA_CCFLAGS := -I../liboggvorbis/include -I../libopusfile/include -I../libopusfile/src -I../libopusfile/celt -I../libopusfile/silk -I../libopusfile/silk/float -DHAVE_CONFIG_H
%$(INTER).o: EXTRA_CCFLAGS := -I. -I.. -I../liboggvorbis/include -I../libopusfile/include -DPAL_HAS_PLATFORM_SPECIFIC_UTILS
