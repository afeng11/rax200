############################################################################################
#
#	Makefile For MiniDLNA
#
#	Author: Michael Jiang
#	Date: 2010.05.01
#	
#	Description:
#		1. I don't re-configure ffmpeg, jpeg-7, zlib & minidlna , because their Makefiles 
#		   have been modified by me. Others will be re-configured again if no Makefiles; 
#		2. It should be compiled in Linux2.6 platform only.
#		3. There are two variables in Makefile which you should take care about. one
#		   is $HOST_COMPILE and the other is $HOST. I can't refer to them from ap/config.in,
#		   so I make a hard code here. When you want to do a portation please take care
#		   them.   
#
#############################################################################################
SUBDIRS += zlib-1.2.8

#SUBDIRS += ffmpeg-0.5.1
SUBDIRS += ffmpeg-3.4.1
ifeq ($(LINUXDIR),$(BASEDIR)/kernel/linux-4.1)
LIBOGGDIR := libogg-1.1.4
else
SUBDIRS += libogg-1.1.4
endif
SUBDIRS += libexif-0.6.19
SUBDIRS += flac-1.2.1
SUBDIRS += libvorbis-1.2.3
SUBDIRS += jpeg-7
SUBDIRS += sqlite-3.6.22
SUBDIRS += libid3tag-0.15.0b
#/projects/hnd/tools/linux/hndtools-mipsel-linux-uclibc-4.2.3
TOOL_CHAIN_PATH=$(TOOLCHAIN)
#/home/michael/DLNA
MINI_DLNA_PATH=$(shell pwd)

CFLAGS += -Os

# [FIXME] added by Michael Jiang.
# The below variables  should be modified when you use different platform.
ifeq ($(CONFIG_XDSL_PRODUCT),y)
HOST=mips-linux-linux26
HOST_COMPILE=$(HOST)-
else
ifeq ($(LINUXDIR),$(BASEDIR)/kernel/linux-4.1)
HOST=arm-buildroot-linux-gnueabi
HOST_COMPILE=$(HOST)-
else
HOST=arm-linux
HOST_COMPILE=$(HOST)-
endif
endif
#CROSS_COMPILE=mipsel-linux-uclibc-
#HOST_COMPILE=mipsel-linux-linux26-
#CROSS=mipsel-linux-uclibc
# comments ended, by Michael Jiang.

all: zlib libogg libexif sqlite3 libvorbis flac libid3tag ffmpeg jpeg
	for i in $(SUBDIRS); do \
		(cd $$i && make TOOL_CHAIN_PATH=$(TOOL_CHAIN_PATH) \
				CROSS_COMPILE=$(CROSS_COMPILE) \
				HOST_COMPILE=$(HOST_COMPILE) \
				MINI_DLNA_PATH=$(MINI_DLNA_PATH)) \
	done
	make copy

	if [ ! -f ./minidlna-1.1.5/Makefile ];then \
		cd minidlna-1.1.5 && (CC=arm-buildroot-linux-gnueabi-gcc ./configure --host=arm --enable-rpath --enable-tivo --enable-netgear \
		LDFLAGS="-lpthread ${LDFLAGS:-} -Wl,-rpath,../lib -L../lib" \
		CFLAGS="-g -O3 -D_GNU_SOURCE -D_FILE_OFFSET_BITS=64 -lpthread -I../ffmpeg-3.4.1/ -I../jpeg-7 -I../ffmpeg-3.4.1/libavformat -I../libvorbis-1.2.3/include \
				-I../sqlite-3.6.22 -I../libexif-0.6.19 -I../libid3tag-0.15.0b \
				-I../libogg-1.1.4/include \
				-I../libvorbis-1.3.5/include/vorbis -I../flac-1.2.1/include \
				-L../lib"); \
	fi

	(cd minidlna-1.1.5 && make MINI_DLNA_PATH=$(MINI_DLNA_PATH) \
			     CROSS_COMPILE=$(CROSS_COMPILE) \
			     HOST_COMPILE=$(HOST_COMPILE) \
			     MINI_DLNA_PATH=$(MINI_DLNA_PATH))
	cp ./minidlna-1.1.5/minidlnad	./lib/minidlna.exe
	#make install


ifeq ($(LINUXDIR),$(BASEDIR)/kernel/linux-4.1)
zlib_config:
	# compile zlib first, bcz. libid3tag needs it for configuration.
	cd zlib-1.2.8 &&  (./configure --sharedlibdir=../lib);

zlib: zlib_config
	# compile zlib first, bcz. libid3tag needs it for configuration.
	cd zlib-1.2.8 &&  make
else ifeq ($(CONFIG_XDSL_PRODUCT),y)
zlib_config:
	# compile zlib first, bcz. libid3tag needs it for configuration.
	cd zlib-1.2.8 &&  (./configure --sharedlibdir=../lib);

zlib: zlib_config
	# compile zlib first, bcz. libid3tag needs it for configuration.
	cd zlib-1.2.8 &&  make
