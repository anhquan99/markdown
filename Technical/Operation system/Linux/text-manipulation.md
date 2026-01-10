# Text Manipulation
## Compare and manipulate file context
### `cat`
- View file from top down
### `tac`
- View file from bottom up
### `tail`
- Show last number of lines
- Default 10 lines are shown, use `-n` to change the default lines to shown  
### `head`
- Show first number of lines
- Like `tail`, use `-n` to change the default line to shown
### `sed`
- Short for **S**tream **Ed**itor
- Transform text by search and replace text
```shell
sed -i 's/[search text]/[replace text]/g' filename
# s is for substitute command
# g is for global to change all occurrences on each line, sed only replace the first thing on each line
# -i is for edit inline, this will change the file
```
### `cut`
- Extract positioned column in a file
```shell
# cut -d '[delimiter]' -f [column number] filename
cut -d ',' -f test.txt
```
### `uniq`
- Unique a content of a file by rows
```shell
# uniq filename
# example file text.txt  with content
# 1
# 1
# 2
# 2
uniq test.txt
# result:
# 1
# 2


# But if the context of the file is
# 1
# 2
# 1
# 2
# 2
uniq test.txt
# the reulst will be:
# 1
# 2
# 1
# 2
# this is where sort command comes in
```
### `sort`
- Sort content of a file by rows
```shell
# Example with the file
# 1
# 2
# 1
# 2
# 2
# We want it to unique all the value correctly
sort test.txt | uniq
# the | is called piping
```
### `diff`
- Show differences between 2 files
```shell
diff file1 file2

diff -c file1 file2 # show context 

diff -y file1 file2 # show side by side
sdiff file1 file2 # same command
```