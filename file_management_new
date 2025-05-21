#include <iostream>
#include <fstream>
#include <string>
#include <sstream>
#include <cstdio>
#include <cstdlib>
#include <unistd.h>
#include <dirent.h>
#include <csignal>
#include <map>
#include <vector>
#include <sys/stat.h>
#include <sys/types.h>
#include <algorithm>

using namespace std;

/**************************************************************
 *  Project Name : CSC 377 File Management System
 *  Description  : A simplified file system simulation that
 *                 supports file and directory operations,
 *                 keyword/filetype search, and I/O handling.
 *
 *  Author       : Xiaohu Luo, Jace Johnson
 *  Date         : 04/04/2025
 **************************************************************/

map<string, bool> deviceActive;// Keeps track of device states
string originalDir;  // Store original directory at startup

// Simulate device interrupt
void simulateInterrupt(const string& device) {
    cout << "[INTERRUPT] Received from device: " << device << endl;
}

// Command: New <filename>
// Creates a new file if it doesn't already exist
void createFile(const string& filename) {
    // Check if the file already exists using an input file stream (ifstream)
    ifstream checkFile(filename);
    if (checkFile.good()) {
        cerr << "Error: File '" << filename << "' already exists." << endl;
        checkFile.close();
        return;
    }
    // If the file does not exist, proceed to create it using ofstream (output stream)
    ofstream file(filename);
    // Check if the file was created successfully
    if (file) {
        cout << "File '" << filename << "' created successfully." << endl;
    } else {
        cerr << "Failed to create file." << endl;
    }
    // Close the file stream (important to release system resources)
    file.close();
}

// Command: Del <filename>
// Deletes the specified file if it exists
void deleteFile(const string& filename) {
    // Step 1: Check if the file exists using access()
    // F_OK checks for the file's existence (0 = exists, -1 = does not exist)
    if (access(filename.c_str(), F_OK) != 0) {
        cerr << "Error: File '" << filename << "' does not exist." << endl;
        return;
    }

    // Step 2: Try to remove the file using remove()
    // remove() returns 0 on success, non-zero on failure
    if (remove(filename.c_str()) == 0) {
        cout << "File '" << filename << "' deleted successfully." << endl;
    } else {
        perror("Error deleting file");
    }
}

// Command: View <filename>
// Displays the content of the file line by line
void viewFile(const string& filename) {
    // Try to open file
    ifstream file(filename);
    // Check if the file was opened successfully
    if (!file) {
        cerr << "Error: Cannot open file '" << filename << "' for reading." << endl;
        return;
    }

    // If the file is open, read it line by line and print to the console
    string line;
    cout << "Contents of '" << filename << "':\n";
    while (getline(file, line)) {
        cout << line << endl;
    }
    // Close the file stream to release system resources
    file.close();
}

// Command: Edit <filename>
// Opens the file and appends lines until ":wq" is typed
void editFile(const string& filename) {
    // Open the file in append mode using ofstream
    // ios::app ensures new content is added at the end without overwriting existing content
    ofstream file(filename, ios::app);

    if (!file) {
        cerr << "Error: Cannot open file '" << filename << "' for writing." << endl;
        return;
    }

    cout << "Enter text to append to '" << filename << "' (type ':wq' to save and quit):" << endl;

    string line;
    // Read input from user line-by-line
    while (getline(cin, line)) {
        // If the user types ":wq", exit the editing loop
        if (line == ":wq") break;
        // Otherwise, write the line to the file and move to the next line
        file << line << endl;
    }

    // Close the file to save changes and release resources
    file.close();

    cout << "Changes saved to '" << filename << "'." << endl;
}

// Command: Filelist
// Lists all files in the current directory, grouped by file extension
void listFiles() {
    // Open the current directory using opendir(".")
    DIR* dir = opendir(".");
    struct dirent* entry;// Struct to hold directory entry info

    // Check if the directory was opened successfully
    if (!dir) {
        perror("Could not open current directory");
        return;
    }

    // Create a map to store files grouped by extension
    // Key   = file extension (e.g., "txt", "cpp")
    // Value = vector of filenames with that extension
    map<string, vector<string>> fileMap;

    // Iterate through directory entries using readdir()
    while ((entry = readdir(dir)) != nullptr) {
        string name = entry->d_name;
        if (name == "." || name == "..") continue;

        // Find the last '.' in the filename to get the file extension
        size_t pos = name.find_last_of(".");

        // If there's a '.' found, extract extension after it
        // Otherwise, use "no_extension" as the key
        string ext = (pos != string::npos) ? name.substr(pos + 1) : "no_extension";

        // Add the filename to the corresponding extension group
        fileMap[ext].push_back(name);
    }

    // Close the directory stream after reading all entries
    closedir(dir);

    // Print the grouped file list
    cout << "Files grouped by file type:\n";
    for (const auto& pair : fileMap) {
        cout << "\n[" << pair.first << "]\n";// Print extension group
        for (const string& file : pair.second) {
            cout << "  " << file << endl;// Print each file under that group
        }
    }
}

