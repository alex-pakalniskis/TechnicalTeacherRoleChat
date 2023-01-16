# Intro to Linux Terminal
Run locally or with Killercoda: https://killercoda.com/playgrounds/scenario/ubuntu

## Part 1

### Open a terminal

* `ctrl + alt + T`

### Create and manipulate folders and files
#### `ls`
https://man7.org/linux/man-pages/man1/ls.1.html
* `ls`
* `ls -la`
* `ls code`

#### `mkdir`
https://man7.org/linux/man-pages/man1/mkdir.1.html
* `mkdir jan2023_rolechat`
* `mkdir jan2023_rolechat/nested_folder`
* `ls`
* `ls jan2023_rolechat`

#### `cd`
https://linuxcommand.org/lc3_man_pages/cdh.html
* `cd jan2023_rolechat`
* `mkdir another_nested && cd another_nested`
* `ls`
* `cd ..`

#### `touch`
https://man7.org/linux/man-pages/man1/touch.1.html
* `touch somefile`
* `touch anotherfile.txt`
* `touch another_nested/yetanotherfile.rs`

#### `git`
https://man7.org/linux/man-pages/man1/git.1.html
* `git clone https://github.com/alex-pakalniskis/TechnicalTeacherRoleChat.git`

#### `nano`
https://linux.die.net/man/1/nano
* `nano open_this_file_with_nano.txt`

#### `cp`
https://man7.org/linux/man-pages/man1/cp.1.html
* `cp open_this_file_with_nano.txt copied_with_cp.txt`

#### `mv`
https://linux.die.net/man/1/mv
* `mv copied_with_cp.txt moved_with_mv.txt`

#### `cat`
https://man7.org/linux/man-pages/man1/cat.1.htm
* `cat open_this_file_with_nano.txt`
* ``` bash
  cat > hellothere.txt << EOF
  hello there 
  EOF
  cat hellothere.txt
  ```

## Part 2
Coming next month

## References
* https://ubuntu.com/tutorials/command-line-for-beginners#1-overview
