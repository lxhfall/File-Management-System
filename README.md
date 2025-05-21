# Operating Systems (Linux/Unix) File-Management-System
A C++ command-line tool simulating a Linux file system with file/directory operations, keyword search, and device communication using POSIX system calls.

CSC 377 File Management System Simulation
A command-line C++ application that simulates a simplified Linux file system. It supports basic file and directory operations, keyword/filetype search, and device communication with interrupt handling. Designed to demonstrate core concepts in operating systems.

Features
File Management

New <filename>: Create a new file

Del <filename>: Delete an existing file

View <filename>: View contents of a file

Edit <filename>: Append text to a file

Directory Management

Makedir <dirname>: Create a new directory

Changedir <dirname>: Change working directory

Removedir <dirname>: Remove an empty directory

Search & Listing

Filelist: List files grouped by extension

Search <keyword>: Search files by name

Device Simulation

Device <name>: Simulate device activation and interrupt

Kill <name>: Deactivate a device

Technologies Used
C++

POSIX system calls (access, remove, mkdir, chdir, opendir, readdir, etc.)

Standard libraries: fstream, dirent.h, unistd.h, map, vector

How to Run
Clone the repository:


Compile the program:
g++ -o file_manager file_management_new.cpp

Run the program:
./file_manager

Example Commands
> New notes.txt  
> Edit notes.txt  
> View notes.txt  
> Makedir test  
> Changedir test  
> Filelist  
> Device printer  
> Kill printer  
> Exit

Authors
Xiaohu Luo

Jace Johnson

License
This project is for educational use and demonstration purposes.
