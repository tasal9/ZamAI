You can install Neovim from download, package, or source in just a few seconds.

To start Neovim, run nvim (not neovim).
Before upgrading to a new version, check Following HEAD.
For config (vimrc) see the FAQ.
Install from download
Downloads are available on the Releases page.

Latest stable release
Latest development prerelease
Install from package
Packages are listed below. (You can also build Neovim from source.)

Windows
Chocolatey
Release (v0.4): choco install neovim (use -y for automatically skipping confirmation messages)
Development (pre-release): choco install neovim --pre
Scoop
Release: scoop install neovim
Development (pre-release):
scoop bucket add versions
scoop install neovim-nightly
Pre-built archives
If you are missing VCRUNTIME140.dll, install the Visual Studio 2015 C++ redistributable (choose x86_64 or x86 depending on your system).
Choose a package (nvim-winXX.zip) from the releases page.
Unzip the package. Any location is fine, administrator privileges are not required.
$VIMRUNTIME will be set to that location automatically.
Double-click nvim-qt.exe.
Optional steps:

Add the bin folder (e.g. C:\Program Files\nvim\bin) to your PATH.
This makes it easy to run nvim and nvim-qt from anywhere.
If :set spell does not work, create the C:/Users/foo/AppData/Local/nvim/site/spell folder. You can then copy your spell files over (for English, located here and here);
For Python plugins you need the pynvim module. "Virtual envs" are recommended. After activating the virtual env do pip install pynvim (in both). Edit your init.vim so that it contains the path to the env's Python executable:
let g:python3_host_prog='C:/Users/foo/Envs/neovim3/Scripts/python.exe'
let g:python_host_prog='C:/Users/foo/Envs/neovim/Scripts/python.exe'
Run :checkhealth and read :help provider-python.
init.vim ("vimrc"): If you already have Vim installed you can copy %userprofile%\_vimrc to %userprofile%\AppData\Local\nvim\init.vim to use your Vim config with Neovim.
macOS / OS X
Pre-built archives
The Releases page provides pre-built binaries for macOS 10.11+.

curl -LO https://github.com/neovim/neovim/releases/download/nightly/nvim-macos.tar.gz
tar xzf nvim-macos.tar.gz
./nvim-osx64/bin/nvim
Homebrew on macOS or Linux
brew install neovim
Or install the development version of Nvim:

brew install --HEAD luajit
brew install --HEAD neovim
To update the development version of Nvim:

brew reinstall neovim
Macports
sudo port selfupdate
sudo port install neovim
Linux
AppImage ("universal" Linux package)
The Releases page provides an AppImage that runs on most Linux systems. No installation is needed, just download nvim.appimage and run it. (It might not work if your Linux distribution is more than 4 years old.)

curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim.appimage
chmod u+x nvim.appimage
./nvim.appimage
If the ./nvim.appimage command fails, try:

./nvim.appimage --appimage-extract
./squashfs-root/AppRun --version

# Optional: exposing nvim globally
mv squashfs-root / && ln -s /squashfs-root/AppRun /usr/bin/nvim
nvim
Arch Linux
Neovim can be installed from the community repository:

sudo pacman -S neovim
Alternatively, Neovim can be also installed using the PKGBUILD neovim-git, available on the AUR.

Alternatively, Neovim Nightly builds can be also installed using the PKGBUILD neovim-nightly-bin, available on the AUR [this PKGBUILD is actually marked as out of date (as of april 29th 2021)]

The Python module is available from the community repository:

sudo pacman -S python-pynvim
Python 2 and Ruby modules (currently only supported in neovim-git) are available from the AUR as python2-neovim and ruby-neovim respectively.

CentOS 8 / RHEL 8
Neovim is available through EPEL (Extra Packages for Enterprise Linux)

yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
yum install -y neovim python36-neovim
# you might need python2-neovim as well on older Fedora releases
Clear Linux OS
NeoVim is available through the neovim bundle

sudo swupd bundle-add neovim
Python (:python) support is available if the python-basic bundle is installed.

sudo swupd bundle-add python-basic
CRUX
A CRUX port is available under 6c37/neovim, along with ports for other dependencies of Neovim.

For instructions on how to install the Python modules, see :help provider-python.

Debian
Neovim is in Debian.

sudo apt-get install neovim
Python (:python) support is installable via the package manager on Debian unstable.

sudo apt-get install python-neovim
sudo apt-get install python3-neovim
If installing via appimage, the following commands may be helpful in updating default paths:

# CUSTOM_NVIM_PATH=/usr/local/bin/nvim.appimage
# Set the above with the correct path, then run the rest of the commands:
set -u
sudo update-alternatives --install /usr/bin/ex ex "${CUSTOM_NVIM_PATH}" 110
sudo update-alternatives --install /usr/bin/vi vi "${CUSTOM_NVIM_PATH}" 110
sudo update-alternatives --install /usr/bin/view view "${CUSTOM_NVIM_PATH}" 110
sudo update-alternatives --install /usr/bin/vim vim "${CUSTOM_NVIM_PATH}" 110
sudo update-alternatives --install /usr/bin/vimdiff vimdiff "${CUSTOM_NVIM_PATH}" 110
Exherbo Linux
Exhereses for scm and released versions are currently available in repository ::medvid. Python client (with GTK+ GUI included) and Qt5 GUI are also available as suggestions:

