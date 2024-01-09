# Terminal Tips and Basic Commands
This file contains basic commands and tips about the Linux terminal.
Feel free to refer back as you navigate through NanoCLUST installation and usage.

## File paths in the terminal

In the terminal, file locations follow a simple pattern: it's like a map to find your stuff!

Imagine your files are in folders. The address to a file is written like this: `parent_folder/subfolder/file.txt`.

- `parent_folder`: It's like the starting point, the big folder that holds everything.
- `/`: This slash is like a road that leads to the next folder.
- `subfolder`: It's a folder inside the `parent_folder`.
- `file.txt`: Ah, here's the treasure! It's inside the `subfolder`.

So, the path guides you through folders, helping you find your files easily.

### Absolute paths
Absolute paths in the terminal denote the complete file location starting from the system's root directory. 
They are like GPS coordinates for your files, indicating the full pathway regardless of your current location within the file system.
They always begin with a forward slash /, denoting the root directory. 

For example `/home/username/documents` points directly to the 'documents' folder within the 'username' directory, irrespective of the current location.

### Relative paths
Unlike absolute paths, relative paths specify file locations concerning your current directory within the system. 
They're like giving directions based on where you're standing, navigating through folders relative to your present location.

For example `./programs/` or `programs/` refer to the 'programs' folder located in the current directory and `../documents/` refers to the 'documents' folder located in the directory one level up from the current location.

---

## Basic commands

### `cd` command

The `cd` command stands for "change directory." It allows you to move between directories within the terminal. You can use absolute and relative paths with this command.

Usage:
```bash
cd directory_name/
```

Replace `directory_name/` with the name of the directory you want to navigate to.

### `ls` command

The `ls` command lists the contents of a directory.

Usage:
```bash
ls
```

This command will display the files and directories in the current location.

### `pwd` command

The `pwd` command stands for "print working directory." It shows the current absolute directory path.

Usage:
```bash
pwd
```

Executing this command will display the full path of the current directory.

### `mkdir` command

In the terminal, the `mkdir` command is used to create new directories or folders. It stands for "make directory."

Usage:
```bash
mkdir directory_name/
```

Replace `directory_name/` with the desired name for your new directory. For instance, if you want to create a directory named `data/`, you would enter:

```bash
mkdir data/
```

### `cp` command

The `cp` command is used in the terminal to copy files and directories from one location to another.

Usage:
```bash
cp source_file destination_directory/
```

Replace `source_file` with the name of the file you want to copy and `destination_directory` with the location where you want to copy the file.

For example, to copy a file named `example.txt` to a directory named `backup`, you would use:

```bash
cp example.txt backup/
```

#### Copying directories:

To copy an entire directory along with its contents, utilize the `-r` flag (recursive option) with the `cp` command:

```bash
cp -r source_directory/ destination_directory/
```

Replace `source_directory/` with the name of the directory you want to copy and `destination_directory/` with the location where you want to copy the directory.

For instance, to copy a directory named `data/` and all its contents into a directory named `archive/`, you would execute:

```bash
cp -r data/ archive/
```
### `mv` command

The `mv` command in the terminal is used to move files or directories from one location to another or rename them.

Usage:
```bash
mv source_file destination_directory/
```

Replace `source_file` with the name of the file you want to move and `destination_directory/` with the location where you want to move the file.

For instance, to move a file named `document.txt` to a directory named `archives/`, you would use:

```bash
mv document.txt archives/
```

#### Renaming

The `mv` command can also be used to rename files. To rename a file, specify the source file and the new filename:

```bash
mv old_filename new_filename
```

Replace `old_filename` with the current name of the file and `new_filename` with the desired new name. For example:

```bash
mv file1.txt newfile.txt
```

This command will rename `file1.txt` to `newfile.txt`.

#### Moving directories:

Similar to moving files, you can move directories using `mv`. The syntax is the same:

```bash
mv source_directory/ destination_directory/
```

Replace `source_directory/` with the directory you want to move and `destination_directory/` with the location where you want to move it.

