<h2 align="center"> SDDM SEL</h2>
<p align=center>
(forked from <a href="https://github.com/leonardochappuis/sddmsel">leonardochappuis</a> and updated to qt6)<br>
A SEL inspired theme for the <a href="https://github.com/sddm/sddm">SDDM Login Manager</a>
</p>
<div align="center">
<h2>Preview</h2>

 <img src="https://raw.githubusercontent.com/reaperwatch/sel-sddm-fedora/refs/heads/main/sel-shaders-fixed/preview.png" alt="Theme Preview" width="600"/>
</div>

## Install

### From sources

> _Assumes that you've installed and configured SDDM correctly_ (if not [read more](https://wiki.archlinux.org/title/SDDM))

> _For Kde plasma please use_ [sddm-kcm](https://github.com/KDE/sddm-kcm)

> _if your kde system (fedora install) overwrites sddm on update run:_

1. install dnf locker

```bash
sudo dnf install 'dnf-command(versionlock)'
```

2. lock sddm

```bash
sudo dnf versionlock add sddm sddm-kcm sddm-wayland-plasma
```

3. enable sddm and disable plasma login (system may still overrite this on update)

```bash
sudo systemctl disable plasmalogin
sudo systemctl enable --force sddm
```

> Please make sure you have the following dependencies installed:

> - **`qt6-qtmultimedia`**: Powers the background video playback and theme boot audio.
> - **`qt6-qt5compat`**: Provides the backward-compatibility layer required to render legacy graphical layouts without performance loss.
> - **`qt6-qtsvg`**: Allows SDDM to properly render vector icons and custom interface shapes.
> - **`gstreamer1-plugins-good` & `gstreamer1-libav`**: Essential system-level multimedia codecs required to decode and play back the live background video container.

- Fedora install command:

```bash
sudo dnf install qt6-qtmultimedia qt6-qtsvg qt6-qt5compat gstreamer1-plugins-good gstreamer1-libav
````

1. Open terminal, and clone the repository with:

   ```sh
   git clone https://github.com/reaperwatch/sel-sddm-fedora.git ~/sddm-sel
   ```

2. Test versions:

   ```sh
   sddm-greeter-qt6 --test-mode --theme ~/sddm-sel/sel-shaders-fixed
   ```

   ```sh
   sddm-greeter-qt6 --test-mode --theme ~/sddm-sel/sel-basic-fixed
   ```

3. Them move the wanted version as follows:

With Shaders

```sh
sudo mv ~/sddm-sel/sel-shaders-fixed /usr/share/sddm/themes/
```

Without Shaders

```sh
sudo mv ~/sddm-sel/sel-basic-fixed /usr/share/sddm/themes/
```

4. Remove the folder if you'd like

```sh
rm -rf ~/sddm-sel
```

## 🔊 Audio Troubleshooting

If the background audio does not play on the login screen, SDDM's user session is likely failing to connect to PipeWire, or it is routing audio to the wrong output hardware. Follow these steps to fix it:

### 1. Enable SDDM Session Lingering

SDDM needs a persistent user session to keep its audio server connection alive during the boot sequence.

First, check if SDDM can communicate with PipeWire:

```bash
sudo -u sddm pw-cli info 0
```

If that command returns an error or shows nothing, force the system to keep the SDDM user session alive by running:

```bash
sudo loginctl enable-linger sddm
```

### 2. Verify and Set the Default Audio Sink

​If the session is alive but you still hear nothing, SDDM might be trying to play audio through a disconnected channel (like an unused HDMI port instead of your speakers).
​Check SDDM's current default audio output target:

```bash
sudo -u sddm pactl info
```

If the default sink listed isn't your primary audio device, list all available audio hardware names on your system:

```bash
sudo -u sddm pactl list short sinks
```

Find your active device name in that list (e.g., alsa_output.pci-0000_00_1f.3.analog-stereo), and set it manually as SDDM's default target:

```bash
sudo -u sddm pactl set-default-sink YOUR_SPEAKER_SINK_NAME_HERE
```

(Replace YOUR_SPEAKER_SINK_NAME_HERE with the exact device name found in the previous step).

## Configure

Edit the `/etc/sddm.conf` (with any text editor with **raised** privileges), so that it looks like this:

```sh
sudo nano /etc/sddm.conf  # use any text editor with raised privileges
---
[Theme]
Current=sddm-sel
```

# FAQ

1. Sometimes SDDM might play no audio on the login screen if pipewire is initialized after it

Here's a possible solution.
Change the `sddm.service` file:

```bash
sudo systemctl edit sddm.service
```

In the editor that opens, paste the following lines:

```
[Unit]
After=pipewire.service pipewire.socket
Wants=pipewire.service pipewire.socket
```

Save and close the file.

Then run this command to apply the changes:

```bash
sudo systemctl daemon-reexec
```

(credits to [`Epixardo`](https://github.com/Epixardo) for reporting this one)

## Credits

- Original Video by [`Skorsen`](https://www.youtube.com/watch?v=zMLNTgomRNk)
- Based on the theme [`Tokyo Night SDDM`](https://github.com/rototrash/tokyo-night-sddm.git) which in turn
  is:
- Based on the theme [`Sugar Dark for SDDM`](https://github.com/MarianArlt/sddm-sugar-dark) by [**MarianArlt**](https://github.com/MarianArlt).

## License

[GNU Lesser General Public License v3.0](LICENSE)

```

```
