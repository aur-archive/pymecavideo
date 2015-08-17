# Maintainer: Djibb <ashashiwa@gmail.com>
# Contributor: Daneel <aur@gnoulibre.org>

# Pymecavideo permet de tracer point par point la trajectoire de point ainsi
# que choisir un référentiel particulier pour étudier la trajectoire dans
# celui-ci. Les données ainsi recueillies peuvent être exportées dans un
# logiciel de traitement.

pkgname=pymecavideo
pkgver=462
pkgrel=2
pkgdesc="Tool allowing to study trajectory of object from video."
arch=(i686 x86_64)
url="http://outilsphysiques.tuxfamily.org/pmwiki.php/Oppl/Pymecavideo"
license=('GPL3')
makedepends=('subversion' 'docbook-xsl' 'libxslt')
depends=('python2-pyqt4' 'ffmpeg' 'opencv' 'python2-matplotlib')
optdepends=('gnuplot: draw graph'
			'pyuno: LibO import'
			'mplayer: make coffee')
source=()
md5sums=()

_svntrunk="svn://svn.tuxfamily.org/svnroot/oppl/pymecavideo/branches/6.1"
_svnmod="pymecavideo"

build() {

	cd $srcdir

	msg "Connecting to SVN server..."

	if [ -d $pkgname-$pkgver/.svn ]; then
		(cd $pkgname-$pkgver && svn up -r $pkgver)
	else
		svn co $_svntrunk --config-dir ./ -r $pkgver $pkgname-$pkgver
	fi

	msg "SVN checkout done or server timeout"

	cd $srcdir/$pkgname-$pkgver

	msg "Patching..."

	sed -i 's@pyuic4@python2-pyuic4@' src/Makefile
	sed -i 's@lrelease @$(LRELEASE) @' src/Makefile
	sed -i 's@stylesheet/nwalsh@xsl-stylesheets-*@' data/help/Makefile
	sed -i 's@"python @"python2 @' src/cadreur.py
	sed -i 's@/usr/share/python-mecavideo@/usr/lib/python2.7/site-packages/pymecavideo/data@g' src/globdef.py
	sed -i 's@/usr/share/doc/HTML/fr/pymecavideo@/usr/lib/python2.7/site-packages/pymecavideo/data/help@g' src/globdef.py

	msg "Starting make..."

	cd $srcdir/$pkgname-$pkgver/src
	make || return 1

	msg "Install..."

	cd $srcdir/$pkgname-$pkgver
	python2 setup.py install --root=$pkgdir/ --optimize=1 || return 1

	cat > $srcdir/$pkgname.sh << EOF
#!/bin/sh
if [ ! -d ~/.local/share/data/pymecavideo ]; then
    mkdir -p ~/.local/share/data/pymecavideo/videos_recodees
fi
python2 /usr/lib/python2.7/site-packages/pymecavideo/pymecavideo.py \$*
EOF

	cat > $srcdir/$pkgname.desktop << EOF
[Desktop Entry]
Encoding=UTF-8
Version=5.4
Type=Application
Exec=/usr/bin/pymecavideo
Icon=/usr/share/pixmaps/pymecavideo.xpm
Terminal=false
Name=pyMecaVideo
GenericName=Meca Video
GenericName[fr_FR]=Méca Vidéo
Comment=Interactive tool to track moving points in video framesets
Comment[fr_FR]=Outil interactif pour le suivi de points dans des vidéos
Categories=KDE;GNOME;Application;Video;Education;Science;Physics
EOF
	
	install -D -m644 $srcdir/$pkgname-$pkgver/COPYING $pkgdir/usr/share/licenses/$pkgname/LICENSE
	install -D -m644 $srcdir/$pkgname-$pkgver/data/icones/$pkgname.xpm $pkgdir/usr/share/pixmaps/$pkgname.xpm
	install -dD -m755 $srcdir/$pkgname-$pkgver/data $pkgdir/usr/lib/python2.7/site-packages/$pkgname/data
	cp -r $srcdir/$pkgname-$pkgver/data/* $pkgdir/usr/lib/python2.7/site-packages/$pkgname/data/
	install -D -m644 $srcdir/$pkgname.desktop $pkgdir/usr/share/applications/$pkgname.desktop
	install -D -m644 $srcdir/$pkgname-$pkgver/$pkgname.xml $pkgdir/usr/share/mime/packages/$pkgname.xml
	install -D -m755 $srcdir/$pkgname.sh $pkgdir/usr/bin/$pkgname

	msg "TODO: update-desktop-database ; update-mime-database"

}

