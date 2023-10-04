# Maintainer: Fredy García <frealgagu at gmail dot com>
# Contributor: klepz <felipe.junger@aluno.ufabc.edu.br>
# Contributor: Dave Blair <mail@dave-blair.de>
# Contributor: James An <james@jamesan.ca>
# Contributor: Mateus Rodrigues Costa <charles [dot] costar [at] gmail [dot] com>

pkgname=chrome-remote-desktop
pkgrel=1
latest_info=$(curl -qs https://dl.google.com/linux/chrome-remote-desktop/deb/dists/stable/main/binary-amd64/Packages)
pkgver=$(echo "${latest_info}" | grep "^Version" | awk '{print $2}')
pkgsum=$(echo "${latest_info}" | grep "^SHA256" | awk '{print $2}')
pkgremotefile=$(echo "${latest_info}" | grep "^Filename" | awk '{print $2}')
pkgdesc="Access other computers or allow another user to access your computer securely over the Internet"
arch=("x86_64")
url="https://remotedesktop.google.com"
license=("BSD")
depends=("gtk3" "libutempter" "libxss" "nss" "python-packaging" "python-psutil" "python-pyxdg" "xf86-video-dummy" "xorg-server-xvfb" "xorg-setxkbmap" "xorg-xauth" "xorg-xdpyinfo" "xorg-xrandr")
install="${pkgname}.install"
source=(
  # "${pkgname}-${pkgver}.deb::https://dl.google.com/linux/${pkgname}/deb/pool/main/c/${pkgname}/${pkgname}_${pkgver}_amd64.deb"
  "${pkgname}-${pkgver}.deb::https://dl.google.com/linux/${pkgname}/deb/${pkgremotefile}"
  "${pkgname}.service"
  "pamrule"
  "crd"
  "xorg-binary-path.patch"
  "use-existing-session.patch"
)
sha256sums=(
  "${pkgsum}"
  "e5da5ae89b5bc599f72f415d1523341b25357931b0de46159fce50ab83615a4b"
  "fcc38269eb1cc902abff9688eda9377a22367e39b9f111f87c0dd8e77adb82e2"
  "021110f49d465294517eec92eeb24ebca41e264ef33cbdda78732add1f269d02"
  "90bcfab85a87cfa6d038a55c556206f74b22eb03644ea51f46732cfb27679963"
  "9a4029669ae5d166bad3bdaff7331af2b0a6e0a8ce87cf39fe83c54da0f47c32"
)

# Direct download of latest version
# https://dl.google.com/linux/direct/chrome-remote-desktop_current_amd64.deb

# Retrieve version and sha256sum
# curl -qs https://dl.google.com/linux/chrome-remote-desktop/deb/dists/stable/main/binary-amd64/Packages | grep "^Version\|^SHA256" | awk '{print $2}'

prepare() {
  cd "${srcdir}"

  bsdtar -xf data.tar.xz -C .
  
  # Removing unnecessary .deb related files
  rm -R "${srcdir}/etc/cron.daily"
  rm -R "${srcdir}/etc/init.d"
  rm -R "${srcdir}/etc/pam.d"

  # Fix xorg_binary location
  patch -Np1 -i "${srcdir}/xorg-binary-path.patch"
  patch -Np1 -i "${srcdir}/use-existing-session.patch"
}

package() {
  cd "${srcdir}"

  install -d "${pkgdir}/etc"
  install -d "${pkgdir}/opt"
  cp -r "${srcdir}/etc/"* "${pkgdir}/etc"
  cp -r "${srcdir}/opt/"* "${pkgdir}/opt"
  install -Dm644 "${srcdir}/usr/share/doc/${pkgname}/copyright" "${pkgdir}/usr/share/licenses/${pkgname}/copyright"
  install -Dm644 "${srcdir}/${pkgname}.service" "${pkgdir}/usr/lib/systemd/user/${pkgname}.service"
  install -Dm644 "${srcdir}/lib/systemd/system/${pkgname}@.service" "${pkgdir}/usr/lib/systemd/system/${pkgname}@.service"
  install -Dm644 "${srcdir}/pamrule" "${pkgdir}/etc/pam.d/${pkgname}"
  install -Dm755 "${srcdir}/crd" "${pkgdir}/usr/bin/crd"
  install -dm755 "${pkgdir}/etc/chromium/native-messaging-hosts"
  
  for _file in $(find "${pkgdir}/etc/opt/chrome/native-messaging-hosts" -type f); do
    local _filename=$(basename ${_file})
    if [[ ! -f "/etc/chromium/native-messaging-hosts/${_filename}" ]]; then
      ln -s "/etc/opt/chrome/native-messaging-hosts/${_filename}" "${pkgdir}/etc/chromium/native-messaging-hosts/${_filename}"
    fi
  done
  chmod u+s "${pkgdir}/opt/google/${pkgname}/user-session"
}