// Command: Search <keyword>
// Searches for files that contain the keyword in their names
void searchFiles(const string& keyword) {
    // Open the current directory
    DIR* dir = opendir(".");
    // Check if directory was opened successfully
    if (dir == nullptr) {
        perror("Could not open current directory");
        return;
    }

    bool found = false;// Flag to check if any matching file is found
    struct dirent* entry;// Pointer to hold directory entry data

    // Iterate over all entries in the directory
    while ((entry = readdir(dir)) != nullptr) {
        if (string(entry->d_name).find(keyword) != string::npos) {
            cout << entry->d_name << endl;
            found = true; // Set the flag to true as a match is found
        }
    }

    if (!found) cout << "No matching files found." << endl;

    closedir(dir);
}

// Command: Device <deviceName>
// Simulates communication with a device and triggers interrupt
void communicateWithDevice(const string& device) {
    // Check if the device is already active
    if (deviceActive[device]) {
        cout << "Communicating with " << device << "...\n";
        simulateInterrupt(device);
    } else {
        cout << "Device '" << device << "' not active. Initializing...\n";
        deviceActive[device] = true;
    }
}

// Command: Kill <deviceName>
// Turns off communication with the specified device
void killDevice(const string& device) {
    if (deviceActive[device]) {
        cout << "Killing communication with device '" << device << "'...\n";
        deviceActive[device] = false;
    } else {
        cout << "Device '" << device << "' is already inactive.\n";
    }
}

// Command: Makedir <dirname>
// Creates a new directory with default permissions
void makeDirectory(const string& dirname) {

    // Attempt to create the directory with permission mode 0755
    // 0755 means: Owner can read/write/execute; Group and Others can read/execute
    if (mkdir(dirname.c_str(), 0755) == 0) {
        cout << "Directory '" << dirname << "' created." << endl;
    } else {
        perror("Failed to create directory");
    }
}

// Command: Changedir <dirname>
// Changes current working directory
void changeDirectory(const string& dirname) {
    // If empty or matches originalDir, go back
    if (dirname.empty() || dirname == originalDir) {
        if (chdir(originalDir.c_str()) == 0) {
            // If successful, get and display the current working directory
            char cwd[1024];
            getcwd(cwd, sizeof(cwd));
            cout << "Returned to original directory: " << cwd << endl;
        } else {
            perror("Failed to return to original directory");
        }
        return;
    }

    // Otherwise, try changing to specified directory
    if (chdir(dirname.c_str()) == 0) {
        // On success, get and display the new working directory
        char cwd[1024];
        getcwd(cwd, sizeof(cwd));
        cout << "Changed to directory: " << cwd << endl;
    } else {
        perror("Failed to change directory");
    }
}

// Command: Removedir <dirname>
// Deletes an empty directory
void removeDirectory(const string& dirname) {
    // Attempt to remove the directory using rmdir (only works if directory is empty)
    if (rmdir(dirname.c_str()) == 0) {
        cout << "Directory '" << dirname << "' removed." << endl;
    } else {
        perror("Failed to remove directory");
    }
}

// Main command loop
int main() {
    // Store the original working directory
    char cwd[1024];
    getcwd(cwd, sizeof(cwd));
    originalDir = cwd;

    string input;
    cout << "Enhanced File System + Device & Directory Management (Linux)\n";
    cout << "Commands: New, Del, View, Edit, Filelist, Search, Device <dev>, Kill <dev>\n";
    cout << "          Makedir, Changedir, Removedir, Exit\n";

    while (true) {
        cout << "\n> ";
        getline(cin, input);// Read full line of user input

        istringstream iss(input);
        string command;
        string argument;

        iss >> command;// Extract the command keyword
        getline(iss, argument);// Extract the rest of the input as an argument

        // Trim leading spaces from argument
        argument.erase(argument.begin(), find_if(argument.begin(), argument.end(), [](unsigned char ch) {
            return !isspace(ch);
        }));

        // Command Dispatcher
        if (command == "New") {
            createFile(argument);
        } else if (command == "Del") {
            deleteFile(argument);
        } else if (command == "View") {
            viewFile(argument);
        } else if (command == "Edit") {
            editFile(argument);
        } else if (command == "Filelist") {
            listFiles();
        } else if (command == "Search") {
            searchFiles(argument);
        } else if (command == "Device") {
            communicateWithDevice(argument);
        } else if (command == "Kill") {
            killDevice(argument);
        } else if (command == "Makedir") {
            makeDirectory(argument);
        } else if (command == "Changedir") {
            changeDirectory(argument);
        } else if (command == "Removedir") {
            removeDirectory(argument);
        } else if (command == "Exit" || command == "exit") {
            cout << "Exiting... Goodbye!" << endl;
            break;
        } else {
            // Handle invalid command
            cout << "Invalid command. Try: New, Del, View, Edit, Filelist, Search, Device, Kill, Makedir, Changedir, Removedir, Exit" << endl;
        }
    }

    return 0;
}
