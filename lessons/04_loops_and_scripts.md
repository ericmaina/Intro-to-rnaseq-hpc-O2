---
title: "The Shell: Loops & Scripts"
author: "Bob Freeman, Mary Piper, Radhika Khetani"
date: "Thursday, May 5, 2016"
---

Approximate time: 60 minutes

## Learning Objectives

* Capture previous commands into a script to re-run in one single command
* Understanding variables and storing information
* Learn how to use variables to operate on multiple files


Now that you've been introduced to a number of commands to interrogate your data, wouldn't it be great if you could do this for each set of data that comes in, without having to manually re-type the commands?

Welcome to the beauty and purpose of shell scripts.

## Shell scripts

Shell scripts are **text files that contain commands we want to run**. As with any file, you can give a shell script any name and usually have the extension `.sh`. For historical reasons, a bunch of commands saved in a file is usually called a shell script, but make no mistake, this is actually a small program. 


We are finally ready to see what makes the shell such a powerful programming environment. We are going to take the commands we repeat frequently and save them into a file so that we can **re-run all those operations** again later by typing **one single command**. Let's write a shell script that will do two things:

1. Tell us our current working directory
2. List the contents of the directory 

First open a new file using `vim`:

```bash
$ vim listing.sh
```

Then type in the following lines in the `listing.sh` file:

```bash
echo "Your current working directory is:"
pwd
echo "These are the contents of this directory:"
ls -l 
```

Exit `vim` and save the file. Now let's run the new script we have created. To run a shell script you usually use the `bash` or `sh` command.

```bash
$ sh listing.sh
```

> Did it work like you expected?
> 
> Were the `echo` commands helpful in letting you know what came next?

This is a very simple shell script. In this session and in upcoming sessions, we will be learning how to write more complex ones, and use the power of scripts to make our lives much easier.

## Bash variables

A **variable** is a common concept shared by many programming languages. Variables are essentially a symbolic/temporary name for, or a reference to, some information. Variables are analogous to "buckets", where information can be stored, maintained and modified. 

Extending the bucket analogy: the bucket has a name associated with it, i.e. the name of the variable, and when referring to the information in the bucket, we use the name of the bucket, and do not directly refer to the actual data stored in it (which is by design, since the stored data is variable).

In the example below, we define a variable or a 'bucket' called `filename`. We will put the filename `Mov10_oe_1.subset.fq` as the value inside the bucket.

```bash
$ filename=Mov10_oe_1.subset.fq
```
Once you press return, you should be back at the command prompt. *How do we know that we actually created the bash variable?* We can use the echo command to list what's inside `filename`:

```bash
$ echo $filename
```

What do you see in the terminal? If the variable was not created, the command will return nothing. Did you notice that when we created the variable we just typed in the variable name, but when using it as an argument to the `echo` command, we explicitly use a `$` in front of it (`$filename`)? Why? 

Well, in the former, we're setting the value, while in the latter, we're retrieving the value. This is standard shell notation (syntax) for defining and using variables. **Don't forget the `$` when you want to retrieve the value of a variable!** 

Let's try another command using the variable that we have created. In the last lesson, we introduced the `wc -l` command which allows us to count the number of lines in a file. We can count the number of lines in `Mov10_oe_1.subset.fq` by referencing the `filename` variable, but first move into the `raw_fastq` directory:

```bash
$ cd ~/unix_workshop/raw_fastq
$ wc -l $filename
```

***

**Exercise**

* Reuse the `$filename` variable to store a different file name, and rerun the commands we ran above (`wc -l`, `echo`)

***

Ok, so we know variables are like buckets, and so far we have seen that bucket filled with a single value. **Variables can store more than just a single value.** They can store multiple values and in this way can be useful to carry out many things at once. Let's create a new variable called `filenames` and this time we will store *all of the filenames* in the `raw_fastq` directory as values. 

To list all the filenames in the directory that have a `.fq` extension, we know the command is:

```bash
$ ls *.fq
```

Now we want to *assign* the output of `ls` to the variable:

```bash
$ filenames=`ls *.fq`
```

> Note the syntax for assigning output of commands to variables, i.e. the ticks around the `ls` command.

Check and see what's stored inside our newly created variable using `echo`:
	
```bash
$ echo $filenames
```

Let's try the `wc -l` command again, but this time using our new variable `filenames` as the argument:

```bash
$ wc -l $filenames
```

What just happened? Because our variable contains multiple values, the shell runs the command on each value stored in `filenames` and prints the results to screen. 

***

**Exercise**

* Use some of the other commands we learned in previous lessons (i.e. `head`, `tail`) on the `filenames` variable. 

***

## Loops

Another powerful concept in the Unix shell and useful when writing scripts is the concept of "Loops". We have just shown you that you can run a single command on multiple files by creating a variable whose values are the filenames that you wish to work on. But what if you want to **run a sequence of multiple commands, on multiple files**? This is where loop come in handy!

Looping is a concept shared by several programming languages, and its implementation in bash is very similar to other languages. 

The structure or the syntax of (*for*) loops in bash is as follows:

```bash
for (variable_name) in (list)
do
(command1 $variable_name)
.
.
done
```

where the ***variable_name*** defines (or initializes) a variable that takes the value of every member of the specified ***list*** one at a time. At each iteration, the loop retrieves the value stored in the variable (which is a member of the input list) and runs through the commands indicated between the `do` and `done` one at a time. *This syntax/structure is virtually set in stone.* 

For example, we can run the same commands (`echo` and `wc -l`) used in the "Bash variables" section but this time run them sequentially on each file:

```bash
for var in *.fq
 do
   echo $var
   wc -l $var
 done
```

#### What does this loop do? 

