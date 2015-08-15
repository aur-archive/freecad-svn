# Maintainer: Giuseppe Borzi <gborzi@ieee.org>
# Contributor : Omar Lakhdar <omar_lakhdar@hotmail.com>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: Joaquim Coimbra <joaca_rj@yahoo.com.br>
# Contributor: Michele Mocciola <mickele>
# Contributor : FoolEcho <Archlinux.fr>
# Thanks to Werner Mayer for his support

pkgname=freecad-svn
pkgver=5330
pkgrel=1
pkgdesc='A general purpose 3D CAD modeler'
arch=('i686' 'x86_64')
url='http://sourceforge.net/apps/mediawiki/free-cad/'
license=('GPL')
depends=('boost-libs=1.48.0' 'opencascade' 'pivy-hg' 'python2-pyqt' 'xerces-c' 'libspnav')
makedepends=('boost=1.48.0' 'eigen3' 'gcc-fortran' 'subversion' 'swig1')
options=(!libtool !makeflags)
source=("${pkgname}.desktop")
md5sums=('e7fcfa91beee620117c06482f4df44f8')

_svnmod='freecad'
_svntrunk='https://free-cad.svn.sourceforge.net/svnroot/free-cad/trunk'

build() {
  cd "$srcdir"

  if [ -d $_svnmod/.svn ]; then
    (cd $_svnmod && svn up -r $pkgver)
  else
    svn co $_svntrunk --config-dir ./ -r $pkgver $_svnmod
  fi

  msg "SVN checkout done or server timeout"
  msg "Starting make..."

  rm -rf "$srcdir/$_svnmod-build"
  cp -r "$srcdir/$_svnmod" "$srcdir/$_svnmod-build"
  cd "$srcdir/$_svnmod-build"

  # fix boost*-mt libs
  sed -i -e 's/-lboost\(.*\)-mt/-lboost\1/' acinclude.m4
  ./autogen.sh

  ./configure \
    --prefix=/usr/lib/${pkgname} \
    --datadir=/usr/share/${pkgname} \
    --includedir=/usr/include/${pkgname} \
    --docdir=/usr/share/doc/${pkgname} \
    --with-qt4-bin=/usr/bin/ \
    --with-qt4-include=/usr/include/ \
    --with-qt4-lib=/usr/lib/ \
    PYTHON=/usr/bin/python2 \
   --with-python-include=/usr/include/python2.7/ \
    --with-python-lib=/usr/lib/python2.7/ \
    --with-boost-include=/usr/include/boost \
    --with-boost-lib=/usr/lib \
    --with-occ-lib=/opt/opencascade/lib \
    --with-occ-include=/opt/opencascade/inc

  # prepare for compilation
  # fix Driver_Document.h problem
  sed -i -e 's#AM_CPPFLAGS = -I$(OCC_INC) -I$(srcdir)/$(smeshdir)inc/#& -I./inc/#' src/3rdParty/salomesmesh/Makefile

  make
}

package() {
  cd "$srcdir/$_svnmod-build"

  make DESTDIR=${pkgdir} install
  sed -i -e 's_/usr/bin/python_&2_' "$pkgdir/usr/lib/freecad-svn/Mod/Robot/MovieTool.py"

  install -Dm644 "${srcdir}/${pkgname}.desktop" "${pkgdir}/usr/share/applications/${pkgname}.desktop"
  install -dm755 "${pkgdir}/usr/bin"
  ln -sf /usr/lib/${pkgname}/bin/FreeCAD "${pkgdir}/usr/bin/${pkgname}"
  ln -sf /usr/lib/${pkgname}/bin/FreeCADCmd "${pkgdir}/usr/bin/freecadcmd-svn"
}
