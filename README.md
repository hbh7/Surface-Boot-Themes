# INSTALL INSTRUCTIONS
1. Run: `sudo mkdir /usr/share/grub/themes` (IF DONE SKIP TO TWO.)
2. Run: `sudo cp --recursive ./surface /usr/share/grub/themes`
3. Add the following line to `/etc/default/grub`:

    ```
    GRUB_THEME=/usr/share/grub/themes/surface/theme.txt
    ```
4. Make the repair title and icon for kernel repair.

    Open `/etc/grub.d/10_linux` and search for (towards bottom):
    ```sh
    echo "submenu '$(gettext_printf "Advanced options for %s" "${OS}" | grub_quote)'
    ```
    Insert the following immediatly after:
    ```
    --class recovery --class repair
    ```
    (note: edit "Advanced options for %s" to "Repair %s" or something to your liking)

5. Make the Secure Boot title and icon.

    Open `/etc/grub.d/30_uefi-firmware` and search for (towards the bottom) :
    ```sh
    menuentry '$LABEL'
    ```
    Insert the following immediatly after:
    ```
    --class secure --class recovery
    ```
    (note: replace `LABEL=System Setup` with `LABEL=Secure Boot`)

6. Make the Windows title and icon for Windows launch.

    If you have run `boot-repair`, open `/etc/grub.d/25_custom` and you will see the following:
    ```sh
    menuentry "Windows UEFI bkpbootmgfw.efi" { 
      search --fs-uuid --no-floppy --set=root BE36-A896 
      chainloader (${root})/EFI/Microsoft/Boot/bkpbootmgfw.efi 
    } 
    menuentry "Windows Boot UEFI loader" { 
      search --fs-uuid --no-floppy --set=root BE36-A896 
      chainloader (${root})/EFI/Boot/bkpbootx64.efi 
    }
    ```
    Delete one of the menuentry settings then insert `--class windows` after the `"`:<br>
    (note: edit title as desired.)
    
    If not, open `/etc/grub.d/30_os-prober` and search for :
    ```sh
    '$(echo "${LONGNAME} $onstr" | grub_quote)' --class windows
    ```
    Replace `${LONGNAME} $onstr` with Windows (note: edit title as desired.)
    
    If you're like me and don't have this line, you can manually edit your grub.cfg and change the line.  (Do this after step 7)
    
    It will look something like `Windows Boot Manager (on /dev/driveidentifier)`. 
    
    You could also use this sed script to change it automatically. `sudo sed -i 's/Windows Boot Manager (on \/dev\/nvme0n1p2)/Windows 10/g' /boot/grub/grub.cfg`
    
    Do note though that these changes are lost after regenerating the grub config file, such as in step 7, so do that first, then this. 
    
7. Run: `sudo grub-mkconfig -o /boot/grub/grub.cfg`