Most simply, it writes to the terminal (`echo`) the name of the file and the number of lines (`wc -l`) for each files that end in `.fq` in the current directory. The output is almost identical to what we had before.

In this case the list of files is specified using the asterisk wildcard: `*.fq`, i.e. all files that end in `.fq`. Then, we execute 2 commands between the `do` and `done`. With a loop, we execute these commands for each file at a time. Once the commands are executed for one file, the loop then executes the same commands on the next file in the list. 

Essentially, **the number of items in the list (variable name) == number of times the code will loop through**, in our case that is 2 times since we have 2 files in `~/unix_workshop/raw_fastq` that end in `.fq`, and these filenames are stored in the `var` variable.

Of course, `var` is a useless variable name. But since it doesn't matter what variable name we use, we can make it something more intuitive.

```bash
for filename in *.fq
 do
   echo $filename
   wc -l $filename
 done
```
In the long run, it's best to use a name that will help point out a variable's functionality, so your future self will understand what you are thinking now.

Pretty simple and cool, huh?

### The `basename` command

Before we get started on creating more complex scripts, we want to introduce you to a command that will be useful for future scripting. The `basename` command is used for extracting the base name of a file, which is accomplished using string splitting to strip the directory and any suffix from filenames. Let's try an example, by first moving back to your home directory:

```bash
$ cd
```

The we will run the `basename` command on one of the FASTQ files. Be sure to specify the path to the file:

```bash
$ basename ~/unix_workshop/raw_fastq/Mov10_oe_1.subset.fq
```

What is returned to you? The filename was split into the path `unix_workshop/raw_fastq/` and the filename `Mov10_oe_1.subset.fq`. The command returns only the filename. Now, suppose we wanted to also trim off the file extension (i.e. remove `.fq` leaving only the file *base name*). We can do this by adding a parameter to the command to specify what string of characters we want trimmed.

```bash
$ basename ~/unix_workshop/raw_fastq/Mov10_oe_1.subset.fq .fq
```

You should now see that only `Mov10_oe_1.subset` is returned. 

***
**Exercise**

* How would you modify the above `basename` command to only return `Mov10_oe_1`?

***

## Automating with Scripts
	
Now that you've learned how to use loops and variables, let's put this processing power to work. Imagine, if you will, a script that will run a series of commands that would do the following for us each time we get a new data set:

- Use for loop to iterate over each FASTQ file
- Generate a prefix to use for naming our output files
- Dump out bad reads into a new file
- Get the count of the number of bad reads and generate a summary for each file
- And after all the FASTQ files are processed, write the summary to a log file

You might not realize it, but this is something that you now know how to do. Let's get started...

Rather than doing all of this in the terminal we are going to create a script file with all relevant commands. Move back in to `unix_workshop` and use `vim` to create our new script file:

```bash
$ cd ~/unix_workshop

$ vim generate_bad_reads_summary.sh
```

We always want to start our scripts with a shebang line: 

```bash
#!/bin/bash
```

This line is the absolute path to the Bash interpreter. The shebang line ensures that the bash shell interprets the script even if it is executed using a different shell.

After the shebang line, we enter the commands we want to execute. First we want to move into our `raw_fastq` directory:

```bash
cd ~/unix_workshop/raw_fastq
```

And now we loop over all the FASTQs:

```bash
for filename in *.fq
```

For each file that we process we can use `basename` to create a variable that will uniquely identify our output file based on where it originated from:

```bash
do
  # create a prefix for all output files
  base=`basename $filename .subset.fq`
```

and then we execute the commands for each loop:

```bash
  # tell us what file we're working on
  echo $filename
  
  # grab all the bad read records into new file
  grep -B1 -A2 NNNNNNNNNN $filename > $base-badreads.fastq
``` 
  
We'll also count the number of these reads and put that in a new file, using the count flag of `grep`:

```bash
  # grab the number of bad reads and write it to a summary file
  grep -cH NNNNNNNNNN $filename > $base-badreads.count.summary
done
```

If you've noticed, we used a new `grep` flag `-H` above; this flag will report the filename along with the match string. This is useful for when we generate the summary file.

And now, as a best practice of capturing all of our work into a running summary log:

```bash
# and add this summary to our run log
cat *badreads.count.summary >> runlog.txt
```

Save and exit `vim`, and voila! You now have a script you can use to assess the quality of all your new datasets. Your finished script, complete with comments, should look like the following:

```bash
#!/bin/bash 

# enter directory with raw FASTQs
cd ~/unix_workshop/raw_fastq

# count bad reads for each FASTQ file in our directory
for filename in *.fq 
do 

  # create a prefix for all output files
  base=`basename $filename .subset.fq`

  # tell us what file we're working on	
  echo $filename

  # grab all the bad read records
  grep -B1 -A2 NNNNNNNNNN $filename > $base-badreads.fastq

  # grab the number of bad reads and write it to a summary file
  grep -cH NNNNNNNNNN $filename > $base-badreads.count.summary
done

# and add this summary to our run log
cat *badreads.count.summary >> runlog.txt

```

To run this script, we simply enter the following command:

```bash
$ sh generate_bad_reads_summary.sh
```

To keep our data organized, let's move all of the bad read files out of the `raw_fastq` directory into a new directory called `other`, and the script to a new directory called `scripts`.

```bash
$ mkdir scripts

$ mv raw_fastq/*bad* other/
$ mv generate_bad_reads_summary.sh scripts/
```

---
*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*

* *The materials used in this lesson were derived from work that is Copyright © Data Carpentry (http://datacarpentry.org/). 
All Data Carpentry instructional material is made available under the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0).*
* *Adapted from the lesson by Tracy Teal. Original contributors: Paul Wilson, Milad Fatenejad, Sasha Wood and Radhika Khetani for Software Carpentry (http://software-carpentry.org/)*

