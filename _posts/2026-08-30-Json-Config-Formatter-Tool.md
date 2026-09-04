---
layout: post
title: "Building a JSON Inspector CLI in Python: What I Learned"
date: 2026-08-29
---

# Building a JSON Inspector CLI in Python: What I Learned

I've been learning Python by building projects rather than only following tutorials.

For this project, I wanted to build something that was a little more serious than a single Python script.

The result is a command-line JSON Inspector and DevTool that can format, validate, analyze, search, compare, and batch-process JSON files.

It started as a way for me to practice Python, but while building it I ended up learning much more about structuring a project, recursion, command-line interfaces, file handling, testing, and debugging my own assumptions.

This is a record of what I built and, more importantly, what I learned while building it.

---

## What I wanted to build

JSON is everywhere in software development, and I wanted to build a small command-line tool that could do more than simply read and print a JSON file.

The basic idea was:

> Give the program a JSON file and let it inspect and manipulate that file from the terminal.

The finished tool can:

* Pretty-print or minify JSON
* Sort keys while formatting
* Detect duplicate keys
* Validate JSON against a simple required-fields schema
* Calculate structural statistics
* Display a tree view of the JSON structure
* Search for keys or values recursively
* Compare two JSON files
* Batch-process JSON files inside directories
* Create backups before modifying files
* Run safely with separate output files instead of overwriting the original

The README describes the project as a modular, package-based Python project rather than one large script.

That architecture became one of the main things I wanted to practice.

---

# Why I didn't want to make one big Python file

One of the things I wanted to learn from this project was how larger Python programs are organized.

When you're learning Python, it is easy to put everything into one file.

At first, that works perfectly well.

But as more features are added, the file can become difficult to understand. Formatting logic, searching logic, validation, file handling, command-line arguments, and everything else start living together.

So I decided to split the project into packages, with each package responsible for a particular job.

The project ended up with separate areas for:

* CLI argument parsing
* Core JSON processing
* Analysis
* Searching
* Comparing JSON files
* Batch processing
* File storage and backups
* Tests

The idea was that `main.py` would primarily act as the entry point: parse the arguments and delegate the work to the appropriate package rather than containing all the feature logic itself.

This was one of the main reasons I built the project.

I wasn't only trying to make a JSON tool.

I was trying to learn **how to organize a Python project when it starts becoming bigger than one script.**

---

# The features I built

## 1. Formatting JSON

The most basic feature is formatting JSON.

For example, the tool can pretty-print a JSON file:

```bash
python main.py data.json
```

It can also minify the JSON:

```bash
python main.py data.json --mode minify
```

And I added an option to sort keys:

```bash
python main.py data.json --mode pretty --sort-keys
```

The output can either be printed to the terminal or written to another file.

I also added an in-place option that creates a backup before overwriting the original file.

That led me into another part of the project that I hadn't thought much about initially: **defensive file handling**.

If a program is going to modify someone's file, simply making the modification work isn't enough.

It should also consider what happens if the user makes a mistake.

That's why the project has backup support and separate output paths.

---

# 2. Detecting duplicate JSON keys

This was one of the more interesting things I discovered while working on the project.

Python's normal JSON parsing behavior can silently overwrite duplicate keys.

That means JSON containing the same key more than once inside an object can result in information being overwritten rather than the program immediately telling me that the input contains a duplicate key.

So I added duplicate-key detection.

This became a good example of something I was learning while building rather than something I fully understood before starting.

It also reinforced something I keep discovering while learning programming:

**The behavior of a library or language feature isn't always exactly what I assume it is.**

Sometimes the only way to discover that is to test it.

---

# 3. Structural statistics

I wanted the tool to be able to look at a JSON document and tell me something about its structure.

The statistics feature counts things such as:

* Objects
* Arrays
* Keys
* Values
* Maximum nesting depth

For example:

```bash
python main.py data.json --stats
```

