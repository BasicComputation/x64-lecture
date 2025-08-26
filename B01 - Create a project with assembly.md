# Create a project with assembly
1. Create a minimal executable for console in assembly.
2. Text editing tips for Visual Studio.

## Create project, write code, build executable and run it.
Download and install Visual Studio, start it. <br>
https://visualstudio.microsoft.com/

1. Select "Create a new project"

2. Select C++, Windows, console and then select emty project. Press Next.
<img width="563" height="206" alt="image" src="https://github.com/user-attachments/assets/9abd9e15-11b9-4677-8f1e-fd9291b00d13" />

3. Give the Project a name. Select a new location for storing the project if you want to.

4. This is a C++ project, we need to add the assembler as part of the process of building the executable.<br>
   In the "Solution Explorer" right click on the name of project, in my case "x64AssemblyOnly". <br>
   In the list go to "Build Dependencies" and then "Build Customizations" <br>
   In the list select "masm" and press OK. This will include the Macro Assembler. <br>
<img width="355" height="215" alt="image" src="https://github.com/user-attachments/assets/7197b0a9-e3ca-444a-99aa-c8a76be961e2" />

6. To add a source code file, thats where you write your code, again right click on the project, go to "add", and then "new item". <br>
  From the list choose "C++ File (.cpp)". Give it a name with file extension name .asm. Example "main.asm"

   **Note:** if you add a .asm file to your project before adding masm as part of the build process, <br>
   then it will not work. You then have to delete all .asm files and create them again. <br>

8. "main.asm" is opened for editing, but you find all your files listed in the Solution Explorer. <br>
   Now lets create a program that does nothing, except exit with a code to the operating system.

```asm
.code            ; code section of file.
                 ; This section of file will be loaded into a memory segment with read and execute permissions.

mainCRTStartup proc      ; mainCRTStartup is the name of a address,
                         ; it is rather the name of the offset into memory from where the executable is loaded.
                         ; (The address from where the executable is loaded is semi random, but can be fixed)
                         ; 'proc' tells that it is a function/procedure, and this name is a global symbol...
                         ; mainCRTStartup is by default the entry function name in C++ console projects.
                         ; Entry function name can be changed

  mov eax, 10            ; the 32 bit exit code is put in 32 bit part of 'A' register

  ret                    ; read value at address contained in rsp, set instruction pointer to what was read,
                         ; returning to the function that called the entry point of your program, mainCRTStartup.
                         ; The return function is in kernel32.dll, that function in turn calls a function in ntdll.dll
                         ; In ntdll.dll there will be a syscall instruction that hands control over to the kernel,
                         ; that close the process.
mainCRTStartup endp

end
```

7. Select "Release" and then press the green "Start without debugging" button, the arrow to the right. <br>
<img width="386" height="31" alt="image" src="https://github.com/user-attachments/assets/c52b89f8-a4a0-4351-8b18-1d9312c0e0fb" /> <br>
This will build the executable file and open up the command prompt and run the application. <br>
You can read the exit code there when the application closes.

9. To find the executable file, right click on the project, select "Open Folder in File Explorer". <br>
  There you find the project files. But to find the executable, go one step back to the folder for the "Solution". <br>
  Then go into the folder "x64", then into the folder "Release", there you find the EXE file. <br>
  I asked you to select "Release" at point 7, if you select "Debug" and build the executable, you will get another folder <br>
  called "Debug" that contain the debug version of the program. The difference between release and debug will be discussed at an other point.

11. If you double click on the EXE file, the command prompt opens, but then closes when the application exits. <br>
   If you search for "cmd" in the start menu you will find command prompt, start it. <br>
   - To navigate into a folder write "cd nameoffolder" and the press the enter key <br>
   Full path example: "cd C:\Tutorial\x64AssemblyOnly\x64\Release" <br>
   So copy the path to your executable from file explorer, in command prompt type cd and then press ctrl + v and then press enter. <br>
   - When you are in the folder of the executable, write the name of the executable to run it. <br>
     Do not need to end it with ".exe". <br>
   - To display the exit code of a program, type "echo %ERRORLEVEL%"
   - To list directories and files inside the folder, write "dir" and press enter.
   - "cd.." will move up to parent folder
   - If you press the tab key while partially written the name of file/folder, the full name will be entered.

## Editing tips
To make your code look clean you often tabulate with the tab key. <br>
In the editor you can select several lines and tabulate them all at the same time with the tab key. <br>
If you want the text a step back you can hold the shift key and press tab.

To change text in several lines at any position at once, select a place in text, hold alt key and then hold shift key, <br>
use down or up arrow keys to select more lines of text, then release alt and shift keys, now you can:
- insert text at the positions.
- hold shift and use right or left arrow keys to select text for overwriting or deletion.
- press end key to get to the end of text in each line or home key to get to the beginning of text.



