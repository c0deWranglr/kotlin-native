TESTS := sum \
         sum_foo_bar \
         arithmetic \
         initialization

BIN_TESTS=$(foreach t, ${TESTS},${t}_test)

VPATH=codegen/function:codegen/object:${TOP}/runtime/build

LLC=llc-mp-3.8
CC=clang-mp-3.8
JAVA=java

TOP=../..
RUNTIME=${TOP}/runtime
BACKEND=${TOP}/backend.native
BACKEND_CLASSES=${TOP}/backend.native/build/classes
KOTLIN_DIST=${BACKEND}/kotlin-ir/dist
KOTLIN_NATIVE_CLASSPATH=${BACKEND_CLASSES}/bc_frontend:${BACKEND_CLASSES}/cli_bc:${BACKEND_CLASSES}/compiler:${BACKEND_CLASSES}/llvmInteropStubs:${BACKEND_CLASSES}/hashInteropStubs:${KOTLIN_DIST}/kotlinc/lib/kotlin-reflect.jar:${KOTLIN_DIST}kotlinc/lib/kotlin-stdlib.jar:${KOTLIN_DIST}/kotlinc/lib/kotlin-compiler.jar:${KOTLIN_DIST}/kotlinc/lib/kotlin-runtime.jar:${TOP}/Interop/Runtime/build/classes/main


define PROTO
$(1)_test:  $1-test.c $1.S runtime.S main.c
	$(CC) -o $$@ $$^

${1}_run:${1}_test
	./${1}_test
endef

$(foreach test,${TESTS},$(eval $(call PROTO,$(test))))

all:${BIN_TESTS}

run:$(foreach test,${TESTS},${test}_run)

clean:
	${RM} *.o *.S *.BCkt ${BIN_TESTS}

%.S:%.bc
	${LLC} -o $@ $<

%.S:%.BCkt
	${LLC} -o $@ $<


%.BCkt:%.kt
	${JAVA} -cp ${KOTLIN_NATIVE_CLASSPATH} -Djava.library.path=${BACKEND}/build/nativelibs org.jetbrains.kotlin.cli.bc.K2NativeKt -output $@ -runtime ${RUNTIME}/build/runtime.bc ${RUNTIME}/src/main/kotlin $<