This feature introduced me more deeply to **recursive data traversal**.

JSON can contain objects inside objects, arrays containing objects, objects containing arrays, and so on.

So instead of only looking at the top level, the program needs to keep going deeper into the structure.

That same idea ended up being useful in several other parts of the project too.

---

# 4. Building a JSON tree view

I also wanted to visualize the structure of a JSON document in the terminal.

So I created a tree view:

```bash
python main.py data.json --tree
```

Instead of displaying only the raw JSON, the program represents its structure as branches.

This was another place where recursive traversal became important.

The program needs to look at the current part of the JSON structure and, if it contains another object or array, continue traversing that structure.

---

# 5. Searching through JSON

Another feature I wanted was the ability to search inside a JSON file.

There are two basic search modes:

```bash
python main.py data.json --search-key email
```

and:

```bash
python main.py data.json --search-value true
```

The important part wasn't only finding a matching key or value.

I wanted the tool to tell me **where** the match occurred.

For example:

```text
root -> users -> [0] -> profile -> email
```

So the search engine recursively walks through the JSON and keeps track of the path it has taken.

This was another place where recursion became useful.

---

# 6. Comparing two JSON files

I also built a structural diff feature.

For example:

```bash
python main.py old.json --diff_file new.json
```

The program compares the two JSON structures and reports:

* `[DELETED]`
* `[ADDED]`
* `[CHANGED]`

It doesn't only compare the top-level keys.

The comparison recursively goes into nested objects and arrays and reports the full path of the difference.

This was probably one of the features that made me appreciate recursive thinking more.

The same general problem keeps appearing:

> "Look at this structure. If there's another structure inside it, go into that one too."

Once I recognized that pattern, I started seeing recursion in several different features rather than thinking of each feature as completely unrelated.

---

# 7. Batch processing

The tool can also accept a directory instead of a single JSON file.

For example:

```bash
python main.py ./some_folder --mode pretty --sort-keys
```

It recursively finds JSON files inside the directory and formats them.

I also added behavior for invalid files so that an invalid JSON file can be skipped instead of stopping the entire batch operation.

This was another reminder that real programs need to think about situations that don't happen in the "happy path."

---

# 8. Configuration

I also added a small configuration system.

The `config.json` file controls default behavior such as indentation and the default formatting mode.

For example:

```json
{
    "indent_spaces": 4,
    "default_mode": "pretty"
}
```

One thing I wanted to practice here was handling configuration failure safely.

If the configuration file is missing or malformed, the program falls back to built-in defaults instead of simply crashing.

This was another small step toward thinking about what happens when users give the program unexpected input.

---

# The bug that taught me "no errors" doesn't mean "correct"

This was probably one of my favorite lessons from the project.

I had a command that was supposed to show statistics about a JSON file.

The command technically worked.

There was no exception.

There was no traceback.

There was no obvious error.

But it wasn't behaving the way I intended.

It was also printing the raw JSON file first, and then printing the formatted JSON again afterward.

I hadn't told the program to stop after doing what that particular command required.

So the program was doing more work than I expected.

And because everything technically executed successfully, I didn't immediately notice it.

I only caught the problem when I stopped and traced through what each command-line option was actually doing.

That was a very useful lesson for me:

> **A program running without errors doesn't necessarily mean the program is correct.**

Sometimes the problem isn't that the program crashes.

Sometimes the problem is that it does something you never intended it to do.

As someone still learning, I think this is an important distinction to understand.

---

# Writing tests for the first time

This project was also the first time I seriously wrote a test suite for one of my projects.

Initially, I wasn't completely convinced.

My first thought was basically:

> "I'm already writing hundreds of lines of code. Why am I writing even more code just to test the code?"

Then I actually ran the tests.

The project currently has 24 tests covering the comparator, engine, query engine, statistics, tree view, and validator modules.

And the tests ran in seconds.

