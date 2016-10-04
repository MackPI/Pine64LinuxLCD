# Pine64LinuxLCD

If all you want is to get the playbox LCD working on your pine 64, Open a terminal and enter the following commands.

<code>cd /boot/pine64</code>

<code>sudo wget https://github.com/MackPI/Pine64LinuxLCD/raw/master/sun50i-a64-pine64-plus.dtb</code>

<code>sudo systemctl disable sunxi-disp-tool</code>

Then reboot and the LCD should start working. ( This is not tested on non plus boards )

The last command isn't needed on the Ubuntu image. On the Debian Images, re-enable the HDMI output with:

<code>sudo systemctl enable sunxi-disp-tool</code>


##Process to get Playbox LCD working on Pine64.

I have the Playbox LCD working on xfce debian running on the 3.10.102-3-pine64-longsleep kernel running on a 2GB Pine64+

I copied the DTB files from /boot/pine64 into a folder in the home directory.

<code>mkdir ~/dtb</code>

<code>cp /boot/pine64/*.dtb ~/dtb</code>

I recommend making a second copy of the dtb file in case something goes wrong(like not booting at all)

I decompiled the files.  The only one I needed was sun50i-a64-pine64-plus.dtb(may have to install dtc here)

<code>cd ~/dtb</code>

<code>dtc -O dts -I dtb sun50i-a64-pine64-plus.dtb > sun50i-a64-pine64-plus.dts</code>

Will probably need a different file for non plus boards but the changes should be the same.
I used mousepad to edit the dts file

The changes I made are as follows:

In the section disp@01000000: 
  I changed screen0_output_type from 0x3 to 0x1
  I changed screen0_output_mode from 0xa to 0x4
  
In the section LCD0@1c0c000:
  I changed lcd_used from 0x0 to 0x1
  I changed lcd_x from 0x0 to 0x400
  I changed lcd_y from 0x0 to 0x258
  I changed lcd_width from 0x0 to 0x400
  I changed lcd_height from 0x0 to 0x258
  
In the section boot_disp:
  I changed output_type from 0x3 to 0x1
  I changed output_mode from 0xa to 0x4
  
I recompiled the dtb file and copied it back into /boot/pine64.

<code>cd ~/dtb</code>

<code>dtc -O dtb -o sun50i-a64-pine64-plus.dtb -b O sun50i-a64-pine64-plus.dts</code>

<code>sudo cp sun50i-a64-pine64-plus.dtb /boot/pine64/sun50i-a64-pine64-plus.dtb</code>

This far got me scrolling text on boot but the screen would go black before the login prompt with HDMI still working.

~~Next I edited /etc/systemd/system/sysinit.target.wants/sunxi-disp-tool.service~~

~~sudo mousepad /etc/systemd/system/sysinit.target.wants/sunxi-disp-tool.service~~

~~I commented out the lines that did something. Which is everything but the section headers
Putting a # on every line should work.  I used comments because I want to eventually get both LCD and HDMI working at the same time~~

Easier way to do the same thing

<code>sudo systemctl disable sunxi-disp-tool</code>

Then reboot and LCD works HDMI does not. Touch is not working either.