else
zlib: 
	# compile zlib first, bcz. libid3tag needs it for configuration.
	cd zlib-1.2.8 &&  (./configure --sharedlibdir=../lib);
endif


libogg:
	if [ ! -f ./libogg-1.1.4/Makefile ];then \
	cd $(LIBOGGDIR) && (./configure --host=$(HOST)  CC=$(CROSS_COMPILE)gcc); \
	fi
	cd $(LIBOGGDIR) && \
	make TOOL_CHAIN_PATH=$(TOOL_CHAIN_PATH) \
				CROSS_COMPILE=$(CROSS_COMPILE) \
				HOST_COMPILE=$(HOST_COMPILE) \
				MINI_DLNA_PATH=$(MINI_DLNA_PATH)
	
libexif:
	if [ ! -f ./libexif-0.6.19/Makefile ];then \
	cd libexif-0.6.19 && (./configure --host=$(HOST)  CC=$(CROSS_COMPILE)gcc); \
	fi

sqlite3:
	if [ ! -f ./sqlite-3.6.22/Makefile ];then \
	cd sqlite-3.6.22 && (./configure --host=$(HOST)  CC=$(CROSS_COMPILE)gcc CFLAGS=-Os \
				--enable-threadsafe ); \
	fi

libvorbis:
	if [ ! -f ./libvorbis-1.2.3/Makefile ];then \
	cd libvorbis-1.2.3 && (./configure --host=$(HOST)  CC=$(CROSS_COMPILE)gcc \
					CFLAGS=-I$(MINI_DLNA_PATH)/libogg-1.1.4/include/ \
					LDFLAGS=-L$(MINI_DLNA_PATH)/libogg-1.1.4/src/.libs/ \
					); \
	fi
flac:
	if [ ! -f ./flac-1.2.1/Makefile ];then \
	cd flac-1.2.1 && (./configure --host=$(HOST)  CC=$(CROSS_COMPILE)gcc \
				CFLAGS=-I$(MINI_DLNA_PATH)/libogg-1.1.4/include/ \
				LDFLAGS=-L$(MINI_DLNA_PATH)/libogg-1.1.4/src/.libs/ \
				); \
	fi

libid3tag:
	if [ ! -f ./libid3tag-0.15.0b/Makefile ];then \
	cd libid3tag-0.15.0b && (./configure --host=$(HOST)  CC=$(CROSS_COMPILE)gcc \
				CPPFLAGS=-I$(MINI_DLNA_PATH)/zlib-1.2.8/ \
				LDFLAGS=-L$(MINI_DLNA_PATH)/zlib-1.2.8/ \
				); \
	fi

ifeq ($(LINUXDIR),$(BASEDIR)/kernel/linux-4.1)
ffmpeg:
	cd ffmpeg-3.4.1 && (./configure --enable-shared --enable-gpl --arch=arm \
				--cc=$(CROSS_COMPILE)gcc --enable-cross-compile \
				--disable-muxers --disable-encoders --disable-filters --arch=arm --target_os=linux \
				--extra-cflags="-fPIC -lgcc_s" \
				);
else ifeq ($(CONFIG_XDSL_PRODUCT),y)
ffmpeg:
	cd ffmpeg-3.4.1 && (./configure --enable-shared --enable-gpl --arch=mips \
				--cc=$(CROSS_COMPILE)gcc --enable-cross-compile \
				--disable-muxers --disable-encoders --disable-filters --arch=mips --target_os=linux \
				--extra-cflags=-fPIC \
				);
else
ffmpeg:
	cd ffmpeg-3.4.1 && (./configure --enable-shared --enable-gpl --arch=arm \
				--cc=arm-brcm-linux-uclibcgnueabi-gcc --enable-cross-compile \
				--disable-muxers --disable-encoders --disable-filters --arch=arm --target_os=linux \
				--extra-cflags=-fPIC \
				);
endif
				
jpeg:
	cd jpeg-7 && (./configure --host=$(HOST)  CC=$(CROSS_COMPILE)gcc ); 
				
