# Maya Installation on CentOS 8

## Overview
The default installation instructions for Maya 2019 on a fresh install of CentOS 8 do not work as outlined.
Although the installation docs note that installations are hardware-specific, the instructions are generally unclear.
This document shows the steps I took to install Maya as guided by the docs, as well as my recommendations for making the installation process less difficult.
To install and troubleshoot Maya, I followed three documents: [1](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2019/ENU/Installation-Maya/files/GUID-E7E054E1-0E32-4B3C-88F9-BF820EB45BE5-htm.html) [2](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2019/ENU/Installation-Maya/files/GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F-htm.html#GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F__GUID-39778FF7-0722-4992-A8F8-9F1F7C6FE968) [3](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2020/ENU/Installation-Maya/files/GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F-htm.html).


## Summary of Findings

### Missing Dependencies not Mentioned
The documents do mention that additional dependencies are required, but none of them mention the following, both of which were needed to run Maya successfully:
- `libprcre16`
- `libpng15`

### Malformed GNOME Desktop File
The desktop file that allows Maya to be launched from a `GNOME` desktop icon has a typo that says `UFT-8` instead of `UTF-8`.
After correcting all other issues, this was found not to be a problem, but it should be fixed anyway.

### 2019 Installation instructions have 2018 information that prevents registration, even with a valid license. 
While following the official steps to register my Maya license with ADLM, I got an opaque `code 13` error and no contextual information.

The actual issue appears to be that the command used for registration was copy-pasted from the 2018 guide and not updated accordingly. See step 12 for more details.

## Recommendations
1. Make note of the required `libpcre16` and `libpng15` packages in the [Additional Linux Notes](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2019/ENU/Installation-Maya/files/GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F-htm.html#GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F__GUID-39778FF7-0722-4992-A8F8-9F1F7C6FE968) and [Additional required Linux Packages for Maya installation](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2019/ENU/Installation-Maya/files/GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F-htm.html#GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F__GUID-39778FF7-0722-4992-A8F8-9F1F7C6FE968) documents.
2. Fix step 8 in the [Install Maya on Linux using the RPM Utility](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2019/ENU/Installation-Maya/files/GUID-E7E054E1-0E32-4B3C-88F9-BF820EB45BE5-htm.html) document.
The correct instructions for standalone licensing should be:
```bash
/usr/autodesk/maya2019/bin/adlmreg -i S <productKey1> <productKey2> 2019.0.0.F <serialNum>
/var/opt/Autodesk/Adlm/Maya2019/MayaConfig.pit
```
3. Fix the `UFT-8` typo in the GNOME desktop file located at `/usr/autodesk/maya2019/desktop/Autodesk-Maya.directory`.
The corrected file contents should be:
```
[Desktop Entry]
Name=Autodesk Maya 2019
Encoding=UTF-8
Icon=Maya.png
```

4. Make `adlm` errors less opaque. It is very difficult to troubleshoot an error that says `Registration failed with code: 13` with no contextual information whatsoever.


## Maya Installation
Steps to reproduce the issues:
1. Download official `Linux64` Maya installer from Autodesk.com.
```bash
[brenden@errmac Downloads]$ sha256sum Autodesk_Maya_2019_Linux_64bit.tgz 
6eacc6e80b4a7bec21eb5e6aa693c0b33ebad50187c5805107b3bd248c569c1c  Autodesk_Maya_2019_Linux_64bit.tgz
```
2. Pull up RPM installation instructions from [official documentation](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2019/ENU/Installation-Maya/files/GUID-E7E054E1-0E32-4B3C-88F9-BF820EB45BE5-htm.html).

3. Become the root user using.
```bash
su -
``` 

4. Extract the `Autodesk_Maya_2019_Linux_64bit.tgz` file.
```bash
tar xvf Autodesk_Maya_2019_Linux_64bit.tgz
```

5. Ensure that the required files are present.
```bash
[root@errmac Downloads]# ls -1
adlmapps14-14.0.23-0.x86_64.rpm
adlmflexnetclient-14.0.23-0.x86_64.rpm
adlmflexnetserver-14.0.23-0.x86_64.rpm
adlmreg
Autodesk_Maya_2019_Linux_64bit.tgz
bifrost.rpm
EULA
libadlmPIT.so
libadlmPIT.so.11
libadlmutil.so
libadlmutil.so.11
libQt3Support.so.4
libQtCore.so.4
libQtGui.so.4
libQtNetwork.so.4
libQtScript.so.4
libQtSql.so.4
libQtXml.so.4
licensechooser
Maya2019_64-2019.0-7966.x86_64.rpm
package.zip
resources
setup
setup-bin
setup.xml
SubstanceMaya-1.4.0-2019-Install.sh
support
unix_installer.py
unix_installer.sh
```

6. Use `rpm` to install the 3 required packages for standalone installation, and verify successful exit status.
```bash
[root@errmac Downloads]# rpm -ivh Maya2019_64-2019.0-7966.x86_64.rpm adlmapps14-14.0.23-0.x86_64.rpm adlmflexnetclient-14.0.23-0.x86_64.rpm 
Verifying...                          ################################# [100%]
Preparing...                          ################################# [100%]
Updating / installing...
   1:adlmflexnetclient-14.0.23-0      ################################# [ 33%]
   2:adlmapps14-14.0.23-0             ################################# [ 67%]
   3:Maya2019_64-2019.0-7966          ################################# [100%]
[root@errmac Downloads]# echo $?
0
``` 

7. Create a `license.env` file with the required contents.
```
MAYA_LICENSE=<redacted>
MAYA_LICENSE_METHOD=standalone
``` 

8. Search for `libGL.so` files in the directories specified in the instructions.
```bash
# searching /usr/lib has no results
[root@errmac Downloads]# find /usr/lib -name '*lib[Gg][Ll]*.so*' -type f 2>/dev/null
[root@errmac Downloads]#

# /usr/X11R6 isn't even a directory in CentOS 8
[root@errmac Downloads]# file /usr/X11R6/lib
/usr/X11R6/lib: cannot open `/usr/X11R6/lib' (No such file or directory)

# /usr/lib64 has some potential winners:
[root@errmac Downloads]# find /usr/lib64 -name '*lib[Gg][Ll]*.so*' -type f 2>/dev/null
/usr/lib64/xorg/modules/extensions/libglx.so
/usr/lib64/xorg/modules/libglamoregl.so
/usr/lib64/libGLdispatch.so.0.0.0
/usr/lib64/libglapi.so.0.0.0
/usr/lib64/libGLESv1_CM.so.1.2.0
/usr/lib64/libGLESv2.so.2.1.0
/usr/lib64/libGL.so.1.7.0
/usr/lib64/libGLX.so.0.0.0
/usr/lib64/libGLX_mesa.so.0.0.0
/usr/lib64/libglib-2.0.so.0.5600.4
/usr/lib64/libglusterfs.so.0.0.1
/usr/lib64/libglibmm-2.4.so.1.3.0
/usr/lib64/libglibmm_generate_extra_defs-2.4.so.1.3.0
/usr/lib64/libgladeui-2.so.6.5.1
/usr/lib64/glade/modules/libgladegtk.so
/usr/lib64/glade/modules/libgladepython.so
/usr/lib64/glade/modules/libgladewebkit2gtk.so
```

9. Run `glxinfo` to verify the OpenGL driver, vendor, and renderer.
**NOTE**:Step 6 in the [documentation](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2019/ENU/Installation-Maya/files/GUID-E7E054E1-0E32-4B3C-88F9-BF820EB45BE5-htm.html) specifically says that the expected driver is **not** Mesa, but my output shows Mesa, so I will likely need to install additional drivers as mentioned on Maya's [Additonal Linux Notes](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2019/ENU/Installation-Maya/files/GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F-htm.html#GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F__GUID-39778FF7-0722-4992-A8F8-9F1F7C6FE968).
```bash
[root@errmac Downloads]# glxinfo | egrep -i 'vendor|renderer|driver'
server glx vendor string: SGI
client glx vendor string: Mesa Project and SGI
    GLX_MESA_multithread_makecurrent, GLX_MESA_query_renderer, 
    GLX_EXT_visual_rating, GLX_MESA_copy_sub_buffer, GLX_MESA_query_renderer, 
Extended renderer info (GLX_MESA_query_renderer):
    Vendor: VMware, Inc. (0xffffffff)
OpenGL vendor string: VMware, Inc.
OpenGL renderer string: llvmpipe (LLVM 8.0, 256 bits)
```

10. Try to install every single dependency listed on Maya's [Additional required packages for Maya installation](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/CloudHelp/cloudhelp/2020/ENU/Installation-Maya/files/GUID-D2B5433C-E0D2-421B-9BD8-24FED217FD7F-htm.html) page.
```bash
[root@errmac Downloads]# yum install mesa-libGLw mesa-libGLU libXp gamin audiofile audiofile-devel e2fsprogs-libs libxkbcommon-x11 xorg-x11-fonts-ISO8859-1-100dpi xorg-x11-fonts-ISO8859-1-75dpi liberation-mono-fonts liberation-fonts-common liberation-sans-fonts liberation-serif-fonts 
```

Out of the above list, only two files were not already installed or available from the Yum default repos:
- `audiofile`
- `audiofile-devel`

Those two packages are available through the EPEL repository, so I installed `epel-release` and updated the `yum` cache to enable installing them:
```bash
[root@errmac Downloads]# yum install -y epel-release
...
[root@errmac Downloads]# yum makecache
CentOS-8 - AppStream                                                     7.4 kB/s | 4.3 kB     00:00    
CentOS-8 - Base                                                           21 kB/s | 3.9 kB     00:00    
CentOS-8 - Extras                                                        4.3 kB/s | 1.5 kB     00:00    
Extra Packages for Enterprise Linux Modular 8 - x86_64                   168 kB/s | 119 kB     00:00    
Extra Packages for Enterprise Linux 8 - x86_64                           5.8 MB/s | 7.0 MB     00:01    
Metadata cache created.
...
[root@errmac Downloads]# yum search audiofile
Last metadata expiration check: 0:00:50 ago on Tue 16 Jun 2020 01:11:46 PM PDT.
==================================== Name Exactly Matched: audiofile ====================================
audiofile.x86_64 : Library for accessing various audio file formats
======================================== Name Matched: audiofile ========================================
audiofile-devel.x86_64 : Development files for Audio File applications
[root@errmac Downloads]# yum install audiofile audiofile-devel
Last metadata expiration check: 0:00:59 ago on Tue 16 Jun 2020 01:11:46 PM PDT.
Dependencies resolved.
...
[root@errmac Downloads]# yum install -y audiofile audiofile-devel
```

11. Modify `LD_LIBRARY_PATH` to include an Autodesk ADLM directory with additional libraries.
```bash
[root@errmac Downloads]# export LD_LIBRARY_PATH=/opt/Autodesk/Adlm/R14/lib64/
```

12. Register Maya using Autodesk's ADLM using default instructions.
```bash
[root@errmac Downloads]# #/usr/autodesk/maya2019/bin/adlmreg -i S <key> <key> 2018.0.0.F <serial> /var/opt/Autodesk/Adlm/Maya2019/MayaConfig.pit
Registration failed with code: 13
```

This registration code failure confused me at first. Then I noticed that even though the doc says the command above should work, the string `2018.0.0.F` indicated to me that these were copy-pasta instructions from the 2018 installation instructions.

I changed the `2018` to `2019` in the command:
```bash
[root@errmac Downloads]# /usr/autodesk/maya2019/bin/adlmreg -i S <key> <key> 2019.0.0.F <serial> /var/opt/Autodesk/Adlm/Maya2019/MayaConfig.pit
Registration succeeded.
```

13. Attempt to run Maya by clicking the `GNOME` desktop icon.
- Nothing visible happened, no Maya window ever spawned.

14. Troubleshoot by looking at logs.
```bash
[root@errmac Downloads]# grep -ni maya /var/log/messages
3376:Jun 16 12:33:53 errmac journal[8794]: Couldn't properly parse desktop file 'file:///usr/share/desktop-directories/Autodesk-Maya2016.directory': 'Key file contains unsupported encoding “UFT-8�”'
3377:Jun 16 12:33:54 errmac journal[8789]: failed to rescan: Failed to parse /usr/share/applications/Autodesk-Maya2016.desktop file: cannot process file of type application/x-desktop
4670:Jun 16 13:29:21 errmac Autodesk-Maya2016.desktop[9661]: /usr/autodesk/maya/bin/maya.bin: error while loading shared libraries: libpcre16.so.0: cannot open shared object file: No such file or directory
4683:Jun 16 13:30:27 errmac Autodesk-Maya2016.desktop[9702]: /usr/autodesk/maya/bin/maya.bin: error while loading shared libraries: libpcre16.so.0: cannot open shared object file: No such file or directory
```

Interestingly there is a typo in the `GNOME` Desktop file for Maya that says `UFT-8` instead of `UTF-8`.
This may be preventing the program from launching, or it may be an innocuous error.
```bash
[root@errmac Downloads]# cat /usr/autodesk/maya2019/desktop/Autodesk-Maya.directory
[Desktop Entry]
Name=Autodesk Maya 2019
Encoding=UFT-8
Icon=Maya.png
```

As noted above, the Maya desktop launcher requires a library for Perl regular expressions, but none is installed, and none of the installation instructions (on **any** of the 3 pages) mention `libpcre16`.

15. Install `libpcre16`.
```bash
[root@errmac Downloads]# yum whatprovides "*/libpcre16.so.0"
pcre-utf16-8.42-4.el8.i686 : UTF-16 variant of PCRE
Repo        : BaseOS
Matched from:
Filename    : /usr/lib/libpcre16.so.0

pcre-utf16-8.42-4.el8.x86_64 : UTF-16 variant of PCRE
Repo        : BaseOS
Matched from:
Filename    : /usr/lib64/libpcre16.so.0
```

Of the two in the list, the `x86_64` version is the one we want:
```bash
[root@errmac Downloads]# yum install -y pcre-utf16-8.42-4.el8.x86_64
```

16. Attempt to run Maya by clicking the `GNOME` desktop icon.
- Nothing visible happened, no Maya window ever spawned.

17. Troubleshoot by looking at logs.
`/var/log/messages` shows that Maya is also dependent upon `libpng15` despite the documentation making no mention of it:
```bash
Jun 16 13:42:02 errmac Autodesk-Maya2016.desktop[9938]: /usr/autodesk/maya/bin/maya.bin: error while loading shared libraries: libpng15.so.15: cannot open shared object file: No such file or directory
```

18. Install `libpng15`.
```bash
[root@errmac Downloads]# yum whatprovides "*/libpng15.so.15"
Last metadata expiration check: 0:03:13 ago on Tue 16 Jun 2020 01:41:34 PM PDT.
libpng15-1.5.30-7.el8.i686 : Old version of libpng, needed to run old binaries
Repo        : AppStream
Matched from:
Filename    : /usr/lib/libpng15.so.15

libpng15-1.5.30-7.el8.x86_64 : Old version of libpng, needed to run old binaries
Repo        : AppStream
Matched from:
Filename    : /usr/lib64/libpng15.so.15
```

Again, we want the `x86_64` version:
```bash
[root@errmac Downloads]# yum install -y libpng15-1.5.30-7.el8.x86_64
```

19. Attempt to run Maya by clicking the `GNOME` desktop icon.
- Nothing visible happened. No window was spawned.

20. Troubleshoot by looking at logs:
`/var/log/messages` shows that Maya is also dedpendent upon `libssl` even though it is not mentioned in any documentation:
```bash
Jun 16 13:51:59 errmac Autodesk-Maya2016.desktop[10386]: /usr/autodesk/maya/bin/maya.bin: error while loading shared libraries: libssl.so.10: cannot open shared object file: No such file or directory
```

21. Install the "Compatibility version of the OpenSSL library".
Searching for the missing shared object file shows the package name:
```bash
[root@errmac Downloads]# yum whatprovides "*/libssl.so.10*"
Last metadata expiration check: 0:11:38 ago on Tue 16 Jun 2020 01:41:34 PM PDT.
compat-openssl10-1:1.0.2o-3.el8.i686 : Compatibility version of the OpenSSL library
Repo        : AppStream
Matched from:
Filename    : /usr/lib/libssl.so.10

compat-openssl10-1:1.0.2o-3.el8.x86_64 : Compatibility version of the OpenSSL library
Repo        : AppStream
Matched from:
Filename    : /usr/lib64/libssl.so.10
```

Once again, install the `x86_64 version`:
```bash
[root@errmac Downloads]# yum install -y compat-openssl10-1:1.0.2o-3.el8.x86_64
```

22. Attempt to run Maya by clicking the `GNOME` desktop icon.
- Success! An `Autodesk Licensing` window is spawned this time

23. Follow the `Autodesk Licensing` dialog
1. Click `I agree` to agree to the Terms of Service.
2. A pop-up appears that says it's verifying the license, presumable from `license.env` created earlier.

24. Maya finally opens and runs!
- Instantly there is a Color Management error that reads:
```
Failed to apply color management settings on file open: Failed to finalize the color transform..
```
![screenshot of Color Management error from Maya on first run](https://github.com/bxbrenden/Maya-Installation-Notes/blob/master/ColorManagementError.png)
There is a [troubleshooting KB article](https://knowledge.autodesk.com/support/maya/troubleshooting/caas/sfdcarticles/sfdcarticles/Failed-to-apply-color-management-settings-on-file-open.html) to fix this.


## CentOS 8 Operating System / Hardware Details
In order to reproduce this issue, I used a system with the following specs:
- brand new installation of CentOS 8 with no additional programs installed or configured
- `Software Selection` set to `Workstation` in the CentOS installer dialog box
- kernel: `4.18.0-147.el8.x86_64` 
- GPU: GeForce GTX 1070
```bash
[root@errmac Downloads]# lspci -vnn | grep VGA
02:00.0 VGA compatible controller [0300]: NVIDIA Corporation GP104 [GeForce GTX 1070] [10de:1b81] (rev a1) (prog-if 00 [VGA controller])
```

