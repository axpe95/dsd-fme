## How to clone and build this branch

### Ubuntu 22.04/20.04/LM20/Debian Bullseye or Newer:

Using the included download-and-install.sh should make for a simple and painless clone, build, and install on newer Debian/Ubuntu/Mint/Pi systems. Simply acquire or copy the script, and run it. Update: Ubuntu 23.04 and RPi Bullseye 64-bit has been tested working with the installer script and functions appropriately.

If you need all dependencies build and installed first (only on Debian/Ubuntu/Mint/Pi), run:

```
wget https://raw.githubusercontent.com/lwvmobile/dsd-fme/audio_work/download-and-install.sh
chmod +x download-and-install.sh
./download-and-install.sh
```

### Arch Based Distros:

```
wget https://raw.githubusercontent.com/lwvmobile/dsd-fme/audio_work/download-and-install-Arch.sh
chmod +x download-and-install-Arch.sh
./download-and-install-Arch.sh
```

### Other:

If you have dependencies already installed, please run this instead:

```
wget https://raw.githubusercontent.com/lwvmobile/dsd-fme/audio_work/download-and-install-nodeps.sh
chmod +x download-and-install-nodeps.sh
./download-and-install-nodeps.sh
```

## Manual Install

First, install dependency packages. This guide will assume you are using Debian/Ubuntu based distros. Check your package manager for equivalent packages if different.

Debian/Mint/Ubuntu/Pi

```
sudo apt update
sudo apt install libpulse-dev pavucontrol libsndfile1-dev libfftw3-dev liblapack-dev socat libusb-1.0-0-dev libncurses5 libncurses5-dev rtl-sdr librtlsdr-dev libusb-1.0-0-dev cmake git wget make build-essential libitpp-dev libncursesw5-dev libcodec2-dev
```

Fedora 36/37 -- from https://github.com/lwvmobile/dsd-fme/issues/99

```
sudo dnf update
sudo dnf install libsndfile-devel fftw-devel lapack-devel rtl-sdr-devel pulseaudio-libs-devel libusb-devel cmake git ncurses ncurses-devel gcc wget pavucontrol gcc-c++ codec2-devel
```

Arch -- https://github.com/lwvmobile/dsd-fme/issues/153 and https://github.com/lwvmobile/dsd-fme/issues/153

```
sudo pacman -Syu
sudo pacman -S libpulse cmake ncurses lapack perl fftw rtl-sdr codec2 base-devel libsndfile git wget rtl-sdr
wget https://mirror.archstrike.org/x86_64/archstrike/itpp-4.3.1-3-x86_64.pkg.tar.xz
sudo pacman -U itpp-4.3.1-3-x86_64.pkg.tar.xz
```

## Headless Ubuntu Server/Pi

If running headless, swap out pavucontrol for pulsemixer, and also install pulseaudio as well. Attempting to install pavucontrol in a headless environment may attempt to install a minimal desktop environment. Note: Default behavior of pulseaudio in a headless environment may be to be muted, so check by opening pulsemixer and unmuting and routing audio appropriately.

```
sudo apt install libpulse-dev libsndfile1-dev libfftw3-dev liblapack-dev socat libusb-1.0-0-dev libncurses5 libncurses5-dev rtl-sdr librtlsdr-dev libusb-1.0-0-dev cmake git wget make build-essential libitpp-dev libncursesw5-dev pulsemixer pulseaudio libcodec2-dev
```

### Build and Install ITPP - ONLY IF NOT IN REPO!!

```
wget -O itpp-latest.tar.bz2 http://sourceforge.net/projects/itpp/files/latest/download?source=files
tar xjf itpp*
#if you can't cd into this folder, double check folder name first
cd itpp-4.3.1
mkdir build
cd build
cmake ..
make -j `nproc`
sudo make install
sudo ldconfig
cd ..
cd ..
```