clean:
	for i in ${SUBDIRS}; do \
		(cd $$i; make clean); \
	done
	rm ./lib/libz.so.1
	rm ./lib/libvorbis.so.0
	rm ./lib/libFLAC.so.8
	rm ./lib/libogg.so.0
	rm ./lib/libexif.so.12
	rm ./lib/libavutil.so.55
	rm ./lib/libavdevice.so.57
	rm ./lib/libavformat.so.57
	rm ./lib/libswresample.so.2
	rm ./lib/libavcodec.so.57
	rm ./lib/libsqlite3.so.0
	rm ./lib/libjpeg.so.7
	rm ./lib/libid3tag.so.0
	rm ./lib/minidlna.exe
	rm -f ./lib/*.so
copy:
	cp ./zlib-1.2.8/libz.so.1.2.8				./lib/libz.so.1 && \
	cp ./zlib-1.2.8/libz.so.1.2.8				./lib/libz.so && \
	cp ./libvorbis-1.2.3/lib/.libs/libvorbis.so.0.4.3	./lib/libvorbis.so.0 && \
	cp ./libvorbis-1.2.3/lib/.libs/libvorbis.so.0.4.3	./lib/libvorbis.so && \
	cp ./flac-1.2.1/src/libFLAC/.libs/libFLAC.so.8.2.0	./lib/libFLAC.so.8 && \
	cp ./flac-1.2.1/src/libFLAC/.libs/libFLAC.so.8.2.0	./lib/libFLAC.so && \
	cp ./libexif-0.6.19/libexif/.libs/libexif.so.12.3.1 	./lib/libexif.so.12  && \
	cp ./libexif-0.6.19/libexif/.libs/libexif.so.12.3.1 	./lib/libexif.so  && \
	cp ./libogg-1.1.4/src/.libs/libogg.so.0.6.0		./lib/libogg.so.0 && \
	cp ./libogg-1.1.4/src/.libs/libogg.so.0.6.0		./lib/libogg.so && \
	cp ./ffmpeg-3.4.1/libavutil/libavutil.so.55		./lib/libavutil.so.55 && \
	cp ./ffmpeg-3.4.1/libavdevice/libavdevice.so.57		./lib/libavdevice.so.57 && \
	cp ./ffmpeg-3.4.1/libavformat/libavformat.so.57		./lib/libavformat.so.57 && \
	cp ./ffmpeg-3.4.1/libavcodec/libavcodec.so.57		./lib/libavcodec.so.57 && \
	cp ./ffmpeg-3.4.1/libswresample/libswresample.so.2	./lib/libswresample.so.2 && \
	cp ./ffmpeg-3.4.1/libavutil/libavutil.so.55		./lib/libavutil.so && \
	cp ./ffmpeg-3.4.1/libavdevice/libavdevice.so.57		./lib/libavdevice.so && \
	cp ./ffmpeg-3.4.1/libavformat/libavformat.so.57		./lib/libavformat.so && \
	cp ./ffmpeg-3.4.1/libavcodec/libavcodec.so.57		./lib/libavcodec.so && \
	cp ./ffmpeg-3.4.1/libavfilter/libavfilter.so.6		./lib/libavfilter.so && \
	cp ./ffmpeg-3.4.1/libavfilter/libavfilter.so.6		./lib/libavfilter.so.6 && \
	cp ./sqlite-3.6.22/.libs/libsqlite3.so.0.8.6 		./lib/libsqlite3.so.0 && \
	cp ./sqlite-3.6.22/.libs/libsqlite3.so.0.8.6 		./lib/libsqlite3.so && \
	cp ./jpeg-7/.libs/libjpeg.so.7.0.0			./lib/libjpeg.so.7 && \
	cp ./jpeg-7/.libs/libjpeg.so.7.0.0			./lib/libjpeg.so && \
	cp ./libid3tag-0.15.0b/.libs/libid3tag.so.0.2.0 	./lib/libid3tag.so.0

distclean:
	cd ./libogg-1.1.4/	&& make distclean 
	cd ./libexif-0.6.19/	&& make distclean
	cd ./sqlite-3.6.22/	&& make distclean
	cd ./libvorbis-1.2.3/	&& make distclean
	cd ./libid3tag-0.15.0b/	&& make distclean
	cd ./flac-1.2.1/	&& make distclean
	cd ./zlib-1.2.8/	&& make clean
	cd ./ffmpeg-3.4.1/	&& make clean
	cd ./jpeg-7/		&& make clean
	cd ./minidlna-1.1.5/		&& make clean
	rm ./lib/*.so.*
	rm ./lib/minidlna.exe

install:
	$(CROSS_COMPILE)strip ./lib/*
	install -m 755 minidlna-1.1.5/minidlna.conf $(TARGETDIR)/usr/
	install -m 755 lib/libz.so.1  $(TARGETDIR)/lib
	install -m 755 lib/libvorbis.so.0 $(TARGETDIR)/lib
	install -m 755 lib/libFLAC.so.8 $(TARGETDIR)/lib
	install -m 755 lib/libexif.so.12 $(TARGETDIR)/lib
	install -m 755 lib/libogg.so.0 $(TARGETDIR)/lib
	install -m 755 lib/libavutil.so.55 $(TARGETDIR)/lib
	install -m 755 lib/libavdevice.so.57 $(TARGETDIR)/lib
	install -m 755 lib/libavformat.so.57 $(TARGETDIR)/lib
	install -m 755 lib/libswresample.so.2 $(TARGETDIR)/lib
	install -m 755 lib/libavcodec.so.57 $(TARGETDIR)/lib
	install -m 755 lib/libavfilter.so.6 $(TARGETDIR)/lib
	install -m 755 lib/libsqlite3.so.0 $(TARGETDIR)/lib
	install -m 755 lib/libjpeg.so.7 $(TARGETDIR)/lib
	install -m 755 lib/libid3tag.so.0 $(TARGETDIR)/lib
	install -m 755 lib/minidlna.exe $(TARGETDIR)/usr/sbin/


