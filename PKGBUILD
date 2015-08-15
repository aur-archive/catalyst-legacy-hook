# Maintainer: Giuseppe Marco "zeld" Randazzo <gmrandazzo@gmail.com>
# Great Contributor: Vi0L0 (maintainer of other catalyst drivers)

pkgname=catalyst-legacy-hook
pkgver=12.6
pkgrel=1
pkgdesc="AMD/ATI drivers. Auto re-compile the fglrx module when kernel update (mkinitcpio's hook) "
arch=('i686' 'x86_64')
url="http://www.ati.amd.com"
license=('custom')
depends=('catalyst-legacy-utils' 'gcc-libs' 'gcc>4.0.0' 'make' 'patch')
replaces=('catalyst' 'catalyst-daemon')
conflicts=('catalyst-hook' 'catalyst-test' 'nvidia' 'xf86-video-ati' 'xf86-video-radeonhd' 'ati-dri' 'catalyst-daemon' 'catalyst' 'catalyst-generator')
provides=("catalyst=${pkgver}")
install=${pkgname}.install

#patch_file="fglrx-2.6.36.patch"
#patch_md5="eb58dbf993dbf8d77924055cc8caaeb6"
#patch_ver="2.6.36"

source=(
    http://www2.ati.com/drivers/legacy/12-6/amd-driver-installer-${pkgver}-legacy-x86.x86_64.zip
    hook-fglrx
    catalyst_build_module
    ati_make.sh
    makefile_compat.patch
    3.5-do_mmap.patch)
#    ${patch_file})

md5sums=('eada88ea8ec12e09f516527b5a5d55d6'
         '9126e1ef0c724f8b57d3ac0fe77efe2f'
         '44bb617cc36ae9b37d194f6c7905760f'
	 'f9866a8e58665f9a91251e3f8e7edc72'
	 '3e1b82bd69774ea808da69c983d6a43b'
	 'a450e2e3db61994b09e9d99d95bee837')
#         ${patch_md5})


build() {
  ## Unpack archive
    msg "Unpacking archive ..."
    /bin/sh ./amd-driver-installer-${pkgver}-legacy-x86.x86_64.run --extract archive_files 
}

package() {

    # License
      install -m755 -d "${pkgdir}/usr/share/licenses/${pkgname}" || return 1
      install -m644 "${srcdir}/archive_files/LICENSE.TXT" "${pkgdir}/usr/share/licenses/${pkgname}/" || return 1

      if [ "${CARCH}" = "i686" ]; then
	cd "${srcdir}/archive_files/arch/x86/usr" || return 1
	_lib=lib
      elif [ "${CARCH}" = "x86_64" ]; then
	cd "${srcdir}/archive_files/arch/x86_64/usr" || return 1
	_lib=lib64
      fi

    # Prepare modules source files
      _archdir=x86_64
      test "${CARCH}" = "i686" && _archdir=x86
      cd "${srcdir}/archive_files/"
      install -m755 -d "${pkgdir}/usr/share/ati/build_mod" || return 1
      install -m644 common/lib/modules/fglrx/build_mod/*.c \
                "${pkgdir}/usr/share/ati/build_mod/" || return 1
      install -m644 common/lib/modules/fglrx/build_mod/*.h \
                "${pkgdir}/usr/share/ati/build_mod/" || return 1
      install -m644 common/lib/modules/fglrx/build_mod/2.6.x/Makefile \
                "${pkgdir}/usr/share/ati/build_mod/" || return 1
      install -m644 arch/${_archdir}/lib/modules/fglrx/build_mod/libfglrx_ip.a \
                "${pkgdir}/usr/share/ati/build_mod/" || return 1
      install -m755 -d "${pkgdir}/usr/bin" || return 1
      install -m755 "${srcdir}/catalyst_build_module" "${pkgdir}/usr/bin" || return 1

    # modified ati's make.sh script
      install -m755 "${srcdir}/ati_make.sh" "${pkgdir}/usr/share/ati/build_mod/" || return 1

    # makefile patch to choose arch_compat_alloc_user_space or older compat_alloc_user_space
    # works only in combination with ati_make.sh script
      install -m644 "${srcdir}/makefile_compat.patch" "${pkgdir}/usr/share/ati/build_mod/" || return 1


      install -m644 "${srcdir}/3.5-do_mmap.patch" "${pkgdir}/usr/share/ati/build_mod/" || return 1

      install -m644 "${srcdir}/hook-fglrx" "${pkgdir}/usr/share/ati/build_mod/" || return 1

    # optional patch
#      for ver in ${patch_ver}; do
#      install -m644 "${srcdir}/${patch_file}" \
#                "${pkgdir}/usr/share/ati/build_mod/${ver}.patch" || return 1
#      done

    # hook fglrx
      install -m755 -d "${pkgdir}/usr/lib/initcpio/install" || return 1
      install -m644 "${srcdir}/hook-fglrx" "${pkgdir}/usr/lib/initcpio/install/fglrx" || return 1
}
