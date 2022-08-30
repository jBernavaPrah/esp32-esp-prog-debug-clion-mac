# How to configure CLion to be used as a debugger?
This is the configuration that permit to use the standalone idf.py with CLion debugger.

## Required steps before proceding:
- You need a esp-prog attached correctly to the esp32 using the JTAG connection. See here: https://docs.espressif.com/projects/espressif-esp-iot-solution/en/latest/hw-reference/ESP-Prog_guide.html

### 1) Create a new Run configuration as type "Remote Debug".
`Go to: Top Menu > Run > Edit Configuration`

#### Set the configuration:
- 'target remote' args: `:3333`.
- Debugger: select Custom GDB executable and search for the `xtensa-esp32-elf-gdb`. This executable can be found here: `/Users/<user>/.espressif/tools/xtensa-esp-elf-gdb/<version>/xtensa-esp-elf-gdb/bin/xtensa-esp32-elf-gdb`.
- Symbol file: `build/<projectname>.elf`. Run at least one time idf.py build, after that you will found the .elf file under the build/ path.
- Sysroot: Root path of the project.

#### Remove `Build` action in the `Before Launch`.

### 2) On the root of the project, create a `gdbinit` file with this content:

```
set remote hardware-watchpoint-limit 2
mon reset halt
flushregs
thb app_main
c
```
Create a second file on `~/.gdbinit` path the with: 
```
set auto-load safe-path /
```
This will instruct gdb to safely load the .gdbinit from your project path.

### 3) Test debug
In one terminal run `idf.py -p <your/tty.serial> openocd build flash monitor`.
This will start the *build*, will *flash* to esp32 and run the *monitor* command. Will also start the openocd for us. 

From CLion, start a debug session. You will see that the debugger will stop on the function `app_main` (mentioned on the `gdbinit` file).

## That's it. If works for you, thanks me adding a start to this project!

And for any inconvenience, open an issue. 

### Knowing issues:

The Configuration shown is enough to execute a debug. But sometimes seems that the esp32 is not resetting itself.   
The current workaround is to try to use the default way `idf.py openocd gdb` command and then retry with CLion. 
Any help on fixing this issue is really apreciated! 






