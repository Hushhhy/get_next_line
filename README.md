# Get Next Line - Read File Line by Line

![cover](assets/cover.png)

*This project has been created as part of the 42 curriculum by carpe.*

<p align="center">
  <img src="assets/get_next_linem.png" alt="Badge">
</p>

---

## 📋 Description

**Get Next Line** is a foundational C project from the 42 curriculum that teaches you how to efficiently read files line by line. This project focuses on implementing a powerful utility function that will become an essential tool in your programming toolkit.

The main challenge is to write a function that reads text from a file descriptor and returns complete lines one at a time, while properly managing memory and handling various edge cases. This project introduces the important concept of **static variables** in C and demonstrates how to maintain state across multiple function calls.

### Project Goals

- **Implement an efficient file reading function**: Master reading from file descriptors
- **Learn static variables**: Understand how to maintain state between function calls
- **Memory management**: Properly allocate and free memory without leaks
- **Handle edge cases**: Deal with various buffer sizes, file formats, and file descriptor types
- **Understand buffering**: Learn why and how to buffer data efficiently

---

## 🛠️ Instructions

### Compilation

To compile the project with a default buffer size of 42:

```bash
make
```

To compile with a specific buffer size (e.g., 1024):

```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=1024 get_next_line.c get_next_line_utils.c
```

### Available Make Rules

```bash
make              # Compile the mandatory part (creates executable)
make all          # Same as make
make bonus        # Compile the bonus part (multiple file descriptors)
make clean        # Remove object files (.o)
make fclean       # Remove object files and executables
make re           # Recompile everything (fclean + all)
```

### Using Get Next Line

To use this function in your own programs:

1. Include the header in your code:
   ```c
   #include "get_next_line.h"
   ```

2. Compile with the function source files:
   ```bash
   cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 your_program.c get_next_line.c get_next_line_utils.c -o your_program
   ```

3. Use the function:
   ```c
   #include <fcntl.h>
   #include <stdio.h>
   #include "get_next_line.h"

   int main(void)
   {
       int     fd;
       char    *line;

       fd = open("file.txt", O_RDONLY);
       if (fd < 0)
           return (1);
       
       while ((line = get_next_line(fd)) != NULL)
       {
           printf("%s", line);
           free(line);
       }
       close(fd);
       return (0);
   }
   ```

### Testing

The function should work correctly with:
- Regular text files
- Standard input (stdin)
- Various buffer sizes (1, 42, 1024, 9999, etc.)
- Files with and without trailing newlines
- Multiple file descriptors simultaneously (bonus part)

---

## 🎯 Core Functionality

### Main Function

```c
char *get_next_line(int fd);
```

**Parameters:**
- `fd`: The file descriptor to read from

**Return value:**
- A pointer to the line read (including the `\n` character, except at EOF)
- `NULL` if there is nothing left to read or if an error occurs

**Behavior:**
- Each call returns the next line from the file
- Lines include the terminating `\n` character
- The last line without a `\n` at EOF is returned without one
- The returned string is dynamically allocated and must be freed by the caller

### Mandatory Requirements

