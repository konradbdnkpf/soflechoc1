# ZMK Firmware Configuration

This repository contains ZMK firmware configuration for custom split keyboard builds.

## Supported Keyboards

- **Corne Choc Pro** - 42-key split keyboard with nice!view display
- **Piantor Pro BT** - 42-key split keyboard with nice!view display
- **Sofle Choc Pro BT** - 58-key split keyboard with encoders and nice!view display

## Building Firmware

Firmware is built automatically using GitHub Actions whenever you push changes.

### Automatic Build on Push

1. Make changes to your keymap or configuration files
2. Commit and push to GitHub:
   ```bash
   git add .
   git commit -m "Update keymap"
   git push
   ```
3. GitHub Actions will automatically start building the firmware
4. Go to the **Actions** tab in your GitHub repository
5. Wait for the build to complete (usually 2-5 minutes)
6. Download the firmware files from the **Artifacts** section

### Manual Workflow Trigger

You can also trigger a build manually without making changes:

1. Go to your repository on GitHub
2. Click the **Actions** tab
3. Select **Build ZMK firmware** workflow
4. Click **Run workflow** button
5. Wait for the build to complete
6. Download the artifacts containing your `.uf2` firmware files

## Flashing Firmware

### Entering Bootloader Mode

To flash new firmware, you need to put each keyboard half into bootloader mode:

1. **Double-tap the reset button** on the keyboard half
   - Or use the settings_reset shield firmware if you don't have physical access to reset button
2. The keyboard should appear as a USB mass storage device

### Flashing on Linux/Fedora

#### Using File Manager (GUI)

1. Put keyboard into bootloader mode
2. A drive named **NICENANO** (or similar) should appear in your file manager
3. Open the drive
4. Copy the appropriate `.uf2` file to the drive:
   - `sofle_choc_pro_left-nice_view_disp.uf2` for left half
   - `sofle_choc_pro_right-nice_view_disp.uf2` for right half
5. The keyboard will automatically reboot with the new firmware

#### Using Command Line

```bash
# Wait for the bootloader drive to mount
# It typically mounts at /run/media/$USER/NICENANO

# Flash left half
cp sofle_choc_pro_left-nice_view_disp.uf2 /run/media/$USER/NICENANO/

# Repeat for right half
# (Put right half in bootloader mode first)
cp sofle_choc_pro_right-nice_view_disp.uf2 /run/media/$USER/NICENANO/
```

### Troubleshooting

#### USB Device Not Appearing

If the bootloader drive doesn't appear, you may need to check USB permissions:

```bash
# Check if device is detected
lsusb | grep -i "Adafruit\|bootloader"

# Add udev rule if needed (create file as root)
sudo nano /etc/udev/rules.d/50-nicenano.rules
```

Add this line:
```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="239a", MODE:="0666"
```

Then reload udev rules:
```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

#### Drive Won't Mount

Try manually mounting:
```bash
# Find the device
lsblk

# Mount it (replace sdX1 with your device)
sudo mkdir -p /mnt/nicenano
sudo mount /dev/sdX1 /mnt/nicenano

# Copy firmware
sudo cp your-firmware.uf2 /mnt/nicenano/

# Unmount
sudo umount /mnt/nicenano
```

## Customization

### Editing Keymaps

Keymap files are located in `config/`:
- `config/corne_choc_pro.keymap`
- `config/piantor_pro_bt.keymap`
- `config/sofle_choc_pro.keymap`

Edit these files to customize your keyboard layout. See [ZMK documentation](https://zmk.dev/docs) for keycode references.

### Modifying Build Configuration

Edit `build.yaml` to change which keyboard configurations are built by GitHub Actions. You can:
- Add/remove keyboard variants
- Enable/disable ZMK Studio
- Change shields or add snippets
- Add custom CMake arguments

### ZMK Studio

ZMK Studio is enabled for the left halves of all keyboards. To use it:
1. Flash firmware with ZMK Studio enabled
2. Connect keyboard via USB
3. Access ZMK Studio web interface to configure keymap in real-time

## Resources

- [ZMK Documentation](https://zmk.dev/docs)
- [ZMK Discord](https://zmk.dev/community/discord/invite)
- [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/)
