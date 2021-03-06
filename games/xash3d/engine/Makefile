# $OpenBSD$

COMMENT =	Flying With Gauss Goldsrc-compatible game engine

GH_ACCOUNT =	FWGS
GH_PROJECT =	xash3d
GH_TAGNAME =	v0.19.1

MAINUI_COMMIT = af924f8d17815438645d9c7c415602b853148f8f

CATEGORIES =	games

HOMEPAGE =	http://xash.su

MAINTAINER =	ryan@slipgate.org

# Mixed license, GPL v3+ for engine library, MIT for cmake files
# Source: https://github.com/FWGS/xash3d/blob/master/README.md
PERMIT_PACKAGE_CDROM =	Yes

WANTLIB += ${COMPILER_LIBCXX} c fontconfig freetype m z SDL2

MASTER_SITES0 =	https://github.com/FWGS/mainui_cpp/archive/

DISTFILES =	${DISTNAME}${EXTRACT_SUFX} \
		${MAINUI_COMMIT}.tar.gz:0

MODULES =		devel/cmake
RUN_DEPENDS =		games/xash3d/hl
LIB_DEPENDS =		devel/sdl2

COMPILER =		base-clang ports-gcc

CONFIGURE_ARGS =	-DXASH_SINGLE_BINARY=yes \
			-DXASH_SDK=${WRKSRC}

.include <bsd.port.arch.mk>
.if ${ARCH:M${LP64_ARCHS}}
CONFIGURE_ARGS +=	-DXASH_64BIT=yes
.endif

CONFIGURE_ENV =		CFLAGS="-I${X11BASE}/include" LDFLAGS="-L${X11BASE}/lib"

NO_TEST =		Yes

post-extract:
	rmdir ${WRKSRC}/mainui
	ln -s ${WRKDIR}/mainui_cpp-${MAINUI_COMMIT} ${WRKSRC}/mainui
	${SUBST_CMD} -m 0755 -c ${FILESDIR}/xash3d.sh ${WRKSRC}/scripts/xash3d.sh

post-install:
	${INSTALL_DATA_DIR} ${PREFIX}/share/xash3d

.include <bsd.port.mk>
