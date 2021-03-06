# $OpenBSD: Makefile,v 1.43 2017/11/04 12:39:29 rsadowski Exp $

ONLY_FOR_ARCHS =	${GCC4_ARCHS} ${CLANG_ARCHS}

CATEGORIES =		graphics devel
COMMENT-docs =		OpenCV documentation and examples
COMMENT-main =		library of programming functions for real time cv
COMMENT-java =		Java bindings for OpenCV

V =			3.4.1
DISTNAME =		opencv-${V}
PKGNAME-main =		opencv-${V}
PKGNAME-docs =		opencv-docs-$V
PKGNAME-java =		opencv-java-${V}

HOMEPAGE =		https://www.opencv.org/

MAINTAINER =		Rafael Sadowski <rsadowski@openbsd.org>

.for i in opencv_calib3d opencv_core opencv_features2d \
  opencv_flann opencv_highgui opencv_imgproc \
  opencv_ml opencv_objdetect opencv_photo opencv_stitching \
  opencv_superres opencv_video opencv_videostab opencv_imgcodecs \
  opencv_shape opencv_videoio opencv_dnn
SHARED_LIBS += $i 4.0
.endfor

WANTLIB-main += ${COMPILER_LIBCXX} Half Iex IlmImf IlmThread Imath
WANTLIB-main += atk-1.0 avcodec avformat avresample avutil c cairo
WANTLIB-main += cairo-gobject gdk-3 gdk_pixbuf-2.0 gio-2.0 glib-2.0
WANTLIB-main += gobject-2.0 gphoto2 gphoto2_port gstapp-1.0 gstbase-1.0
WANTLIB-main += gstpbutils-1.0 gstreamer-1.0 gstriff-1.0 gstvideo-1.0
WANTLIB-main += gthread-2.0 gtk-3 intl jasper jpeg m pango-1.0 pangocairo-1.0
WANTLIB-main += png swscale tiff webp z xine

WANTLIB-java += ${COMPILER_LIBCXX} m opencv_calib3d opencv_core opencv_features2d
WANTLIB-java += opencv_flann opencv_highgui opencv_imgcodecs opencv_imgproc
WANTLIB-java += opencv_ml opencv_objdetect opencv_photo opencv_video
WANTLIB-java += opencv_videoio opencv_dnn

WANTLIB-docs =

MASTER_SITES =		${MASTER_SITE_SOURCEFORGE:=opencvlibrary/}
EXTRACT_SUFX =		.zip

MULTI_PACKAGES =	-main -docs -java
PSEUDO_FLAVORS =	no_docs no_java
FLAVOR ?=

# BSDL
PERMIT_PACKAGE_CDROM =	Yes

.include <bsd.port.arch.mk>

MODULES =		lang/clang \
			devel/cmake \
			lang/python

BUILD_DEPENDS =		devel/doxygen>=1.8.10 \
			math/py-numpy \
			math/eigen3

RUN_DEPENDS-main =	math/py-numpy
RUN_DEPENDS-docs =	${BUILD_PKGPATH},-main
RUN_DEPENDS-java =	${MODJAVA_RUN_DEPENDS}

LIB_DEPENDS-main =	${LIB_DEPENDS} \
			multimedia/gstreamer1/plugins-base \
			x11/gtk+3 \
			multimedia/xine-lib \
			graphics/ffmpeg \
			graphics/ilmbase \
			graphics/jpeg \
			graphics/libgphoto2 \
			graphics/libwebp \
			graphics/openexr \
			graphics/png \
			graphics/tiff

LIB_DEPENDS-java =	${BUILD_PKGPATH},-main=${V}

# XXX PIE cannot be produced due to problems with inline assembly.
# Since OpenCV is mostly used as a LIBrary, switch to PIC.
.if ${ARCH:Mi386}
CONFIGURE_ENV =		CFLAGS="${CFLAGS} -fPIC" \
			CXXFLAGS="${CXXFLAGS} -fPIC"
.endif

# ATM opencv requires V4L1 compat, ask them to change it,
# then see WITH_V4L below.
CONFIGURE_ARGS =	-DWITH_1394:Bool=Off \
			-DWITH_VTK:Bool=Off \
			-DWITH_CUDA:Bool=Off \
			-DWITH_EIGEN:Bool=Off \
			-DWITH_V4L:Bool=Off \
			-DWITH_IPP:Bool=Off \
			-DWITH_OPENCL:Bool=Off \
			-DWITH_XINE:Bool=On \
			-DBUILD_ITT:Bool=Off \
			-DBUILD_IPP_IW:Bool=Off

#-DENABLE_SSE3:Bool=Off \
#-DBUILD_TESTS:Bool=On

#CONFIGURE_ARGS +=	-DCMAKE_POLICY_DEFAULT_CMP0058=NEW

.if ${BUILD_PACKAGES:M-docs}
BUILD_DEPENDS +=	textproc/py-sphinx
ALL_TARGET =		all doxygen
CONFIGURE_ARGS +=	-DBUILD_DOCS:Bool=On \
			-DBUILD_EXAMPLES:Bool=On \
			-DINSTALL_C_EXAMPLES:Bool=On \
			-DINSTALL_PYTHON_EXAMPLES:Bool=On
.else
CONFIGURE_ARGS +=	-DBUILD_DOCS:Bool=Off \
			-DBUILD_EXAMPLES:Bool=Off \
			-DINSTALL_PYTHON_EXAMPLES:Bool=Off
.endif

.if ${BUILD_PACKAGES:M-java}
MODULES +=		java
MODJAVA_VER =		1.8+
BUILD_DEPENDS +=	devel/apache-ant
.else
# Safe: Java will be detected, if present, but won't be used
CONFIGURE_ARGS +=	-DBUILD_opencv_java:Bool=Off
.endif

CONFIG_ADJ_CMD =	perl -pi
.for _l _v in ${SHARED_LIBS}
CONFIG_ADJ_CMD +=	-e 's,lib${_l}.so([^.]),lib${_l}.so.${_v}$$1,g;'
.endfor

TEST_IS_INTERACTIVE =	X11

post-patch:
	perl -pi -e 's@^.*(#\s*include)@$$1@' \
		${WRKSRC}/samples/cpp/tutorial_code/core/how_to_scan_images/how_to_scan_images.cpp

post-install:
	${CONFIG_ADJ_CMD} ${PREFIX}/share/OpenCV/OpenCVConfig.cmake
	${MODPY_BIN} ${MODPY_LIBDIR}/compileall.py ${WRKINST}${MODPY_SITEPKG}

do-test:
	cd ${WRKBUILD}; \
	${MODPY_BIN} ${WRKSRC}/modules/ts/misc/run.py

.include <bsd.port.mk>
