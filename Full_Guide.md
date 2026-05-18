# Complete RetroPie Setup Guide

## Table of Contents
1. [Install Raspi-Imager](#install-raspi-imager)
2. [Image Micro-SD Card](#image-micro-sd-card)
3. [Setup RetroPie](#setup-retropie)
4. [Setup Samba Network Share](#setup-samba-network-share)
5. [Install McAirpos](#install-mcairpos)
6. [Install Games](#install-games)
7. [Optional Configuration](#optional-configuration)

---

## Install Raspi-Imager

> [!WARNING] ChromeOS (Chromebook) NOT COMPATIBLE!

[Download the app here (select the correct operating system; example: macOS, Windows, Linux, etc.)](https://www.raspberrypi.com/software/)

---

## Image Micro-SD Card

1. Launch the installed app and follow any additional instructions.
2. Select the Raspberry Pi model.
3. Choose the OS (Operating System).
   > [!NOTE] How to select RetroPie:
   > 1. Scroll down to "Emulation and Game OS"
   > 2. Select "RetroPie"
   > 3. Choose your device model if more than one appears

4. Select the target storage drive you are willing to sacrifice(**ALL DATA WILL BE ERASED!**).
   > [!CAUTION] **DO NOT SELECT THE SYSTEM DRIVE!!!**

5. If prompted, do not enable SSH; we will configure it later.
6. Press Start.
7. Wait for the process to finish; **DO NOT UNPLUG THE TARGET DRIVE**.
8. Press Complete/Finish.
9. Unplug the drive.

> [!IMPORTANT] Required Equipment:
> - Power cable
> - Keyboard *(for setup and Godot only)*
> - HDMI cable
> - At least one USB controller (if hardwired buttons/joysticks are not connected)
> - Micro-SD card
> - Mouse not required

10. Insert the Micro-SD card into the Raspberry Pi with all other required cables/devices.
11. Restart the Raspberry Pi.
    > [!TIP] How to power cycle the Raspberry Pi:
    > Unplug the power cable, wait 10 seconds, then plug the power cable back in.

12. Wait until the system completes its auto-configuration and shows the controller configuration screen, then proceed to the next part of this guide.

---

## Setup RetroPie

### Configure Controller

1. When the controller configuration screen appears, press any button to start.
2. Follow the on-screen prompts to map your controller buttons:
   - Press the button/key shown on screen.
   - Complete all button mappings (D-Pad, A/B/X/Y, L/R triggers, Start, Select, Hotkey [Start + Select], etc.).
   - If a button is not available, hold any button for 3 seconds to skip.
3. When finished, the controller configuration is complete.
4. Press OK to return to the main RetroPie menu.

### Configure Network

> [!NOTE] Use arrow keys and/or the D-Pad to navigate.

1. From the RetroPie main menu, select `Raspi-Config` then `Network Options` or `Wifi`.
2. Set your home country if prompted.
3. Choose your connection type:
   - **WiFi**: Select the network and enter the password; If it is a guest network just press enter, no password needed.
   - **Ethernet**: Connect the network cable; configuration should happen automatically.
4. (Optional) Rename the device hostname in `Network Options`.
   - Recommended format: `retropie-<your_classroom>`
   - Use lowercase letters only.
5. Note your IP address (displayed in Raspi-Config or by running `ifconfig` in a terminal).
6. Exit Raspi-Config and return to the main menu.

> [!NOTE] Terminal access requires a keyboard.
> To access the terminal:
> 1. Go to the main menu.
> 2. Press `Start` or `Select` to open the menu.
> 3. Choose `Quit` and then `Quit EmulationStation`.
> 4. Press Enter when the terminal appears.

### Set New Password

#### Option 1
1. From the RetroPie main menu, open `Raspi-Config`.
2. Select the password reset option.
3. Follow the prompts to set a new password.
   > [!TIP] Unless you will remember the password and keep it on the machine, use `Soaring1234!`

#### Option 2
1. Open a terminal on the Raspberry Pi or SSH in from your computer.
2. Run:
   ```bash
   passwd
   ```
3. Follow the prompts to enter the current password and a new strong password.
    > [!TIP] Unless you will remember the password and keep it on the machine, use `Soaring1234!`
4. Optionally set a Samba password for the `pi` account after enabling Samba:
   ```bash
   sudo smbpasswd -a pi
   ```

### Setup Samba Network Share

1. Enable Samba (SMB) for network file sharing:
   1. From EmulationStation, press `Start` or `Select` → `Quit` → `Quit EmulationStation`.
   2. Change to the RetroPie setup directory:
      ```bash
      cd RetroPie-Setup
      sudo ./retropie_setup.sh
      ```
   3. In the RetroPie-Setup menu, go to `Configuration / Tools` → `samba` → `Install` or `Enable`.

2. Configure Samba sharing:
   1. Edit the Samba configuration:
      ```bash
      sudo nano /etc/samba/smb.conf
      ```
   2. Add a share entry at the end of the file:
      ```ini
      [retropie]
      path = /home/pi/RetroPie
      read only = no
      guest ok = no
      create mask = 0775
      directory mask = 0775
      valid users = pi
      ```
   3. Save and exit (Ctrl+O, Enter, Ctrl+X).
   4. Set or update the Samba password for `pi`:
      ```bash
      sudo smbpasswd -a pi
      sudo systemctl restart smbd
      ```

3. Access the share from your computer:
   - Username: `pi`
   - Password: the password you set with `passwd` / `smbpasswd`.
   - **macOS**: Finder → Go → Connect to Server → `smb://<YOUR_RASPI_IP>/retropie`.
   - **Windows**: File Explorer → `\\<YOUR_RASPI_IP>\retropie`.
   - **Linux**: Use your file manager's network browser or `smbclient`.

4. ROM files should be copied to:
   ```text
   /home/pi/RetroPie/roms/<SYSTEM>/
   ```

### Install McAirpos

McAirpos is an application for managing and emulating MakeCode Arcade games on RetroPie.

1. Download and install McAirpos:
   ```bash
   wget https://raw.githubusercontent.com/Vegz78/McAirpos/master/install.sh
   bash install.sh
   rm install.sh
   ```

2. Configure McAirpos:
   > [!WARNING]
   > Do not change McAirpos settings unless you understand the configuration.

3. Update McAirpos:
   > [!WARNING]
   > This may remove games from the MakeCode emulator. Back up your ROMs first.
   ```bash
   rm -rf <the_directory_with_makecode_or_mcairpos>
   wget https://raw.githubusercontent.com/Vegz78/McAirpos/master/install.sh
   bash install.sh
   rm install.sh
   ```

---

## Install Games

### Adding ROM Files

1. **Via Network Share (recommended)**:
   - Connect to your Samba share from your computer.
   - Use the same credentials you set for the `pi` account.
   - Copy ROM files into the appropriate folder: `retropie/roms/<SYSTEM>/`.
   - Games should appear in EmulationStation within a few seconds.

2. **Via USB Drive**:
   - Create a folder named `retropie` on the USB drive.
   - Inside it, create `retropie/roms/<SYSTEM>/`.
   - Copy your ROM files into the correct system folder.
   - Plug the drive into the Raspberry Pi.
   - From the RetroPie menu, select `Tools` → `USB-ROM Service`.
   - Choose the USB drive and wait for the import to complete.

3. **Via SSH**:
   > [!NOTE] This method is advanced and recommended for experienced users.
   ```bash
   ssh pi@<YOUR_RASPI_IP>
   scp /path/to/roms/* pi@<YOUR_RASPI_IP>:/home/pi/RetroPie/roms/<SYSTEM>/
   ```

### Supported Systems & File Types
- **NES**: .nes, .zip
- **SNES**: .smc, .sfc, .zip
- **Genesis**: .md, .bin, .zip
- **Arcade**: .zip (MAME format)
- **PlayStation 1**: .iso, .cue/.bin, .zip
- **Game Boy**: .gb, .zip
- **Game Boy Color**: .gbc, .zip
- **Game Boy Advance**: .gba, .zip
- **Godot**: .zip, .pck
- **MakeCode Arcade**: .elf, .uf2 (convert to .elf if required)

---

## Optional Configuration

### Change Wallpapers and Splash Screens
- From EmulationStation, go to **Main Menu** → **UI Settings**.
- Download and select themes, splash screens, or wallpapers.
- Apply the settings you want.

### Setup SSH

1. On the Raspberry Pi:
   - Go to **RetroPie Menu** → **Raspi-Config**.
   - Select **Interface Options**.
   - Enable **SSH**.
   - Exit and return to the main menu.

2. From your computer:
   ```bash
   ssh pi@<YOUR_RASPI_IP>
   # Default password: raspberry
   # Recommended: change the password with passwd
   ```

### Install GODOT Emulator

1. Follow the RetroPie Godot emulator installation guide:
   - [RetroPie-Godot-Engine-Emulator](https://github.com/hiulit/RetroPie-Godot-Engine-Emulator)

2. Add Godot games:
   - Place `.zip` or `.pck` files in `/home/pi/RetroPie/roms/godot/`.
   - Games should appear in EmulationStation.

### Edit Menu

1. SSH into your Raspberry Pi:
   ```bash
   nano /home/pi/.emulationstation/es_systems.cfg
   ```
2. Edit system names, descriptions, or add/remove systems.
3. Save (Ctrl+O, Enter, Ctrl+X).
4. Restart EmulationStation:
   ```bash
   killall emulationstation
   ```

### Advanced Tweaks

**Overclock Settings (performance boost):**
1. Go to **RetroPie Menu** → **Raspi-Config**.
2. Select **Performance Options**.
3. Choose an overclock profile if supported by your model.
4. Restart when prompted.

**Configure Controller Remapping:**
1. From the RetroPie menu, open **Configuration Editor**.
2. Select **Configure Controller Mappings**.
3. Choose the emulator you want to configure.
4. Follow the prompts to remap buttons.

---

## Troubleshooting

**No games appearing:**
- Ensure ROM files are in the correct `/home/pi/RetroPie/roms/<SYSTEM>/` folder.
- Check that file extensions are supported.
- Restart EmulationStation: Press `Start` → `Quit`.

**Network share not accessible:**
- Verify the Raspberry Pi and your computer are on the same network.
- Check the IP address.
- Restart Samba in a terminal:
  ```bash
  sudo systemctl restart smbd
  ```
- If needed, reinstall Samba via the RetroPie setup menu.

---

**Last Updated:** May 15, 2026
**Created By:** Nolan F. Nelson, 7th Grade
**Email:** N/A; create an issue instead.

