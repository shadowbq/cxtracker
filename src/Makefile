#Makefile
CC=gcc

OCFLAGS=-g -O3 -Wall -Wextra
DCFLAGS=-g
PCFLAGS=-g -pg
LIBS=-lpcap
LDFLAGS=
RMCOMMAND = rm -f

SRC= cxtracker.c cxtracker.h \
format.c format.h \
ip.c ip.h

OBJS= ip.o format.o cxtracker.o

TARGETS=cxtracker

all: $(TARGETS) 

cxtracker: $(OBJS)
	test -d bin/ || mkdir -p bin/
	$(CC) $(OBJS) -o bin/cxtracker $(LIBS) $(LDFLAGS)

debug: $(OBJS)
	test -d bin/ || mkdir -p bin/
	$(CC) $(OBJS) -o bin/cxtracker $(LIBS) $(DCFLAGS) $(LDFLAGS)

profile: $(OBJS)
	test -d bin/ || mkdir -p bin/
	$(CC) $(OBJS) -o bin/cxtracker $(LIBS) $(PCFLAGS) $(LDFLAGS)

clean:
	$(RMCOMMAND) ${OBJS}
	$(RMCOMMAND) bin/cxtracker

.c.o:
	$(CC) -c -Wall $(DEFS) $(OCFLAGS) $<

test:
	bin/cxtracker -V | grep 'shadowbq'