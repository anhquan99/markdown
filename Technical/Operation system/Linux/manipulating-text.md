# Manipulating Text
## `cat`
- Stand for concatenate, used to view file contents.
- `tac` is `cat` spelled backwards where it prints in reverse order of lines.
```bash
# write to file, waits for the user to edit/enter the text. Press CTRL-D at the beginning of the next line to save and exit
cat > <filename> 
# write to file, exit by entering eof
cat > <filename> << eof
```
## Working with large file
- Use `less` to view a file by page. `less` is `more` but with more capabilities.
- Use `head` to view first number (default 10) lines of file.
- Use `tail` to view last number (default 10) lines of file.
## View compressed files
- Use the associated utilities with letter `z` prefixed to view compressed files like:
	- `zcat, zless, zdiff`
	- `xzcat, xzless, xzdiff`
## `sed`
- Stand for stream editor.
- Used to filter text, as well as perform substitutions in data streams.
- Use `-i` option with care, it will save changes in the same file causing the irreversible action.

| Command                                 | Usage                                                                                                                            |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `sed -e command <filename>`             | Specify editing commands at the command line, process input from a file, and put the output on standard out (e.g., the terminal) |
| `sed -f scriptfile <filename>`          | Specify a script file containing `sed` commands, operate on file, and put output on standard out                                 |
| `echo "I hate you" \| sed s/hate/love/` | Use `sed` to filter standard input, putting output on standard out                                                               |
## `awk`
- Used to extract and then print specific content of a file.

| Command                                 | Usage                                                          |
| --------------------------------------- | -------------------------------------------------------------- |
| `awk '{ print $0 }' /etc/passwd`        | Print entire file                                              |
| `awk -F: '{ print $1 }' /etc/passwd`    | Print first field (column) of every line, separated by a colon |
| `awk -F: '{ print $1 $7 }' /etc/passwd` | Print first and seventh field of every line                    |
## File manipulation utilities
### `sort`
- Used to rearrange the line of a text file according to a sort key.
- Use with the `-u` to check for unique values after sorting.
```bash
sort <filename>
cat file1 file2 | sort
sort -r <filename> # sort the lines in reverse order
sort -k 3 <filename> # sort the lines by the 3rd field on each line
```
### `uniq`
- Used to remove duplicate *consecutive* lines in a text file, and is useful for simplifying the text display.
- `uniq` requires the duplicate entries must be consecutive, it is usually used with `sort`.
### `paste`
- Suppose you have a file that contains the full name of all employees and another file that lists their phone numbers and Employee IDs. You want to create a new file that contains all the data listed in three columns: name, employee ID, and phone number. How can you do this effectively without investing too much time?
- `paste` can be used to create a single file containing all three columns. The different columns are identified based on delimiters (spacing used to separate two fields). For example, delimiters can be a blank space, a tab, or an `Enter`. In the image provided, a single space is used as the delimiter in all files.
- `paste` accepts the following options:
	- `-d` delimiters, which specify a list of delimiters to be used instead of tabs for separating consecutive values on a single line. Each delimiter is used in turn; when the list has been exhausted, `paste` begins again at the first delimiter.
	- `-s`, which causes paste to append the data in series rather than in parallel; that is, in a horizontal rather than vertical fashion.
### `join`
- Suppose you have two files with some similar columns. You have saved employees’ phone numbers in two files, one with their first name and the other with their last name. You want to combine the files without repeating the data of common columns. How do you achieve this?
- The above task can be achieved using `join`, which is essentially an enhanced version of `paste`. It first checks whether the files share common fields, such as names or phone numbers, and then joins the lines in two files based on a common field.
### `split`
- Used to break up (split) a file into equal-sized segments for easier viewing and manipulation.
- By default, `split` breaks up a file into 1000-line segments.
## `grep`
- `grep` is extensively used as a primary text searching tool.
## `tr`
- Used to translate specified characters into other characters or to delete them.
```bash
tr [options] set1 [set2]
```
## `tee`
- `tee` takes the output from any command, and, while sending it to standard output, it also saves it to a file.
## `wc`
- Stand for word count.
- It counts number of lines, words, and characters in a file or list of files.

|Option|Description|
|---|---|
|`-l`|Displays the number of lines|
|`-c`|Displays the number of bytes|
|`-w`|Displays the number of words|
## `cut`
- Used for manipulating column-based files and is designed to extract specific columns.
- The default column separator is the **TAB** character. A different delimiter can be given as a command option.