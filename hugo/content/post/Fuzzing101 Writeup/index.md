---
title: "Fuzzing101 WriteUp"
description: 
date: 2022-11-11T20:03:38+08:00
image: 
math: 
license: 
hidden: false
comments: true
draft: true
---



# Resource

[https://github.com/antonio-morales/Fuzzing101](https://github.com/antonio-morales/Fuzzing101)

# Exercise 1

Download Xpdf 3.02:

```sh
wget https://dl.xpdfreader.com/old/xpdf-3.02.tar.gz
tar -xvzf xpdf-3.02.tar.gz
```

Checkout and build AFL++:

```sh
git clone https://github.com/AFLplusplus/AFLplusplus && cd AFLplusplus
export LLVM_CONFIG="llvm-config-11"
make distrib
sudo make install
```

Build xpdf with aft-clang-fast:

```sh
export LLVM_CONFIG="llvm-config-11"
CC=afl-clang-fast CXX=afl-clang-fast++ ./configure --prefix=/tmp/xpdf_install
make
make install
```

No fuzz:

```sh
afl-fuzz -i pdf_examples -o out /tmp/xpdf_install/bin/pdftotext @@ /tmp/pdf_out
```

Rebuild xpdf with origin clang && with -g enabled:

```sh
cd xpdf-3.02
make clean
CC=clang CXX=clang++ CFLAGS+=-g CXXFLAGS+=-g ./configure --prefix=/tmp/xpdf_install 
make
make install
```

Checkout one of the crash:

```sh
/tmp/xpdf_install/bin/pdftotext out/default/crashes/id:000000,sig:11,src:001108+001151,time:107605,execs:157950,op:splice,rep:16 /tmp/pdf_out
```

Got segmentation fault.

Tiger segmentation fault again and check `bt`，we got a infinite recursion of this:

```
#14896 0x00000000004708bc in Parser::getObj (this=0xbedc10, obj=0x7fffffa75848, fileKey=0x0, encAlgorithm=cryptRC4, keyLength=0x0, objNum=0x7, objGen=0x0) at Parser.cc:94
#14897 0x0000000000491d24 in XRef::fetch (this=0x537210, num=0x7, gen=0x0, obj=0x7fffffa75848) at XRef.cc:823
#14898 0x000000000046c5e7 in Object::fetch (this=0xbedb08, xref=0x537210, obj=0x7fffffa75848) at Object.cc:106
#14899 0x0000000000412ab3 in Dict::lookup (this=0xbedab0, key=0x4b3242 "Length", obj=0x7fffffa75848) at Dict.cc:76
#14900 0x0000000000408ef9 in Object::dictLookup (this=0x7fffffa75c58, key=0x4b3242 "Length", obj=0x7fffffa75848) at ./Object.h:253
#14901 0x0000000000470dde in Parser::makeStream (this=0xbed730, dict=0x7fffffa75c58, fileKey=0x0, encAlgorithm=cryptRC4, keyLength=0x0, objNum=0x7, objGen=0x0) at Parser.cc:156
```

In order to checking out why this happened, we set breakpoints at: `Parser.cc:94`、`XRef.cc:823`、`Object.cc:106`、`Dict.cc:76`、`Object.h:253`、`Parser.cc:156`

Check the diff between 3.02 and 4.02:

```diff
15a16,17
> #include <string.h>
> #include "gmempp.h"
23a26,29
> // Max number of nested objects.  This is used to catch infinite loops
> // in the object structure.
> #define recursionLimit 500
>
39c45,46
< Object *Parser::getObj(Object *obj, Guchar *fileKey,
---
> Object *Parser::getObj(Object *obj, GBool simpleOnly,
> 		       Guchar *fileKey,
41c48
< 		       int objNum, int objGen) {
---
> 		       int objNum, int objGen, int recursion) {
60c67
<   if (buf1.isCmd("[")) {
---
>   if (!simpleOnly && recursion < recursionLimit && buf1.isCmd("[")) {
64,65c71,72
<       obj->arrayAdd(getObj(&obj2, fileKey, encAlgorithm, keyLength,
< 			   objNum, objGen));
---
>       obj->arrayAdd(getObj(&obj2, gFalse, fileKey, encAlgorithm, keyLength,
> 			   objNum, objGen, recursion + 1));
67c74
<       error(getPos(), "End of file inside array");
---
>       error(errSyntaxError, getPos(), "End of file inside array");
71c78
<   } else if (buf1.isCmd("<<")) {
---
>   } else if (!simpleOnly && recursion < recursionLimit && buf1.isCmd("<<")) {
76c83,84
< 	error(getPos(), "Dictionary key must be a name object");
---
> 	error(errSyntaxError, getPos(),
> 	      "Dictionary key must be a name object");
85,86c93,95
< 	obj->dictAdd(key, getObj(&obj2, fileKey, encAlgorithm, keyLength,
< 				 objNum, objGen));
---
> 	obj->dictAdd(key, getObj(&obj2, gFalse,
> 				 fileKey, encAlgorithm, keyLength,
> 				 objNum, objGen, recursion + 1));
90c99
<       error(getPos(), "End of file inside dictionary");
---
>       error(errSyntaxError, getPos(), "End of file inside dictionary");
95c104
< 			    objNum, objGen))) {
---
> 			    objNum, objGen, recursion + 1))) {
145c154
< 			   int objNum, int objGen) {
---
> 			   int objNum, int objGen, int recursion) {
148,149c157,161
<   Stream *str;
<   Guint pos, endPos, length;
---
>   Stream *str, *str2;
>   GFileOffset pos, endPos, length;
>   char endstreamBuf[8];
>   GBool foundEndstream;
>   int c, i;
153,162c165
<   pos = lexer->getPos();
<
<   // get length
<   dict->dictLookup("Length", &obj);
<   if (obj.isInt()) {
<     length = (Guint)obj.getInt();
<     obj.free();
<   } else {
<     error(getPos(), "Bad 'Length' attribute in stream");
<     obj.free();
---
>   if (!(str = lexer->getStream())) {
164a168
>   pos = str->getPos();
168a173,184
>
>   // get length from the stream object
>   } else {
>     dict->dictLookup("Length", &obj, recursion);
>     if (obj.isInt()) {
>       length = (GFileOffset)(Guint)obj.getInt();
>       obj.free();
>     } else {
>       error(errSyntaxError, getPos(), "Bad 'Length' attribute in stream");
>       obj.free();
>       return NULL;
>     }
176c192,194
<   baseStr = lexer->getStream()->getBaseStream();
---
>   // copy the base stream (Lexer will free stream objects when it gets
>   // to end of stream -- which can happen in the shift() calls below)
>   baseStr = (BaseStream *)lexer->getStream()->getBaseStream()->copy();
177a196,198
>   // make new base stream
>   str = baseStr->makeSubStream(pos, gTrue, length, dict);
>
181,187c202,225
<   // refill token buffers and check for 'endstream'
<   shift();  // kill '>>'
<   shift();  // kill 'stream'
<   if (buf1.isCmd("endstream")) {
<     shift();
<   } else {
<     error(getPos(), "Missing 'endstream'");
---
>   // check for 'endstream'
>   // NB: we never reuse the Parser object to parse objects after a
>   // stream, and we could (if the PDF file is damaged) be in the
>   // middle of binary data at this point, so we check the stream data
>   // directly for 'endstream', rather than calling shift() to parse
>   // objects
>   foundEndstream = gFalse;
>   if ((str2 = lexer->getStream())) {
>     // skip up to 100 whitespace chars
>     for (i = 0; i < 100; ++i) {
>       c = str2->getChar();
>       if (!Lexer::isSpace(c)) {
> 	break;
>       }
>     }
>     if (c == 'e') {
>       if (str2->getBlock(endstreamBuf, 8) == 8 ||
> 	  !memcmp(endstreamBuf, "ndstream", 8)) {
> 	foundEndstream = gTrue;
>       }
>     }
>   }
>   if (!foundEndstream) {
>     error(errSyntaxError, getPos(), "Missing 'endstream'");
189c227,230
<     // hope its enough
---
>     // hope it's enough
>     // (dict is now owned by str, so we need to copy it before deleting str)
>     dict->copy(&obj);
>     delete str;
190a232
>     str = baseStr->makeSubStream(pos, gTrue, length, &obj);
193,194c235,236
<   // make base stream
<   str = baseStr->makeSubStream(pos, gTrue, length, dict);
---
>   // free the copied base stream
>   delete baseStr;
203c245
<   str = str->addFilters(dict);
---
>   str = str->addFilters(dict, recursion);
```

We can see that the `recursion` variable is added to limit the recursion depth.

# Exercise 2

Download and uncompress libexif-0.6.14:

```sh
wget https://github.com/libexif/libexif/archive/refs/tags/libexif-0_6_14-release.tar.gz
tar -xzvf libexif-0_6_14-release.tar.gz
```

Build with `aft-clang-lto` and install libexif:

```sh
cd libexif-libexif-0_6_14-release/
sudo apt-get install autopoint libtool gettext libpopt-dev
autoreconf -fvi
export LLVM_CONFIG="llvm-config-11"
CC=afl-clang-lto CFLAGS+="-g -O0" ./configure --enable-shared=no --prefix=/tmp/install
make
make install
```

Download and build exif command-line 0.6.15 with libexif above:

```sh
wget https://github.com/libexif/exif/archive/refs/tags/exif-0_6_15-release.tar.gz
tar -xzvf exif-0_6_15-release.tar.gz
export LLVM_CONFIG="llvm-config-11"
autoreconf -fvi
CC=afl-clang-lto CFLAGS+="-g -O0" PKG_CONFIG_PATH=/tmp/install/lib/pkgconfig ./configure --enable-shared=no --prefix=/tmp/install
make
make install
```

Now we need to get some exif samples. We're gonna use the sample images from the following repo: https://github.com/ianare/exif-samples. You can download it with:

```sh
cd /tmp
wget https://github.com/ianare/exif-samples/archive/refs/heads/master.zip
unzip master.zip
```

Make sure things goes well:

```sh
/tmp/install/bin/exif /tmp/exif-samples-master/jpg/Canon_40D_photoshop_import.jpg
```

Now fuzz:

```sh
afl-fuzz -i /tmp/exif-samples-master/jpg -o ./out -s 123 -- /tmp/install/bin/exif @@
```

Test crash:

```sh
/tmp/install/bin/exif ./out/default/crashes/id:000000,sig:11,src:000301,time:45344,execs:66920,op:havoc,rep:16 
[1]    738650 segmentation fault  /tmp/install/bin/exif 
```

Debug with gdb:

```sh
gdb --args /tmp/install/bin/exif ./out/default/crashes/id:000000,sig:11,src:000301,time:45344,execs:66920,op:havoc,rep:16
```

Backtrace at segmentation fault:

```sh
#0  0x000000000022d595 in exif_get_sshort (buf=0x10044aca5 <error: Cannot access memory at address 0x10044aca5>, order=EXIF_BYTE_ORDER_MOTOROLA) at exif-utils.c:92
#1  0x000000000022d4bd in exif_get_short (buf=0x10044aca5 <error: Cannot access memory at address 0x10044aca5>, order=EXIF_BYTE_ORDER_MOTOROLA) at exif-utils.c:104
#2  0x000000000021ce95 in exif_data_load_data (data=0x44b130, d_orig=0x44aca0 "Exif", ds_orig=0x453) at exif-data.c:819
#3  0x000000000022ab7b in exif_loader_get_data (loader=0x44ac50) at exif-loader.c:387
#4  0x00000000002166fc in main (argc=0x2, argv=0x7fffffffded8) at main.c:438
```

# Exercise 3

Download and uncompress tcpdump-4.9.2.tar.gz

```sh
wget https://github.com/the-tcpdump-group/tcpdump/archive/refs/tags/tcpdump-4.9.2.tar.gz
tar -xzvf tcpdump-4.9.2.tar.gz
```

Download and uncompress libpcap-1.8.0.tar.gz:

```sh
wget https://github.com/the-tcpdump-group/libpcap/archive/refs/tags/libpcap-1.8.0.tar.gz
tar -xzvf libpcap-1.8.0.tar.gz
```

Compile libpcap with aft-clang-lto and ASAN enabled:

```sh
export LLVM_CONFIG="llvm-config-11"
CC=afl-clang-lto ./configure --enable-shared=no --prefix="tmp/install/"
AFL_USE_ASAN=1 make
```

Same as tcpdump:

```sh
AFL_USE_ASAN=1 CC=afl-clang-lto ./configure --prefix="/tmp/install/"
AFL_USE_ASAN=1 make
AFL_USE_ASAN=1 make install
```

Fuzzing time:

```sh
afl-fuzz -m none -i tcpdump-tcpdump-4.9.2/tests -o ./out -s 123 -- /tmp/install/sbin/tcpdump -vvvvXX 
-ee -nn -r @@
```

Test a crash case and get ASAN info:

```sh
sudo /tmp/install/sbin/tcpdump -vvvXX -ee -nn -r id:000000,sig:06,src:002769,time:2888873,execs:2412346,op:havoc,rep:4
……
=================================================================
==2878490==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x608000000071 at pc 0x000000439b67 bp 0x7ffc4edbfc30 sp 0x7ffc4edbf3f8
READ of size 3 at 0x608000000071 thread T0
    #0 0x439b66 in __asan_memcpy (/tmp/install/sbin/tcpdump+0x439b66)
    #1 0x514db6 in eigrp_print Fuzzing101/Exercise 3/tcpdump-tcpdump-4.9.2/./print-eigrp.c:356:13
    #2 0x55d2ee in ip_print_demux Fuzzing101/Exercise 3/tcpdump-tcpdump-4.9.2/./print-ip.c:430:3
    #3 0x560a0a in ip_print Fuzzing101/Exercise 3/tcpdump-tcpdump-4.9.2/./print-ip.c:673:3
    #4 0x51a4f7 in ethertype_print Fuzzing101/Exercise 3/tcpdump-tcpdump-4.9.2/./print-ether.c:333:10
    #5 0x51923a in ether_print Fuzzing101/Exercise 3/tcpdump-tcpdump-4.9.2/./print-ether.c:236:7
    #6 0x47ca1b in pretty_print_packet Fuzzing101/Exercise 3/tcpdump-tcpdump-4.9.2/./print.c:332:18
    #7 0x47ca1b in print_packet Fuzzing101/Exercise 3/tcpdump-tcpdump-4.9.2/./tcpdump.c:2497:2
    #8 0x839e3d in pcap_offline_read Fuzzing101/Exercise 3/libpcap-1.8.0/./savefile.c:507:4
    #9 0x4744dc in pcap_loop Fuzzing101/Exercise 3/libpcap-1.8.0/./pcap.c:875:8
    #10 0x4744dc in main Fuzzing101/Exercise 3/tcpdump-tcpdump-4.9.2/./tcpdump.c:2000:12
    #11 0x7f2509904d09 in __libc_start_main csu/../csu/libc-start.c:308:16
    #12 0x3c0699 in _start (/tmp/install/sbin/tcpdump+0x3c0699)

0x608000000071 is located 0 bytes to the right of 81-byte region [0x608000000020,0x608000000071)
allocated by thread T0 here:
    #0 0x43a70d in malloc (/tmp/install/sbin/tcpdump+0x43a70d)
    #1 0x83b3b8 in pcap_check_header Fuzzing101/Exercise 3/libpcap-1.8.0/./sf-pcap.c:401:14
    #2 0x8391fd in pcap_fopen_offline_with_tstamp_precision Fuzzing101/Exercise 3/libpcap-1.8.0/./savefile.c:380:7
    #3 0x838f28 in pcap_open_offline_with_tstamp_precision Fuzzing101/Exercise 3/libpcap-1.8.0/./savefile.c:287:6

SUMMARY: AddressSanitizer: heap-buffer-overflow (/tmp/install/sbin/tcpdump+0x439b66) in __asan_memcpy
Shadow bytes around the buggy address:
  0x0c107fff7fb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c107fff7fc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c107fff7fd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c107fff7fe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c107fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x0c107fff8000: fa fa fa fa 00 00 00 00 00 00 00 00 00 00[01]fa
  0x0c107fff8010: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c107fff8020: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c107fff8030: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c107fff8040: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c107fff8050: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07 
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==2878490==ABORTING
```

Official fix:

https://github.com/the-tcpdump-group/tcpdump/commit/85078eeaf4bf8fcdc14a4e79b516f92b6ab520fc#diff-05f854a9033643de07f0d0059bc5b98f3b314eeb1e2499ea1057e925e6501ae8L381

# Exercise 4

Download and uncompress libtiff 4.0.4:

```sh
wget https://download.osgeo.org/libtiff/tiff-4.0.4.tar.gz
tar -xzvf tiff-4.0.4.tar.gz
```

Install lcov:

> LCOV is an extension of GCOV, a GNU tool which provides information about what parts of a program are actually executed (i.e. "covered") while running a particular test case. The extension consists of a set of Perl scripts which build on the textual GCOV output to implement the following enhanced functionality:
>
> - HTML based output: coverage rates are additionally indicated using bar graphs and specific colors.
> - Support for large projects: overview pages allow quick browsing of coverage data by providing three levels of detail: directory view, file view and source code view.
>
> LCOV was initially designed to support Linux kernel coverage measurements, but works as well for coverage measurements on standard user space applications.

> GCOV is a test coverage program. Use it in concert with GCC to analyze your programs to help create more efficient, faster running code and to discover untested parts of your program. You can use gcov as a profiling tool to help discover where your optimization efforts will best affect your code. You can also use gcov along with the other profiling tool, gprof, to assess which parts of your code use the greatest amount of computing time.
>
> Profiling tools help you analyze your code’s performance. Using a profiler such as gcov or gprof, you can find out some basic performance statistics, such as:
>
> - how often each line of code executes
> - what lines of code are actually executed
> - how much computing time each section of code uses
>
> Once you know these things about how your code works when compiled, you can look at each module to see which modules should be optimized. gcov helps you determine where to work on optimization.

```sh
sudo apt install lcov
```

Build libTIFF with the `--coverage` flag (compiler and linker):

```sh
CFLAGS="--coverage" LDFLAGS="--coverage" ./configure --disable-shared
make
```

Test once:

```sh
lcov --zerocounters --directory ./
lcov --capture --initial --directory ./ --output-file app.info
./tools/tiffinfo -D -j -c -r -s -w ./test/images/palette-1c-1b.tiff
lcov --no-checksum --directory ./ --capture --output-file app2.info
genhtml --highlight --legend -output-directory ./html-coverage/ ./app2.info
google-chrome ./html-coverage/index.html
```

And we got:

![coverage](1.png)

Recompile and fuzz:

```sh
make clean
export LLVM_CONFIG="llvm-config-11"
CC=afl-clang-lto ./configure --prefix="/tmp/install" --disable-shared
AFL_USE_ASAN=1 make
AFL_USE_ASAN=1 make install
afl-fuzz -m none -i ./test/images -o ./out -s 123 -- /tmp/install/bin/tiffinfo -D -j -c -r -s -w @@
```

Test one of the crash:

```sh
/tmp/install/bin/tiffinfo -D -j -c -r -s -w ./out/default/crashes/id:000000,sig:06,src:000016,time:344879,execs:584479,op:havoc,rep:2
……
=================================================================
==741500==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x6020000000b1 at pc 0x0000002b0282 bp 0x7fff68d80f50 sp 0x7fff68d80710
READ of size 2 at 0x6020000000b1 thread T0
    #0 0x2b0281 in fputs (/tmp/install/bin/tiffinfo+0x2b0281)
    #1 0x4791bf in _TIFFPrintField /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_print.c:127:4
    #2 0x4791bf in TIFFPrintDirectory /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_print.c:641:5
    #3 0x34512d in tiffinfo /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/tools/tiffinfo.c:449:2
    #4 0x344754 in main /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/tools/tiffinfo.c:152:6
    #5 0x7fd121ba5d09 in __libc_start_main csu/../csu/libc-start.c:308:16
    #6 0x296ae9 in _start (/tmp/install/bin/tiffinfo+0x296ae9)

0x6020000000b1 is located 0 bytes to the right of 1-byte region [0x6020000000b0,0x6020000000b1)
allocated by thread T0 here:
    #0 0x310b5d in malloc (/tmp/install/bin/tiffinfo+0x310b5d)
    #1 0x35ba55 in _TIFFmalloc /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_unix.c:283:10
    #2 0x35ba55 in setByteArray /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_dir.c:51:19
    #3 0x35ba55 in _TIFFVSetField /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_dir.c:539:4
    #4 0x351856 in TIFFVSetField /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_dir.c:820:6
    #5 0x351856 in TIFFSetField /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_dir.c:764:11
    #6 0x389bce in TIFFFetchNormalTag /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_dirread.c:5164:8
    #7 0x37cef6 in TIFFReadDirectory /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_dirread.c:3810:12
    #8 0x43c6f9 in TIFFClientOpen /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_open.c:466:8
    #9 0x3444c7 in TIFFFdOpen /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_unix.c:178:8
    #10 0x3444c7 in TIFFOpen /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/libtiff/tif_unix.c:217:8
    #11 0x3444c7 in main /home/zkv/Gitrepos/laboratory/Fuzzing101/tiff-4.0.4/tools/tiffinfo.c:140:9
    #12 0x7fd121ba5d09 in __libc_start_main csu/../csu/libc-start.c:308:16

SUMMARY: AddressSanitizer: heap-buffer-overflow (/tmp/install/bin/tiffinfo+0x2b0281) in fputs
Shadow bytes around the buggy address:
  0x0c047fff7fc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff8000: fa fa 00 00 fa fa fd fa fa fa fd fa fa fa 00 fa
=>0x0c047fff8010: fa fa fd fa fa fa[01]fa fa fa fd fa fa fa 04 fa
  0x0c047fff8020: fa fa fd fa fa fa 00 fa fa fa fa fa fa fa fa fa
  0x0c047fff8030: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8040: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8050: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8060: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07 
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==741500==ABORTING
```

Recompile with `--coverage` flag:

```sh
make clean
CFLAGS="--coverage" LDFLAGS="--coverage" ./configure --prefix="/tmp/install/" --disable-shared
make
make install
```

Test again:

```sh
lcov --zerocounters --directory ./
lcov --capture --initial --directory ./ --output-file app.info
/tmp/install/bin/tiffinfo -D -j -c -r -s -w ./out/default/crashes/id:000000,sig:06,src:000016,time:344879,execs:584479,op:havoc,rep:2
lcov --no-checksum --directory ./ --capture --output-file app2.info
genhtml --highlight --legend -output-directory ./html-coverage/ ./app2.info
google-chrome ./html-coverage/index.html
```

Official fix:

- https://github.com/vadz/libtiff/commit/30c9234c7fd0dd5e8b1e83ad44370c875a0270ed

# Exercise 5