That's when the idea finally clicked for me.

If I manually checked every feature after every change, it would take much more effort.

With tests, I can make a change and quickly check whether the existing behavior still works.

I don't think I've suddenly become an expert at testing because of one project.

But I now understand **why tests are useful**, rather than simply knowing that "professional developers write tests."

That's a much more useful lesson for me.

---

# What I learned about recursion

If I had to pick one technical concept that appeared repeatedly in this project, it would probably be recursion.

I used recursive traversal independently in several features:

* Statistics
* Tree view
* Search
* Diff

The interesting part wasn't simply learning the syntax of recursion.

It was recognizing the pattern.

A JSON structure can contain another structure, which can contain another structure, and so on.

So many operations on JSON naturally become:

1. Look at the current object or value.
2. Process what is there.
3. If it contains another nested structure, process that too.
4. Continue until there is nothing deeper to process.

I started this project knowing that recursion was a Python concept I needed to understand.

I finished it with a better intuition for **where recursion can actually be useful**.

That's a much bigger improvement for me.

---

# What I learned about project architecture

Another major lesson was that architecture isn't something that only matters in huge professional applications.

Even in a learning project, separating responsibilities made me think differently about the code.

The project has individual packages responsible for things such as searching, comparing, analyzing, batch processing, and storage.

The goal wasn't to create the most sophisticated architecture possible.

It was to practice breaking a problem into smaller responsibilities.

I also learned that having more files doesn't automatically mean having better architecture.

The important question is:

> **Does each part have a clear responsibility?**

That's something I want to get better at with future projects.

---

# What I still haven't solved

This project isn't perfect.

I intentionally documented some limitations rather than pretending everything is finished.

For example, the schema validation is currently simple. It checks for required top-level fields but doesn't implement full JSON Schema validation with types, nested schemas, or formats.

The search-value conversion also has limitations: numeric coercion currently handles positive integers, while floats and negative numbers are treated as strings.

Batch mode also doesn't currently provide a dry-run or separate output option, so the README recommends backing up important directories before using it.

These aren't necessarily things I need to solve immediately.

For me, documenting limitations is part of finishing a project too.

A project doesn't have to be perfect to be useful.

---

# What I would improve next

If I continued developing this project, there are several things I'd like to improve.

The first would be making schema validation more capable.

I'd also like to improve numeric search handling and make batch processing safer by adding something like a dry-run or output-directory option.

But I'm choosing to leave those as future improvements for now.

The purpose of this project was not to create a production-ready JSON development tool.

It was to learn.

And I think it did that job pretty well.

---

# Project structure

The final project is organized roughly like this:

```text
main.py
cli/
    parser.py

core/
    config_loader.py
    engine.py

analyzer/
    statistics.py
    tree_view.py
    validator.py

search/
    query_engine.py

difference/
    comparator.py

batch/
    processor.py

storage/
    file_manager.py

tests/
```

The main entry point parses the command-line arguments and delegates the work to the appropriate package.

This structure was one of the main things I wanted to practice with this project.

---

# Looking back

When I started this project, I thought I was mainly building a JSON utility.

Looking back, the actual project was much bigger for me as a learning experience.

I practiced:

* Building a multi-package Python project
* Using `argparse` for a CLI
* Working with JSON
* Recursive traversal
* File I/O
* Defensive handling of user input
* Configuration
* Writing tests with `pytest`
* Debugging behavior that didn't produce errors
* Thinking about separation of responsibilities
* Reviewing and improving my own code

Most importantly, I started to understand something that is difficult to learn from tutorials alone:

**Writing code that runs is only the beginning.**

You also have to understand what the code is actually doing, test your assumptions, find the places where your mental model is wrong, and gradually improve the design.

I'm still learning Python.

There are still plenty of things I don't understand.

But that's exactly why I'm building these projects.

Not to prove that I already know everything.

To find out what I don't know.

One project at a time.

