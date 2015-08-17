# $Id: PKGBUILD 49631 2009-08-14 06:17:04Z tobias $
# Maintainer: jdhore [ jdhore1 at gmail com ]

pkgname=vim73-hg
_basename=vim73
pkgver=2571
pkgrel=1
pkgdesc='Vi Improved, BETA VERSION'
arch=(i686 x86_64)
license=('custom:vim')
url="http://www.vim.org"
depends=('gpm' 'perl' 'python')
makedepends=('wget' 'sed' 'grep' 'gettext' 'mercurial')
conflicts=(vim)
provides=(vim=7.3)
source=(vimrc archlinux.vim)
md5sums=('6228d36c3702d4e9afc4d2a1edcc3aff'
         '10353a61aadc3f276692d0e17db1478e')
backup=(etc/vimrc)

_hgroot="https://vim.googlecode.com/hg/"
_hgrepo="vim"

fetch() {
  # update source
  cd $srcdir

  if [ -d ${_hgrepo} ]; then
    cd $srcdir/${_hgrepo}
    hg pull -u
    hg update ${_basename}
  else
    hg clone ${_hgroot}${_hgrepo} || return 1
    cd $srcdir/${_hgrepo}
    hg update ${_basename}
  fi



  msg "Mercurial checkout done or server timeout"
  msg "Starting make..."

  rm -rf "$srcdir/$_hgrepo-build"
  cp -R "$srcdir/$_hgrepo" "$srcdir/$_hgrepo-build"
}

build()
{
  fetch

  cd "$srcdir/$_hgrepo-build"

  sed -i 's|^.*\(#define SYS_.*VIMRC_FILE.*"\) .*$|\1|' src/feature.h
  sed -i 's|^.*\(#define VIMRC_FILE.*"\) .*$|\1|' src/feature.h
  # build party
  ./configure --prefix=/usr --localstatedir=/var/lib/vim --mandir=/usr/share/man \
  --with-compiledby=ArchLinux --with-features=big \
  --enable-gpm --enable-acl --with-x=no --disable-gui \
  --enable-multibyte --enable-cscope \
  --enable-perlinterp --enable-pythoninterp

  make || return 1
}

package() {
  cd "$srcdir/$_hgrepo-build"
  make VIMRCLOC=/etc DESTDIR=${pkgdir} install

  cd "$pkgdir/usr/bin"
  rm ex view                # provided by (n)vi in core

  # delete some manpages
  cd ${pkgdir}/usr/share/man
  rm -f {*/,}man1/ex.1 {*/,}man1/view.1      # provided by (n)vi
  rm -f {*/,}man1/evim.1                     # this does not make sense in the console version

  # patch runtime
  cd $pkgdir/usr/share/vim/vim73/
  sed -i "s/rpmsave/pacsave/;s/rpmnew/pacnew/;s/,\*\.ebuild/\0,PKGBUILD*,*.install/" filetype.vim

  # fix FS#17216
  sed -i 's|messages,/var|messages,/var/log/messages.log,/var|' \
    ${pkgdir}/usr/share/vim/vim73/filetype.vim


  install -Dm644 ${srcdir}/vimrc  ${pkgdir}/etc/vimrc
  install -Dm644 ${srcdir}/archlinux.vim  \
                 ${pkgdir}/usr/share/vim/vimfiles/archlinux.vim
  install -dm755 ${pkgdir}/usr/share/licenses/$pkgname
  cd ${pkgdir}/usr/share/licenses/$pkgname
  ln -s ../../vim/vim73/doc/uganda.txt license.txt
}

# vim:set ts=2 sw=2 et:
