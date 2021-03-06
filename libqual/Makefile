# see License.txt for copyright and terms of use

.SUFFIXES:

SMBASE_DIR := ../smbase
STACK_DIR  := ..

CC    := gcc
AR     := ar
RANLIB := ranlib
DEP    := $(SMBASE_DIR)/depend.pl

# get rid of region weirdnesses
REGIONFLAGS = -Ddeletes= -Dtraditional= -Dsameregion= -Dparentptr=

CFLAGS :=
CFLAGS += $(REGIONFLAGS)

CFLAGS += -Wall

# turn on debugging symbols
CFLAGS += -g

# turn on gcov
# CFLAGS += -fprofile-arcs -ftest-coverage

# turn on gprof
# CFLAGS += -pg

# turn on optimizations
CFLAGS += -O2
CFLAGS += -fno-strict-aliasing
#CFLAGS += -O0
## Note: -O3 compiles incorrectly!
#CFLAGS += -O3

OBJ :=

# NOTE: This is going to cause flex and bison to run
OBJ += lattice-lex.o
OBJ += lattice-parse.tab.o

OBJ += buffer.o
OBJ += dd_list-serialize.o
OBJ += dd_list.o
OBJ += hash-serialize.o
OBJ += hash.o
OBJ += hashset-serialize.o
OBJ += hashset.o
OBJ += libqual.o
OBJ += location.o
OBJ += location_smbase_iface.o
OBJ += qerror.o
OBJ += qual_flags.o
OBJ += quals.o
OBJ += serialize.o
OBJ += utils.o

LIB :=
LIB += libqual.a

.PHONY: all
all: $(LIB)

.PHONY: clean
clean:
# generated files
	rm -f $(OBJ) $(LIB) lattice-lex.c lattice-parse.tab.*
# artifacts of building with the dependencies system
	rm -f *.d *.i

# for now, check-full is just check
.PHONY: check-full
check-full: check

.PHONY: check
check:
	@echo "No check in libqual; checking done by the oink/qual tests."

# **** build lexer and parser

# generate lexer; NOTE: we really depend on lattice-parse.tab.h but
# both are generated at the same time
lattice-lex.c: lattice-parse.l lattice-parse.tab.c
	flex -o$@ -Plyy $<

# generate parser; NOTE: lattice-parse.tab.h is generated by bison for
# lex at the same time
lattice-parse.tab.c: lattice-parse.y
	bison -d -plyy $<

# **** compile

%.o : %.c
# if we go through an intermediate .i file, it works
	$(CC) -c -o $@ -I$(STACK_DIR) $(CFLAGS) $<
	@$(DEP)  -o $@ -I$(STACK_DIR) $(CFLAGS) $< > $*.d
-include $(OBJ:.o=.d)

%.o : %.cc
	$(CC) -c -o $@ -I$(STACK_DIR) $(CFLAGS) $<        -Wno-deprecated
	@$(DEP)  -o $@ -I$(STACK_DIR) $(CFLAGS) $< > $*.d

# **** archive
libqual.a: $(OBJ)

$(LIB): %.a:
	$(AR) cru $@ $^
	$(RANLIB) $@

# **** coverage
.PHONY: gcov
gcov:
	gcov -f *.c