cave resolve app-editors/neovim --take dev-python/neovim-python --take app-editors/neovim-qt
Fedora
Neovim is in Fedora starting with Fedora 25:

sudo dnf install -y neovim python3-neovim
You can also get nightly builds of git master from the Copr automated build system:

dnf copr enable agriffis/neovim-nightly
dnf install -y neovim python{2,3}-neovim
See the blog post for information on how these are built.

Flatpak
You can find Neovim on Flathub. Providing you have Flatpak set up:

flatpak install flathub io.neovim.nvim
flatpak run io.neovim.nvim
You can add /var/lib/flatpak/exports/bin (or ~/.local/share/flatpak/exports/bin if you used --user) to the $PATH and run it with io.neovim.nvim.

Note that Flatpak'ed Neovim will look for init.vim in ~/.var/app/io.neovim.nvim/config/nvim instead of ~/.config/nvim.

Gentoo Linux
An ebuild is available in Gentoo's official portage repository:

emerge -a app-editors/neovim
GNU Guix
Neovim can be installed with:

guix install neovim
Nix / NixOS
Neovim can be installed with:

nix-env -iA nixpkgs.neovim
Mageia 7
urpmi neovim
To install the Python modules:

urpmi python2-pynvim python3-pynvim
OpenSUSE
Neovim can be installed with:

sudo zypper in neovim
To install the Python modules:

sudo zypper in python-neovim python3-neovim
PLD Linux
Neovim is in PLD Linux:

poldek -u neovim
poldek -u python-neovim python3-neovim
poldek -u python-neovim-gui python3-neovim-gui
Slackware
See neovim on SlackBuilds.

For instructions on how to install the Python modules, see :help provider-python.

Source Mage
Neovim can be installed using the Sorcery package manager:

cast neovim
Solus
Neovim can be installed using the default package manager in Solus (eopkg):

sudo eopkg install neovim
Snap
Neovim nightly and stable are available on the snap store.

Stable Builds

sudo snap install --beta nvim --classic
Nightly Builds

sudo snap install --edge nvim --classic
Ubuntu
Since 18.04 via official repository As in Debian, Neovim is in Ubuntu.

sudo apt install neovim
Python (:python) support seems to be automatically installed

sudo apt install python-neovim
sudo apt install python3-neovim
Since 18.04 via PPA Neovim has been added to a "Personal Package Archive" (PPA). This allows you to install it with apt-get. Follow the links to the PPAs to see which versions of Ubuntu are currently available via the PPA. Choose stable or unstable:

https://launchpad.net/~neovim-ppa/+archive/ubuntu/stable (Bionic Beaver 18.04 or newer only).
https://launchpad.net/~neovim-ppa/+archive/ubuntu/unstable
Important: those PPA have dropped support for Xenial 16.04 and older.

To be able to use add-apt-repository you may need to install software-properties-common:

sudo apt-get install software-properties-common
If you're using an older version Ubuntu you must use:

sudo apt-get install python-software-properties
Run the following commands:

sudo add-apt-repository ppa:neovim-ppa/stable
sudo apt-get update
sudo apt-get install neovim
Prerequisites for the Python modules:

sudo apt-get install python-dev python-pip python3-dev python3-pip
If you're using an older version Ubuntu you must use:

sudo apt-get install python-dev python-pip python3-dev
sudo apt-get install python3-setuptools
sudo easy_install3 pip
For instructions to install the Python modules, see :help provider-python.

If you want to use Neovim for some (or all) of the editor alternatives, use the following commands:

sudo update-alternatives --install /usr/bin/vi vi /usr/bin/nvim 60
sudo update-alternatives --config vi
sudo update-alternatives --install /usr/bin/vim vim /usr/bin/nvim 60
sudo update-alternatives --config vim
sudo update-alternatives --install /usr/bin/editor editor /usr/bin/nvim 60
sudo update-alternatives --config editor
Note, however, that special interfaces, like view for nvim -R, are not supported. (See #1646 and #2008.)

Void-Linux
Neovim can be installed using the xbps package manager

sudo xbps-install -S neovim
Alpine Linux
Neovim can be installed using the apk package manager

sudo apk add neovim
BSD
FreeBSD
Neovim can be installed using pkg(8):

pkg install neovim
or from the ports tree:

cd /usr/ports/editors/neovim/ && make install clean
To install the pynvim Python modules using pkg(8) run:

pkg install py27-pynvim py36-pynvim
OpenBSD
Neovim can be installed using pkg_add(1):

pkg_add neovim
or from the ports tree:

cd /usr/ports/editors/neovim/ && make install
Android
Termux on the Google Play store offers a Neovim package.

Install from source
If a package is not provided for your platform, you can build Neovim from source. See Building-Neovim for details. If you have the prerequisites then building is easy:

make CMAKE_BUILD_TYPE=Release
sudo make install
For Unix-like systems this installs Neovim to /usr/local, while for Windows to C:\Program Files. Note, however, that this can complicate uninstallation. The following example avoids this by isolating an installation under $HOME/neovim:

rm -r build/  # clear the CMake cache
make CMAKE_EXTRA_FLAGS="-DCMAKE_INSTALL_PREFIX=$HOME/neovim"
make install
export PATH="$HOME/neovim/bin:$PATH"
Uninstall
To uninstall after make install, just delete the CMAKE_INSTALL_PREFIX artifacts:

sudo rm /usr/local/bin/nvim
sudo rm -r /usr/local/share/nvim/