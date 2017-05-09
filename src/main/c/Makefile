ifeq ($(JAVA_HOME),)
$(error $$(JAVA_HOME) is not defined)
endif

JNI_INCLUDE_DIRS=$(addprefix -I,$(shell find $(JAVA_HOME)/include -type d))
CFLAGS=-O2 -Wall -fPIC $(JNI_INCLUDE_DIRS)
CC=gcc

#OS-dependent extension lookup
UNAME := $(shell uname)
ifeq ($(UNAME),Darwin)
LIB_EXT=Darwin.dylib
else
LIB_EXT=Linux.so
endif

FERMI_LITE_COMMIT=016847d75560f46de71d3f190e921674360e3ce1
JNI_BASE_NAME=org_broadinstitute_hellbender_utils_fermi_FermiLiteAssembler

all: libfml.$(LIB_EXT)

libfml.$(LIB_EXT): $(JNI_BASE_NAME).o fermi-lite/libfml.a
	$(CC) -dynamiclib -shared -o $@ $^ -lm -lz -lpthread

fermi-lite:
	git clone https://github.com/frank-y-liu/fermi-lite && cd fermi-lite && git checkout $(FERMI_LITE_COMMIT) && echo '#define FML_COMMIT "'$(FERMI_LITE_COMMIT)'"' > fml_commit.h

fermi-lite/libfml.a: fermi-lite
	$(MAKE) CFLAGS="$(CFLAGS)" -C fermi-lite libfml.a

$(JNI_BASE_NAME).o: $(JNI_BASE_NAME).c fermi-lite/libfml.a

test: test.o fermi-lite/libfml.a
	$(CC) -o $@ $^ -lm -lz -lpthread

clean:
	rm -rf fermi-lite *.o *.$(LIB_EXT)

.PHONY: all clean
