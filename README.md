##  Please view description of the [original repo](https://github.com/andryblack/fontbuilder) for software details.

**This branch is intended to implement [this closed pull request](https://github.com/andryblack/fontbuilder/pull/14/files)**

**What the fork?**

While looking for precompiled Windows binaries of fontbuilder, the best I could find was [this Google Code page](https://code.google.com/archive/p/fontbuilder/downloads) with builds from **over a decade ago**. The download link on the original repository leads to a 404, and there are no Releases available on GitHub.

The entire purpose of this fork is to host more up-to-date Windows binaries in the event that someone, like me, is frantically searching through the forks looking for a download just to see if the program even works (it does).

[Download is available on the Releases page.](https://github.com/Toxijuice/fontbuilder/releases)

**How up to date is this version?**

While technically this was built from a clone of [this commit](https://github.com/andryblack/fontbuilder/commit/6a5927a228661c4503780e9712ba7427abf9af48), the only thing added at that time was a donation link. As far as I can tell, at the time of writing this, the most recent change that affects how the software works is from **2018**, with a [commit fixing kerning exports](https://github.com/andryblack/fontbuilder/commit/d42224deef8f91e8885952751271a19fa952dfcf) (which notably doesn't seem to work for me, but YMMV.)

As far as version numbering goes, the version number is still set to 0.6a, same as the build from 2011, despite the fact that there [have been many changes since](https://github.com/andryblack/fontbuilder/commits/master).

**How do I build this myself?**

Listen, I *really* don't know what I'm doing. I don't have a strong grasp on C++. I might be able to change a variable or two without breaking anything, but if I were to try to write anything significant, I'm pretty sure something would explode. So while the main repository gives the very detailed instructions of: `qmake && make`, that means **absolutely nothing** to me.

So in the event that you, dear reader, come across this fork in the distant future and download links have once again been broken, I will give some guidance on how I stumbled my way through building.

I was unable to get any progress on building this using Windows-based software, so I ended up using the **MinGW-w64 toolchain** on a [Manjaro](https://manjaro.org/download/) VM, by following [these wonderful instructions on StackOverflow](https://stackoverflow.com/questions/10934683/how-do-i-configure-qt-for-cross-compilation-from-linux-to-windows-target/48467983#48467983). Some unmentioned packages ended up being required for me to follow that guide:

    sudo pacman -Syu make
    sudo pacman -Syu patch
    sudo pacman -Syu automake

I believe at least one of these was [required to install yay](https://github.com/Jguer/yay#binary), and may not be required for building the toolchain. Beyond that, I ran into a snag where I was unable to build freetype2, causing the whole process to fail. I was able to circumvent this by downloading the binaries it was complaining about from [this unofficial repository](https://martchus.no-ip.biz/repo/arch/ownstuff/) via pacman. You may even be able to skip most of if not all of the toolchain build by installing from there instead.

I did not try the mentioned [Docker Image](https://hub.docker.com/r/burningdaylight/mingw-arch), which I imagine is even simpler to get working.

Once MinGW was ready to go, I cloned fontbuilder to a folder I had proper permissions in, and I downloaded the [freetype2 source](https://download.savannah.gnu.org/releases/freetype/). I specifically used 2.13.0, but I imagine both older and newer versions will work as well. More importantly, I had to extract the **include** folder from inside the source archive ***next to*** the fontbuilder folder. I was unable to get it to build without it specifically in that configuration, like this:

    parent_folder/
    ├─ include/
    │  ├─ ft2build.h
    │  ├─ (other stuff)
    ├─ fontbuilder/
    │  ├─ FontBuilder.Pro
    │  ├─ (other stuff)

Then, **finally**, with a terminal whose working directory is set to the fontbuilder:

If you want a 32-bit Windows build, type:

    i686-w64-mingw32-qmake-qt5

If you want a 64-bit Windows build, type:

    x86_64-w64-mingw32-qmake-qt5
Followed by:

    make

Then, once done, you should have an exe in the bin folder. However, **you still need to grab the proper dlls to make it work**. They will be included in subdirectories of `/usr/i686-w64-mingw32-qmake-qt5/` or `/usr/x86_64-w64-mingw32-qmake-qt5/` depending on what your build target was.

I could only get it to work if I copied all the .dll files in `/usr/(target)-w64-mingw32/bin/` next to `FontBuilder.exe`, and then copied the folder `platforms/` from `/usr/(target)-w64-mingw32/lib/qt/plugins/` into the same place.

In the end, it should look something like: 

    fontbuilder/
    ├─ platforms/
    │  ├─ qwindows.dll
    │  ├─ qxxxx.dll
    ├─ FontBuilder.exe
    ├─ libxxxx.dll
    ├─ Qt5xxxx.dll
    ├─ zlib1.dll
*(For the sake of clarity, there will be a lot more .dll files than shown here.)*

It's likely possible that you can remove some of the .dlls and still have it work, but I wasn't having any luck doing so.

In any case, that's how I was able to get it built and working. There might be a much simpler solution out there, but my lack of know-how prevented me from finding it. Hopefully this build guide won't be needed, though!