MBELib is considered a requirement in this build. You must read this notice prior to continuing. [MBElib Patent Notice](https://github.com/lwvmobile/mbelib#readme "MBElib Patent Notice") 

```
git clone https://github.com/lwvmobile/mbelib
git checkout ambe_tones
cd mbelib
mkdir build
cd build
cmake ..
make -j `nproc`
sudo make install
sudo ldconfig
cd ..
cd ..
```

Note: Some environment/distro users (Cygwin, Arch, and others) may need to manually copy any libmbe files found in the build folder to the /usr/lib folder on root.

```
cp libmbe* /usr/lib
cp cygmbe* /usr/lib
cp cygmbe* /bin
```

or

```
sudo cp libmbe* /usr/lib
sudo cp cygmbe* /usr/lib
sudo cp cygmbe* /bin
```

Finish by running these steps to clone and build DSD-FME.

```
git clone https://github.com/lwvmobile/dsd-fme
cd dsd-fme
mkdir build
cd build
cmake ..
make -j `nproc`
sudo make install
sudo ldconfig

```

### Windows Cygwin Builds

If using RTL input support, you must install libusb-win32 before proceeding with Cygwin installation. You will also need to build and compile a version of librtlsdr, but this can be tricky, and personally, I had to resort to using a much older version of the source code to get it to build at all with the rtl support in the precompiled 'Aero' builds.

Then install all dependencies available in the repo prior to building, either directly selecting them during setup with the setup-x86.exe or setup-x64.exe or by issuing a command similar to this:

```
setup-x64.exe --packages libpulse-devel,libpulse-mainloop-glib0,libpulse-simple0,libpulse0,pulseaudio,pulseaudio-debuginfo,pulseaudio-equalizer,pulseaudio-module-x11,pulseaudio-module-zeroconf,pulseaudio-utils,sox-fmt-pulseaudio,xfce-pulseaudio-plugin,libusb0,libusb1.0,libusb1.0-debuginfo,libusb1.0-devel,libncurses++w10,libncurses-devel,libncursesw10,ncurses,cmake,gcc-core,gcc-debuginfo,gcc-objc,git,make,socat,sox,sox-fmt-ao,unzip,wget,gcc-g++,libsndfile-devel
```

Then manually install ITPP and MBElib, see above install notes. Be sure to run `cp libmbe* /usr/lib` after compiling MBElib.

Then you can build and install using

```
git clone https://github.com/lwvmobile/dsd-fme
cd dsd-fme
mkdir build
cd build
# -DAERO=ON is optional, but recommended if you want the older OSS support as well
cmake -DAERO=ON ..
make
make install
```

### Virtual Sinks

You may wish to direct sound into DSD-FME via Virtual Sinks. You may set up a Virtual Sink or two on your machine for routing audio in and out of applications to other applications using the following command, and opening up pavucontrol "PulseAudio Volume Control" in the menu (or `pulsemixer` in headless mode) to change line out of application to virtual sink, and line in of DSD-FME to monitor of virtual sink. This command will not persist past a reboot, so you will need to invoke them each time you reboot, or search for how to add this to your conf files for persistency if desired. Note: This setup is completely optional if using TCP Network Sink Audio and/or RTL Input.

```
pactl load-module module-null-sink sink_name=virtual_sink  sink_properties=device.description=Virtual_Sink
pactl load-module module-null-sink sink_name=virtual_sink2  sink_properties=device.description=Virtual_Sink2
```

Already have this branch, and just want to pull the latest build? You can run the rebuild.sh file in the dsd-fme folder, or manually do the pull with the commands:

```
##Open your clone folder##
git pull
##cd into your build folder##
cd build
cmake ..
make -j `nproc`
sudo make install
sudo ldconfig
```

### Applying Patches -- Recommended For Advanced Users Only

More experimental or potentially functionality changing or temperamental features may be included as seperate .patch files inside of the patch folder, and are not included in the source code by default. 
If users wish to try or use these patches, then they can apply patches and build/rebuild with the following procedure. Make sure you have changed to the dsd-fme directory first, either by the top
command, or by otherwise opening the terminal to the clone folder.

```
cd dsd-fme
git apply patch/patchname.patch
cd build
make
sudo make install
```

If you later wish to update to the latest version via `git pull`, you may first need to reset any patches, perform the `git pull`, and then reapply patches.
A procedure for this is similar to, but not limited to:

```
cd dsd-fme
git reset --hard
git pull
git apply patch/xyz.patch
cd build
make
sudo make install
```

NOTE: Patches can potential break over time with further changes to code, requiring merge resolution or other manual intervention to re-apply patches.