✅ Function works with file descriptors  
✅ Repeated calls read lines sequentially  
✅ Returns lines with `\n` (except last line if file doesn't end with `\n`)  
✅ Proper memory management (no leaks)  
✅ Works with various BUFFER_SIZE values  
✅ No segmentation faults or undefined behavior  
✅ Follows 42 Norm standards  

### Bonus Features

The bonus part extends functionality:

✅ Uses only **one static variable** (more elegant implementation)  
✅ Manages **multiple file descriptors simultaneously**  
✅ Can seamlessly switch between different file descriptors (fd3 → fd4 → fd5 → fd3, etc.)  

---

## 🔍 Algorithm Explanation

### Approach & Design Decisions

The Get Next Line function uses a **buffering strategy** with static variables to efficiently read lines from files:

#### Key Concepts

1. **Static Buffer Management**
   - A static variable maintains leftover data between function calls
   - This allows efficient reuse of read data without redundant file operations
   - When a line is found, the buffer is split, and the remainder is saved for the next call

2. **Reading Strategy**
   - Read in chunks of size `BUFFER_SIZE` using the `read()` system call
   - Search for the newline character (`\n`) in the read data
   - If found, return the complete line; if not, continue reading
   - This approach is more efficient than reading the entire file at once

3. **State Management (Mandatory Part)**
   - One static variable stores the buffer/leftover data
   - This design simplifies the implementation but works with only one file descriptor at a time
   - Perfect for sequential reading from a single file

4. **Multi-Descriptor Support (Bonus Part)**
   - One static variable stores a structure/linked list of descriptors
   - Each descriptor tracks its own reading state independently
   - Allows seamless switching between multiple files without losing progress

#### Why This Design?

- **Efficiency**: Minimizes system calls and memory allocation
- **Simplicity**: Elegant use of static variables to maintain state
- **Flexibility**: Works with any buffer size and file type (except binary)
- **Scalability**: Bonus implementation handles multiple files elegantly

#### Complexity Analysis

- **Time Complexity**: O(n) where n is the total number of characters read
- **Space Complexity**: O(BUFFER_SIZE) for the buffer, plus O(line_length) for each returned line
- **System Calls**: Minimized by buffering; only reads when necessary

---

## 📚 Project Structure

### Mandatory Part

```
get_next_line/
├── get_next_line.h         # Main header with function prototype
├── get_next_line.c         # Main function implementation
├── get_next_line_utils.c   # Helper functions
└── Makefile                # Build configuration
```

### Bonus Part (Optional)

```
get_next_line/
├── get_next_line_bonus.h         # Bonus header
├── get_next_line_bonus.c         # Bonus implementation (multi-fd)
├── get_next_line_utils_bonus.c   # Bonus helper functions
└── Makefile                      # Build with bonus rule
```

### Files Included

- **get_next_line.h**: Function prototype and necessary includes
- **get_next_line.c**: Main function that reads and returns lines
- **get_next_line_utils.c**: Helper functions (string manipulation, memory management)
- **get_next_line_bonus.h**: Extended header for bonus features
- **get_next_line_bonus.c**: Multi-file descriptor implementation
- **get_next_line_utils_bonus.c**: Additional utilities for bonus part

---

## 📋 Technical Specifications

### Compilation Requirements

- **Compiler**: `cc` (C compiler)
- **Mandatory flags**: `-Wall -Wextra -Werror`
- **Buffer size**: `-D BUFFER_SIZE=n` (required, n can be any positive integer)
- **Allowed functions**: `read()`, `malloc()`, `free()`
- **Forbidden**: `lseek()`, global variables, `libft` library

### Code Standards

- **No global variables** (except static ones)
- **No memory leaks** tolerated
- **No segmentation faults** or crashes
- **Adherence to 42 Norm**: Required for all code
- **Function protection**: Static variables maintain state safely

### Important Constraints

- Return lines WITH the `\n` character (except possibly the last line)
- Must handle different BUFFER_SIZE values (1, 42, 9999, etc.)
- Must work with regular files AND standard input
- Bonus: One static variable + multiple file descriptor support

---

## 📖 Resources

### File I/O and System Calls
- [read() man page](https://man7.org/linux/man-pages/man2/read.2.html) - System call for reading from file descriptors
- [File Descriptors in C](https://www.geeksforgeeks.org/file-descriptors-in-c/) - Understanding file descriptor concepts
- [open() man page](https://man7.org/linux/man-pages/man2/open.2.html) - File opening system call

### Static Variables in C
- [Static Variables in C](https://www.geeksforgeeks.org/static-variables-in-c/) - Understanding persistence and scope
- [Static Keyword in C](https://en.cppreference.com/w/c/language/storage_duration) - Detailed explanation of storage classes

### Memory Management
- [Dynamic Memory in C](https://en.cppreference.com/w/c/memory) - malloc() and free() best practices
- [Memory Leaks and Debugging](https://www.geeksforgeeks.org/memory-leak-in-c-and-how-to-avoid-it/) - Detecting and fixing memory issues

### String and Buffer Handling
- [String Functions in C](https://en.cppreference.com/w/c/string) - Standard string library reference
- [Buffer Overflow Prevention](https://owasp.org/www-community/attacks/Buffer_Overflow) - Security considerations

### 42 School Resources
- [42 Norm](https://github.com/42School/42cursus/blob/main/docs/en.norm.pdf) - Official 42 coding standard
- [42 Intranet](https://intra.42.fr) - Official project guidelines and updates

### How AI Was Used

AI was utilized for **documentation and README creation purposes only**:
- Structuring and organizing the README following 42 standards
- Explaining algorithm concepts and design decisions
- Providing clear compilation and usage instructions
- Writing example code snippets for illustration

**AI was NOT used for**:
- Core implementation of get_next_line()
- Solving algorithmic challenges
- Writing the main function logic
- Debugging or problem-solving during development

This aligns with 42's philosophy of building strong foundational knowledge through genuine intellectual effort, peer learning, and independent problem-solving.

---

## ✅ Verification Checklist

Before submission, verify:

- [ ] Function reads lines correctly from files
- [ ] Works with stdin (standard input)
- [ ] Handles various buffer sizes (1, 42, 1024, 9999)
- [ ] No memory leaks detected
- [ ] No segmentation faults
- [ ] Lines include `\n` (except last line when appropriate)
- [ ] Code follows 42 Norm
- [ ] Compiles with flags: `-Wall -Wextra -Werror -D BUFFER_SIZE=42`
- [ ] Bonus part (if submitted):
  - [ ] Uses only one static variable
  - [ ] Handles multiple file descriptors
  - [ ] Can switch between fds without losing state

---

## 🧪 Example Test Cases

### Basic File Reading
```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c test.c -o test
./test
```

### Testing Different Buffer Sizes
```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=1 get_next_line.c get_next_line_utils.c test.c -o test_1
./test_1

cc -Wall -Wextra -Werror -D BUFFER_SIZE=9999 get_next_line.c get_next_line_utils.c test.c -o test_9999
./test_9999
```

### Testing with stdin
```bash
echo -e "Hello\nWorld\nTest" | ./test
```

---

## 📌 Important Notes

- Each call to `get_next_line()` returns a newly allocated string that **must be freed** by the caller
- The function should handle files that do and don't end with a newline character
- Behavior is undefined when reading binary files (but you may implement a graceful handler)
- The BUFFER_SIZE value significantly impacts performance and should be tested thoroughly
- When writing multiple file descriptor support (bonus), ensure seamless switching between fds
- Static variables are your friend—use them wisely to maintain state!

---

## 📝 Debugging Tips

- **Valgrind**: Check for memory leaks
  ```bash
  valgrind --leak-check=full ./test
  ```
- **Strace**: Track system calls
  ```bash
  strace ./test
  ```
- **GDB**: Debug interactively
  ```bash
  gdb ./test
  ```
- Test with extremely small buffers (1 or 2) to catch edge cases
- Verify behavior with files that have no newline at EOF

---

*Last updated: May 2026*
