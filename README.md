# Notice:
I will not be continuing tihs project for the foreseeable future.  I have entirely changed to various distributions of Linux for all of my systems.  Should there be a time in the future where I am required to use Windows, I would likely resume it at that time in the future.


# automate-wsl-distro-deployment
Eventually seeks to automate installation of WSL2 for xrdp connectivity to Xubuntu hosted on Windows 10 along with USB passthrough via usbipd as well as additional requisite configurations in Windows and the distro.


# Present manual process in Windows 10 Pro:


Install and access `Ubuntu` CLI:


1. Open admin-level CMD or PS.
2. Install `wsl`: `wsl --install`
3. Update `wsl`: `wsl --update`
4. Install `Ubuntu`: `wsl --install -d Ubuntu`
   - If `An error occurred during installation. Distribution Name: 'Ubuntu' Error Code: 0x80072eff` is encountered, `Win + R > wsreset.exe` and re-run installation command; additional troubleshooting for [0x80072eff](https://docs.microsoft.com/en-us/answers/questions/116772/error-code-0x80072eff-on-windows-updates.html)
5. Access `Ubuntu`: `wsl --distributionName Ubuntu --user userName`


Install and configure `xfce` and `xrdp`:


1. Remove any traces of `xrdp`: `sudo apt-get purge xrdp`
2. Install `xrdp`: `sudo apt-get install xrdp`
3. Install `xfce`: `sudo apt-get install xfce4`
4. Install additional `xfce` packages: `sudo apt-get install xfce4-goodies`


Configure `xrdp` on `xcfe`:


1. `sudo cp /etc/xrdp/xrdp.ini /etc/xrdp/xrdp.ini.bak`
2. Change `xrdp` port to allow concurrent RDP and shell access: `sudo sed -i 's/3389/3390/g' /etc/xrdp/xrdp.ini`
3. `sudo sed -i 's/max_bpp=32/#max_bpp=32\nmax_bpp=128/g' /etc/xrdp/xrdp.ini`
4. `sudo sed -i 's/xserverbpp=24/#xserverbpp=24\nxserverbpp=128/g' /etc/xrdp/xrdp.ini`
5. `echo xfce4-session > ~/.xsession`
6. Modify `/etc/xrdp/startwm.sh`
   - to comment-out
     - `#test -x /etc/X11/Xsession && exec /etc/X11/Xsession`
     - `#exec /bin/sh /etc/X11/Xsession`
   - and append these lines to the end of the file:
     - `# xfce`
     - `startxfce4`
   1. `sudo sed -i 's:test -x /etc/X11/Xsession \&\& exec /etc/X11/Xsession:#test -x /etc/X11/Xsession \&\& exec /etc/X11/Xsession:g' /etc/xrdp/startwm.sh`
   2. `sudo sed -i 's:exec /bin/sh /etc/X11/Xsession:#exec /bin/sh /etc/X11/Xsession\n# xfce\nstartxfce4:g' /etc/xrdp/startwm.sh`


Start the `xrdp` service:


1. `sudo /etc/init.d/xrdp start`


Connect to `Ubuntu` using RDP:


1. In Windows, `Win + R > %windir%\system32\mstsc.exe` to open "Remote Desktop Connection"
2. Set `Computer` to `localhost:3390`
3. Click on `Connect`
4. Set `Session` to `Xorg`
5. Populate `username` and `password`
6. Click on `OK`
7. Verify Xubuntu desktop.


Backup the distribution:


1. In the Window OS admin-level CLI, execute `wsl --export <distro> <filename.tar>`
   - e.g., `wsl --export Ubuntu C:\projects\Ubuntu-wsl2-usbip.tar`


To restore this backup of the distribution:


1. In the Windows OS admin-level CLI, execute `wsl --unregister <distro>` to remove the problem distribution.
   - e.g., `wsl --unregister wsl2-usbip`
2. In the Windows OS admin-level CLI, execute `wsl --import <distro> <install location> <filename>`
   - e.g., `wsl --import wsl2-usbip C:\projects\wsl-dist C:\projects\Ubuntu-wsl2-usbip.tar`


---


# Current task:


Start WSL2 when Windows starts


1. [How to Run WSL2 at Startup on Windows](https://medium.com/swlh/how-to-run-ubuntu-in-wsl2-at-startup-on-windows-10-c4567d6c48f1)


---


# To do:


1. implement installation of `wsl` and `Windows Terminal` using `Chocolatey`
2. implement automated configuration of this entire process using Ansible
   - Windows
   - Chocolatey (community? module)
   - Linux


# Wishlist:


- resolve [usbipd](https://github.com/dorssel/usbipd-win) passthrough issue with Windows 10
  - include in automation if resolved


---


# Acknowledgements and references


- WSL reference: [Install WSL](https://docs.microsoft.com/en-us/windows/wsl/install)
- The post by [Muhammad Ridwan Hakin • Apr 30 '20 • Edited on Jun 6 in "Linux on Windows: WSL with Desktop Environment via RDP"](https://dev.to/darksmile92/linux-on-windows-wsl-with-desktop-environment-via-rdp-522g) was extremely helpful to configure `Xubuntu` for `xrdp`.
