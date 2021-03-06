include ../../rules.mak
#******************************************************************************************
# $Id$
#
# Copyright (C) 2009-2012 Calpont Corporation
# All rights reserved
#*****************************************************************************************/

# The name of the executable
PROGRAM=dataPumpTmp
VERSION=1.0.0

# List all the source files here
SRCS=main.cpp iosocket.cpp messagequeue.cpp bytestream.cpp socketparms.cpp configcpp.cpp inetstreamsocket.cpp

# Run-time directories for project shared libs
CALPONT_LIBRARY_PATH=$(EXPORT_ROOT)/lib

# Preprocessor flags
CPPFLAGS=-I/usr/include/libxml2

# Compiler flags
#CXXFLAGS+=-ggdb3 -Wall -O0
CXXFLAGS+=-g0 -O3 -fno-strict-aliasing -Wall

# Linker flags
LDFLAGS+=-lxml2 -lboost_thread

# install dir (set CALPONT_INSTALL_ROOT in env to override)
CALPONT_INSTALL_ROOT?=$(EXPORT_ROOT)

# end (sub-)project-specifc settings

.PHONY: install clean test docs FORCE coverage leakcheck bootstrap

GLIBS=-lcppunit -ldl

OBJS=$(SRCS:.cpp=.o)

$(PROGRAM): $(OBJS)
	$(LINK.cpp) -o $@ $^

bootstrap:

install: bootstrap $(PROGRAM)
	mkdir -p $(INSTALL_ROOT_BIN)
	$(INSTALL) $(PROGRAM) $(INSTALL_ROOT_BIN)

clean:
	rm -f $(OBJS) $(PROGRAM) core *~ *-gcov.* *.gcov $(PROGRAM)-gcov *.d config.tag *.d.*
	rm -rf html

docs:
	doxygen $(EXPORT_ROOT)/etc/Doxyfile

FORCE:

%-gcov.o: %.cpp
	$(COMPILE.cpp) -o $@ $^

$(PROGRAM)-gcov: CXXFLAGS+=-fprofile-arcs -ftest-coverage
$(PROGRAM)-gcov: $(subst .o,-gcov.o,$(OBJS))
	$(LINK.cpp) -o $@ $^ $(GLIBS)

coverage:

xcoverage: $(PROGRAM)-gcov FORCE
	LD_LIBRARY_PATH=$(LD_LIBRARY_PATH) ./$(PROGRAM)-gcov
	for file in $(SRCS); do \
		bfile=`basename $$file .cpp`; \
		gcov -o $${bfile}-gcov $$file; \
	done

tdriver: tdriver.o
	$(LINK.cpp) -o $@ $^ -lboost_program_options

test:

xtest run:
	LD_LIBRARY_PATH=$(LD_LIBRARY_PATH) ./$(PROGRAM)

leakcheck:

xleakcheck: $(PROGRAM)
	LD_LIBRARY_PATH=$(LD_LIBRARY_PATH) valgrind --tool=memcheck --leak-check=yes ./$(PROGRAM)

%.d: %.cpp
	@set -e; rm -f $@; \
	$(CC) -MM $(CPPFLAGS) $< > $@.$$$$; \
	sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ > $@; \
	rm -f $@.$$$$

ifndef BOOTSTRAP
-include $(SRCS:.cpp=.d)
endif

