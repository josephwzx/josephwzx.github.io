---
title: docGen - Command Line Tool
date: 2024-03-08 01:38:22
tags:
---

# Documentation Generator Tool

This documentation outlines the usage and functionality of a Documentation Generator Tool developed using Node.js and Python. The tool automatically generates documentation for Java code by leveraging machine learning models and the OpenAI GPT for generating docstrings.

## Overview

The Documentation Generator Tool is a command-line application that allows users to generate documentation for Java code. It supports processing individual files, entire directories, or specific commits within a Git repository. The tool utilizes a Python script (`docGen.py`) to analyze Java code and generate docstrings, which are then inserted into the source code at the appropriate positions. It provides flexibility by allowing users to choose between local machine learning models or the OpenAI GPT for generating documentation.

## Setup

1. Ensure Node.js and Python are installed on your system.
2. Clone the tool's repository to your local machine.
3. Install the required Python dependencies listed in `requirements.txt`.
4. Ensure the Python script `docGen.py` is in the same directory as the Node.js CLI tool or properly configured to be accessible.

## Usage

The CLI tool can be invoked with various commands to generate documentation for Java source code. Below are the primary commands and options available:

### Generating Documentation for a Repository Commit

To generate documentation for a specific commit in a Git repository:

```
docGen repo <repository URL> --commit <commit-hash> [--gpt]
```

### Generating Documentation for a Commit Range

To generate documentation for a range of commits within a Git repository:

```
docGen repo <repository URL> --start <start-commit-hash> --end <end-commit-hash> [--gpt]
```

### Generating Documentation for a Single File

To generate documentation for a single Java file:

```
docGen file <path/to/file> [--gpt]
```

### Generating Documentation for All Java Files in a Directory

To generate documentation for all Java files within a specified directory:

```
docGen path <path/to/directory> [--gpt]
```

### Generating Documentation for All Java Files in the Current Directory

To generate documentation for all Java files in the current directory:

```
docGen all [--gpt]
```

## Options

- `--gpt`: Use the OpenAI GPT for generating docstrings. If not specified, the tool will use the local machine learning model by default.

## Examples

Generating documentation for a single file using GPT:

```
docGen file src/MyClass.java --gpt
```

Generating documentation for a specific commit in a repository:

```
docGen repo https://github.com/user/repo --commit abc123
```

## Conclusion

The Documentation Generator Tool provides an efficient way to automatically generate and insert documentation into Java code. By leveraging advanced machine learning models and the power of OpenAI's GPT, it aims to reduce the manual effort required in documenting code, thus improving code readability and maintainability.