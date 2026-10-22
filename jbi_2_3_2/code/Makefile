#
# Makefile for Jam STAPL Player (Linux / UNIX)
#
# SPDX-License-Identifier: MIT-0
#

CC ?= gcc
CFLAGS ?= -O2 -Wall
LDFLAGS ?=
LIBS ?=

# libgpiod configuration (pkg-config is preferred if available)
PKG_CONFIG ?= pkg-config
LIBGPIOD_CFLAGS := $(shell $(PKG_CONFIG) --cflags libgpiod 2>/dev/null)
LIBGPIOD_LIBS := $(shell $(PKG_CONFIG) --libs libgpiod 2>/dev/null || echo -lgpiod)

# Installation paths
PREFIX ?= /usr/local
BINDIR ?= $(PREFIX)/bin

TARGET ?= jbi

SRCS = jbicomp.c jbijtag.c jbimain.c jbistub.c

OBJS = $(SRCS:.c=.o)
DEPS = $(SRCS:.c=.d)

ALL_CFLAGS = $(CFLAGS) $(CPPFLAGS) $(LIBGPIOD_CFLAGS) -MMD -MP
ALL_LIBS   = $(LDFLAGS) $(LIBS) $(LIBGPIOD_LIBS)

.PHONY: all clean install uninstall

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $(OBJS) $(ALL_LIBS)

%.o: %.c
	$(CC) $(ALL_CFLAGS) -c $< -o $@

-include $(DEPS)

install: $(TARGET)
	install -d $(DESTDIR)$(BINDIR)
	install -m 755 $(TARGET) $(DESTDIR)$(BINDIR)/$(TARGET)

uninstall:
	rm -f $(DESTDIR)$(BINDIR)/$(TARGET)

clean:
	rm -f $(OBJS) $(DEPS) $(TARGET)

