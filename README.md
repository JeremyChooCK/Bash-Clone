
# Bash Clone

  

## Description

  

This project is a custom implementation of a bash-like shell called "minishell". It provides a command-line interface to interact with the operating system, supporting a subset of the features found in the standard bash shell.

  

## Features

  

*  **Command Execution:** Executes commands with arguments.

*  **Piping:** Supports piping between commands using the `|` operator.

*  **Redirection:**

* Input redirection from a file using `<`.

* Output redirection to a file using `>`.

* Appending output to a file using `>>`.

*  **Heredoc:** Supports here documents for providing input to a command using `<<`.

*  **Environment Variables:**

* Expands environment variables (e.g., `$USER`).

* Handles the `$?` special variable to get the exit status of the last command.

*  **Built-in Commands:**

*  `echo`: Prints arguments to the standard output.

*  `cd`: Changes the current directory.

*  `pwd`: Prints the current working directory.

*  `export`: Sets environment variables.

*  `unset`: Unsets environment variables.

*  `env`: Prints the environment variables.

*  `exit`: Exits the shell.

*  **Signal Handling:** Handles `Ctrl+C` (SIGINT) and `Ctrl+\` (SIGQUIT) gracefully.

*  **Command History:** Maintains a history of executed commands.

  

## How it Works

<img src="img/minishell flowchart.png"></a>

The minishell operates in a continuous loop, processing user input and executing commands. Here's a detailed breakdown of its workflow:

  

1.  **Display Prompt and Read Input:** The shell displays a prompt and waits for the user to enter a command. The input is read using the `readline` library, which also handles command history.

  

2.  **Parsing:** The raw input string is parsed to create a structured representation of the command.

*  **Tokenization:** The input is split into tokens based on spaces and special characters like `|`, `<`, and `>`. The shell correctly handles single and double quotes, preventing tokenization within quoted strings.

*  **Command Structure:** The tokens are organized into a command structure, identifying the command, its arguments, and any pipes or redirections.

*  **Variable Expansion:** The parser expands environment variables prefixed with a `$`. It also handles the special `$?` variable, which holds the exit status of the previously executed command.

  

3.  **Command Execution:** The `execute_command` function is the central point for command execution.

*  **Built-in vs. External:** The shell first checks if the command is a built-in command (e.g., `cd`, `exit`, `echo`). If it is, the corresponding function is called directly within the shell's process.

*  **External Commands:** If the command is not a built-in, the shell assumes it's an external command that needs to be executed from the file system.

  

4.  **Finding and Executing External Commands:**

*  **Path Resolution:** For external commands, the shell searches for the executable file. It retrieves the `PATH` environment variable, which contains a colon-separated list of directories. The shell iterates through these directories, prepending each directory to the command name until a valid, executable file is found.

*  **Forking and Executing:** Once the full path to the executable is resolved, the shell uses the `fork()` system call to create a new child process.

* The child process then uses `execve()` to replace its own image with the program to be executed. This runs the command in a separate process, preventing it from crashing the shell.

* The parent process waits for the child process to finish using `waitpid()` and then retrieves its exit status.

  

5.  **Pipes and Redirections:** The shell uses file descriptors to manage the input and output streams of the executed commands.

*  **Redirection (`<`, `>`, `>>`):** Before executing a command, the shell checks for redirection operators.

* If input redirection (`<`) is present, the shell opens the specified file and uses `dup2()` to replace the standard input file descriptor (`STDIN_FILENO`) with the file descriptor of the opened file.

* For output redirection (`>` or `>>`), the shell opens or creates the output file and uses `dup2()` to replace the standard output file descriptor (`STDOUT_FILENO`) with the file descriptor of the output file.

* Crucially, the original `stdin` and `stdout` file descriptors are saved before redirection and restored after the command has finished executing.

*  **Pipes (`|`):** When commands are chained together with pipes, the shell creates a pipe using the `pipe()` system call, which provides a pair of file descriptors: one for reading and one for writing.

* The standard output of the command on the left side of the pipe is redirected to the write-end of the pipe.

* The standard input of the command on the right side of the pipe is redirected to the read-end of the pipe.

* This is achieved by forking separate processes for each command in the pipeline and using `dup2()` in each child process to set up the correct input and output streams.

  

## How to Compile and Run

  

1.  **Clone the repository:**

```bash

git clone <repository-url>

```

2.  **Compile the project:**

```bash

make

```

3.  **Run the shell:**

```bash

./minishell

```

  

## Project Structure

  

*  `src/`: Contains the source code for the minishell.

*  `inc/`: Contains the header files.

*  `libft/`: Contains a custom library of utility functions.

*  `Makefile`: The makefile for compiling the project.

## Functions used

readline, rl_on_new_line, rl_replace_line, rl_redisplay,
add_history, printf, malloc, free, write, access, open, read,
close, fork, waitpid, signal, exit, getcwd, chdir, stat, unlink,
execve, dup2, pipe, perror, getenv, ft_setenv

