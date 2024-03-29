# Fedora Kinoite Einrichtung
Fedora Kinoite ist eine sehr einfache, moderne und freundliche Distribution.

Durch das uBlue Projekt kann man auch hier alle nötigen Treiber, Mediacodecs und mehr bekommen.

## Installieren

Download von [Fedoras Website](https://fedoraproject.org/atomic-desktops/kinoite/) , auf einen USB-Stick brennen mit dem [Fedora Media Writer](https://flathub.org/apps/org.fedoraproject.MediaWriter) von Flathub oder derselben Website. Ventoy funktioniert nicht zuverlässig.

Wichtig bei der Einrichtung:

- Keinen root Account erstellen
- Festplatte mit LUKS verschlüsseln
- Sichere Passwörter verwenden (hauptsächlich für die Festplattenverschlüsselung)
- Unter "Netzwerk" kann man den Hostname des PCs zu "PC" umbenennen, was ihn unauffällig in lokalen Netzwerken macht


# Nach der Installation

Wechseln zu Kinoite von [uBlue](https://universal-blue.org): 

normal:
```
rpm-ostree rebase --reboot ostree-unverified-registry:ghcr.io/ublue-os/kinoite-main:($rpm -E %fedora)

# nach dem neutart, wichtig
rpm-ostree rebase --reboot ostree-image-signed:docker://ghcr.io/ublue-os/kinoite-main:($rpm -E %fedora)
```

Für nVIDIA, Framework, Asus, Surface und andere Varianten, [ein Abbild aus dieser Liste wählen](https://github.com/orgs/ublue-os/packages).

### Nach dem Neustart

Terminal öffnen, das hier einfügen

```
cat <<EOF







==========================================

Fedora Kinoite Einrichtung

Ein paar Tricks, um die Nutzung
zu vereinfachen!

===========================================


EOF

echo "Fedora Flaptak repository mit Flathub tauschen"
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
flatpak remote-delete fedora -y

cat <<EOF
# Mehr Flatpak repositories
github.com/trytomakeyouprivate/flatpak-remotes

# Empfohlene Flatpak-Apps
github.com/trytomakeyouprivate/recommended-flatpak-apps

# Alias deiner Flatpaks zum einfachen Öffnen 
github.com/trytomakeyouprivate/flatalias

# Löschen ungenutzter Daten alter Flatpak apps
github.com/trytomakeyouprivate/flatpak-trash-remover

# COPR Repositories einfach hinzufügen
github.com/trytomakeyouprivate/copr-command

EOF

xdg-open github.com/trytomakeyouprivate/flatpak-remotes
xdg-open github.com/trytomakeyouprivate/recommended-flatpak-apps
xdg-open github.com/trytomakeyouprivate/flatalias
xdg-open github.com/trytomakeyouprivate/flatpak-trash-remover
xdg-open github.com/trytomakeyouprivate/copr-command

echo "Discover Quellen aktualisieren"
pkcon refresh

echo "Baloo deaktivieren (verursacht Probleme mit ostree)"
balooctl disable && balooctl purge

echo "Geoclue Location Service deaktivieren"
systemctl disable geoclue
touch ~/.local/share/applications/geoclue-demo-agent.desktop

echo "DiscoverNotifier deaktivieren"
touch ~/.local/share/applications/org.kde.discover.notifier.desktop

echo "Ein paar gute Bash-Aliasse hinzufügen"
cat >> ~/.bashrc <<EOF



# Some nice Bash shortcuts for easy usage

alias logout="qdbus org.kde.ksmserver /KSMServer logout 0 0 1"
alias update='flatpak update -y && notify-send -a "Updates" "Flatpaks updated" ; distrobox upgrade --all ; rpm-ostree update'
alias upfin='update && shutdown -h now'
alias rstat="rpm-ostree status"
alias fwup="fwupdmgr update"
alias flatrm="flatpak remove --delete-data"
alias rpmfind="rpm -qa | grep"

alias untar='tar -xvf'
alias "pin-this"="ostree admin pin 0"
alias q="exit"
alias c="clear"

# Mkdir Create Parent Directories
alias mkdir='mkdir -v'

# List (ls)
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CFl'

# NETWORKING
alias myip='curl ifconfig.co'
alias netlisten='netstat -plntu'
alias pingtest='ping -c 2 wikipedia.org'
alias httpcode="curl --head --silent --output /dev/null --write-out '%{http_code}' "

alias conf="kwrite ~/.bashrc"

EOF
```

### Some recommended Apps to layer

distrobox ist bereits installiert

Grundlegende Shell Apps
- `fish`
- `[rust coreutils](https://github.com/uutils/coreutils) ([gute COPR](https://copr.fedorainfracloud.org/coprs/salimma/rust-coreutils))`
- `helix` (vim in rust)
- `bat eza glow` und weitere coreutil Ersatze in Rust, die nicht 1:1 kompatibel sind 

Virtualisierung
- `virt-manager qemu qemu-kvm`
- andere Architekturen finden mit `rpm-ostree search qemu-system`

### Firmware updaten

```
fwupdmgr update
```
