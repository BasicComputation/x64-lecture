# Organizing code with multiple files in MASM
Create a new project, include MASM as part of the build process. <br>
Add C++ source code file named "main.asm" and another named "procs.asm". <br>
Add another C++ file and name it "procs.inc", this file will be included in other .asm files. <br>

In main.asm
```asm
include procs.inc

.code

mainCRTStartup proc
	call func1
	mov eax, [globalData]
	ret
mainCRTStartup endp

end
```

In procs.asm
```asm
public globalData	  ; make variable name global

.code

func1 proc
	mov [globalData], 1
	ret
func1 endp

.data
globalData dd ?

end
```

In procs.inc
```asm
IFNDEF PROCS_INC			; if not defined "PROCS_INC", include up until end if
	
	PROCS_INC equ 1			; define PROCS_INC to be 

	func1 proto
	extern globalData:dword

ENDIF
```


### Explanation
When building the executable, each .asm file is processed separatly and temporary object files is produced. <br>
Next you have linker phase, where the object files are put togheter and reference to global symbols are linked. <br>
Symbols are names of functions or data.

So each part does not know about the content of the others, to tell that a reference to symbol is external to file, <br>
the proto keyword are used for functions and extern keyword is used for data.

In this example you have a .inc file that is included in main.asm, the content of the .inc file is paste in during the <br>
preprocessing stage, so before the other steps.

The reason to create a .inc file, is that you don't want to write the symbol declaration and other values in every .asm file <br>
that needs to use functions, data and so forth, from another part.  <br>
Conventionally there is a .inc file per .asm file, the name of the .inc file is given same name as the .asm file.

You see IFNDEF "name" (if not defined) and ENDIF (end if), that is ensure that what is between those is only processed once. <br>
So if name is not defined, define the name with a value, so next time it is included, the name is defined. <br>
So the .inc file can be included many times, this can be necessary when having a complex setup.


