# Shell

## Introduction to Shell for Data Science

{% embed url="https://www.datacamp.com/courses/introduction-to-shell-for-data-science" %}

To find out where you are in the filesystem, run the command `pwd` \(short for "**p**rint **w**orking **d**irectory"\). This prints the absolute path of your **current working directory**, which is where the shell runs commands and looks for files by default.

### Create, remove. look at dir and files

To find out what's there, type `ls` \(which is short for "**l**i**s**ting"\)

 The shell decides if a path is absolute or relative by looking at its first character: if it begins with `/`, it is absolute, and if it doesn't, it is relative.

move around in the filesystem using the command `cd` \(which stands for "change directory"\)

A single dot on its own, `.`, always means "the current directory", so `ls` on its own and `ls .` do the same thing, while `cd .` has no effect \(because it moves you into the directory you're currently in\).

One final special path is `~` \(the tilde character\), which means "your home directory", such as `/home/repl`. No matter where you are, `ls ~` will always list the contents of your home directory, and `cd ~` will always take you home.

`cp` copies a file, `mv` moves it from one directory to another, `mv` can also be used to rename files. `rm` stands for "remove".

`rmdir`. For added safety, it only works when the directory is empty, so you must delete the files in a directory _before_ you delete the directory. 

`mkdir directory_name` create a new \(empty\) directory. 

### view contents of a file

`cat`, which just prints the contents of files onto the screen. \(Its name is short for "concatenate", meaning "to link things together", since it will print all the files whose names you give it, one after the other.\)

**piece by piece:** When you `less` a file, one page is displayed at a time; you can press spacebar to page down or type `q` to quit.

If you give `less` the names of several files, you can type `:n` \(colon and a lower-case 'n'\) to move to the next file, `:p` to go back to the previous one, or `:q` to quit.

`head`prints the first few lines of a file \(where "a few" means 10\)

```text
head -n 3 seasonal/summer.csv
```

 Can give `ls` the flag `-R` \(which means "recursive"\). If you use `ls -R` in your home directory, you will see something like this:

```text
backup          course.txt      people          seasonal

./backup:

./people:
agarwal.txt

./seasonal:
autumn.csv      spring.csv      summer.csv      winter.csv
```

This shows every file and directory in the current level, then everything in each sub-directory, and so on.

```text
ls -R -F ~
```

上面的语句把home所有的dir全打印出来，文件夹后面/ 文档颜色也不一样。

If you want to select columns, you can use the command `cut`. 

```text
cut -f 2-5,8 -d , values.csv
```

which means "select columns 2 through 5 and columns 8, using comma as the separator". `cut` uses `-f` \(meaning "fields"\) to specify columns and `-d`\(meaning "delimiter"\) to specify the separator. You need to specify the latter because some files may use spaces, tabs, or colons to separate columns.

`history` will print a list of commands you have run recently,`man` gives a manual.  `!55` to re-run the 55th command in your history \(if you have that many\). You can also re-run a command by typing an exclamation mark followed by the command's name, such as `!head` or `!cut`, which will re-run the most recent use of that command.

### select lines containing specific values

`cut` selects columns, and `grep` selects lines according to what they contain. , `grep bicuspid seasonal/winter.csv` prints lines from `winter.csv` that contain "bicuspid".

`grep` can search for patterns as well; more common flags:

* `-c`: print a count of matching lines rather than the lines themselves
* `-h`: do _not_ print the names of files when searching multiple files
* `-i`: ignore case \(e.g., treat "Regression" and "regression" as matches\)
* `-l`: print the names of files that contain matches, not the matches
* `-n`: print line numbers for matching lines
* `-v`: invert the match, i.e., only show lines that _don't_ match

`paste` can be used to combine data files instead of cutting them up.

### Save to file 

 `>` tells the shell to redirect output to a file.

### combine commands

```text
head -n 5 seasonal/summer.csv | tail -n 3
```

The pipe symbol tells the shell to use the output of the command on the left as the input to the command on the right.

### count the records 

`wc` \(short for "word count"\) prints the number of characters, words, and lines in a file \(`-c`, `-w`, or `-l` \). 

### wildcards

* `?` matches a single character, so `201?.txt` will match `2017.txt` or `2018.txt`, but not `2017-01.txt`.
* `[...]` matches any one of the characters inside the square brackets, so `201[78].txt` matches `2017.txt` or `2018.txt`, but not `2016.txt`.
* `{...}` matches any of the comma-separated patterns inside the curly brackets, so `{*.txt, *.csv}` matches any file whose name ends with `.txt` or `.csv`, but not files whose names end with `.pdf`.

###   sort lines of text

`sort` puts data in order. By default it does this in ascending alphabetical order, but the flags `-n` and `-r` can be used to sort numerically and reverse the order of its output, while `-b` tells it to ignore leading blanks and `-f`tells it to **f**old case \(i.e., be case-insensitive\). Pipelines often use `grep` to get rid of unwanted records and then `sort` to put the remaining records in order.

### uniq

  `uniq`, whose job is to remove duplicated lines. it removes _adjacent_ duplicated lines. 所以可以uniq之前先sort一下

### Stop a running program

 `Ctrl` + `C`to end it. This is often written `^C` in Unix documentation; note that the 'c' can be lower-case.

### print a variable's value

A simpler way to find a variable's value is to use a command called `echo.`to get the value of a variable called `X`, you must write `$X`. \(This is so that the shell can tell whether you mean "a file named X" or "the value of a variable named X".\)

###  **shell variable**

To create a shell variable, you simply assign a value to a name:

```text
training=seasonal/summer.csv
```

_without_ any spaces before or after the `=` sign. 

 Once you have done this, you can check the variable's value with:

```text
echo $training
```

### For loop in Shell

#### variables

```text
for filetype in gif jpg png; do echo $filetype; done
```

1. The structure is `for` ...variable... `in` ...list... `; do` ...body... `; done`
2. The list of things the loop is to process \(in our case, the words `gif`, `jpg`, and `png`\).
3. The variable that keeps track of which thing the loop is currently processing \(in our case, `filetype`\).
4. The body of the loop that does the processing \(in our case, `echo $filetype`\).

#### filenames

```text
for filename in seasonal/*.csv; do echo $filename; done
```

#### dataset

record a list of filenames. For example, if you define `datasets` like this:

```text
datasets=seasonal/*.csv
```

you can display the files' names later using:

```text
for filename in $datasets; do echo $filename; done
```

This saves typing and makes errors less likely. 注意上面两个$。如果第一个没有的话，出来的就是一行，dataset，因为它读的是变量名而不是变量里的内容wildcard。

### edit a file

`nano filename`, it will open `filename` for editing \(or create it if it doesn't already exist\).

* 
  `Ctrl` + `K`: delete a line.

  `Ctrl` + `U`: un-delete a line.

  `Ctrl` + `O`: save the file \('O' stands for 'output'\).

  `Ctrl` + `X`: exit the editor.

### record last few steps

1. Run `history`.
2. Pipe its output to `tail -n 10` \(or however many recent steps you want to save\).
3. Redirect that to a file called something like `figure-5.history`.

`cp seasonal/s* ~   
grep -h -v Tooth spring.csv summer.csv>temp.csv  
history|tail -n 3 > steps.txt`

### save commands to re-run

save the following command in a file called `headers.sh`:

```text
head -n 1 seasonal/*.csv
```

```text
bash headers.sh
```

This tells the shell \(which is just a program called `bash`\) to run the commands contained in the file `headers.sh`

### pass filenames to script

`$@`  means "all of the command-line parameters given to the script". 

```text
sort $@ | uniq
bash unique-lines.sh seasonal/summer.csv seasonal/autumn.csv
```

The shell lets you use `$1`, `$2`, and so on to refer to specific command-line parameters. 

```text
cut -d , -f $2 $1
```

and then run it using:

```text
bash column.sh seasonal/autumn.csv 1
```

### write loops in a shell script

```text
# Print the first and last data records of each file.
for filename in $@
do
    head -n 2 $filename | tail -n 1
    tail -n 1 $filename
done
```

### mistake: wait for file names

```text
head -n 5 | tail -n 3 somefile.txt
```

 `tail` goes ahead and prints the last three lines of `somefile.txt`, but `head` waits forever for keyboard input, since it wasn't given a filename and there isn't anything ahead of it in the pipeline.

## Data Processing using Shell

{% embed url="https://www.datacamp.com/courses/data-processing-in-shell" %}

### Sample data

header line plus a random sample of lines after

```text
awk 'BEGIN {srand()} !/^$/ { if (rand() <= .01 || FNR==1) print $0}'
```

### Screen 

| screen -S sessionname | 创建screen会话时为会话指定一个名字 |
| :---: | :---: |
| screen -ls | 列出所有对话 |
| screen -r sessionname  | 进入某一对话 |
| Ctrl+A+D | 暂时断开对话 |
| killall screen | 杀了所有对话 |

