# General instructions for the practical session

## 1. bash console / R console: where am I?


When you connect to the VM using the ssh command, you are in the **bash console**. You can recognize that you are in the bash console by looking at the **prompt** (i.e. the left part of the input line); it should look line this:

```bash
user5@powerfulrutherford-30fb1:~$
```

Each participant has a home directory on the virtual machine, for example:

```bash
/home/user5/
```

Each user can jump directly into his home directory using the simple command

```bash
cd
````



Sometimes, we will use **R** to process the data; to activate R, simply type the following into the bash console:

```bash
/usr/bin/R
```

Now, you will be in the **R console**, which you can recognize by looking at the prompt, which now looks like this:

```
> 
```

To get back to the **bash console**, simply type the following in the R console (and answer `n` to any question):

```r
q()
```

You should be back to the **bash console** (check the prompt)!

## 2. Where is the data?

Each user has in his home directory 2 folders:
* `data`: this folder contains all the data (fastq / bam / ...) that you will need for the analysis
* `analysis`: this folder will be used to store all the outputs of your analysis.

During the analysis, we will create further subdirectories into the `analysis` directory.

## 3. How do I access the tools needed for the analysis?

We will use a number of software tools for the analysis; we have prepared a **virtual environment** using **conda** containing all required tools. You first need to activate this environment, by running the simple command (in the bash console):

```bash
conda activate chipatac
```

You should see the prompt changing from 

```
user5@powerfulrutherford-30fb1:~$
```
to
```
(chipatac) user5@powerfulrutherford-30fb1:~$
```

Now you can use all tools described in the tutorial!

