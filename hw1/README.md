# Homework 1: Enron email command-line wrangling 

###Your challenge
Given a large directory of email messages, generate csv files that capture the message contents in a structured form suitable for loading into a database, spreadsheet or statistical package.  

###Your tools
For this assignment, you are limited to using [Ruby](http://ruby-lang.org), [bash](http://www.gnu.org/software/bash/), and the [standard Unix utilities](http://en.wikipedia.org/wiki/List_of_Unix_utilities).  We strongly encourage you to use Ruby's built-in [CSV](http://ruby-doc.org/stdlib-1.9.2/libdoc/csv/rdoc/CSV.html) and [find](http://ruby-doc.org/stdlib-1.9.2/libdoc/find/rdoc/Find.html) libraries, and the [mail](https://github.com/mikel/mail/blob/master/README.md) gem to deal with email files, directory trees, and csv files.  All of these are pre-installed on the instructional machines for you.  You should not need to use any additional Ruby gems in your solution. (You may find [ruby-debug](http://bashdb.sourceforge.net/ruby-debug.html) useful along the way -- it is also installed.)

###Your constraints
You need to be able to handle an input directory that is far larger than the memory of the computer that runs the script.  To do so, you should:

1. write *streaming* Ruby and bash code that only examines one email message at a time, and
2. utilize UNIX utilities like `sort` that provide out-of-core, divide-and-conquer algorithms.  


You should not need to write much code in either Ruby or bash.  Take advantage of UNIX utilities as much as you can.  In particular, note that there is no need for you to write an out-of-core algorithm to complete this homework.

###Getting started
In these instructions, we assume you are using your CS186 inst account. We will be using Ruby v1.9.2 and bash version 4.2.10(1). You can try to develop on your own machine, but you'll be on your own -- the CS186 staff will only answer configuration questions pertaining to inst.  And eventually you need to turn in a solution that runs "out of the box" on the instructional machines, as described below.

Log into your class account, and check out the git repository for the course:

    % git clone XXXXXXXXXXXXX

This will create a directory called sp12 in your home directory, which will contain a subdirectory called hw1. change directory into there and look around:

    % cd sp12/hw1
    % ls
    hw1.sh    README.md         test              tokens.csv
    mail.csv  state_counts.csv  token_counts.csv
    %

In addition to this README file, you will see:

* `hw1.sh`, a skeleton of the bash file you will write
* `test`, a subdir with ruby unit tests you can use to validate your solution,
* `mail.csv`, `state_counts.csv`, `token_counts.csv` and `tokens.csv`:
   correctly-formatted example outputs.


###Specification
Your solution should be driven by a `hw1.sh` script that is passed one argument: the root of a directory that contains valid email files:

    % ./hw1.sh ~cs186/sp12/hw1/enron_sample
    
The directory may contain arbitrary files, but the ones that are to be parsed as email must have names ending in ".txt".

The script should overwrite the four csv output files, as follows:

* `mail.csv` should be a legal csv file containing the same header row as the example.  The remainder of the file should contain the specified fields from all the email messages.  See the discussion of "Reading an email" in the [documentation on the `mail` gem](https://github.com/mikel/mail).
* `tokens.csv` should be a csv containing two fields: a message\_id and a token.  This file is generated by taking the `body` field of a message, and splitting it on non-alphanumeric characters into separate tokens (substrings) which are converted to all-lowercase characters.  After splitting and lowercasing, each token should be copied into the `tokens.csv` file, prepended by the associated `message\_id' (and a comma).  *Note that a given token may appear multiple times per `message\_id`, and/or multiple times across different `message\_id`s.*
* `token_counts.csv` should sum up the number of occurrences of each *distinct* token in the `tokens.csv` file.  Each row should be of the form `token, count`.
* `state_counts.csv` is intended to store the rough result of the question "how often is each state mentioned in an email?" It should contain those rows from `token_counts.csv` with the (lowercase) name of a US state in the `token` field.  For states containing two tokens in their name, we will be a bit sloppy, returning the row for only one token as follows:
    * "new hampshire", "new jersey", "new mexico", "new york": return row for the second token
    * "north carolina", "north dakota", "south carolina", "south dakota": return row for the second token
	* "rhode island": return row for the first token
	
###Testing
A simple Ruby unit test is provide in `tc_sanity.rb`.  If you type 

  % ruby test/tc_sanity.rb

it runs your `doit.sh` script against a subset of about 75 emails taken from [Berkeley's Enron Analysis website](http://bailando.sims.berkeley.edu/enron_email.html) and compares your output to what the solution produced.

Our grading script will compare your code against the approximately [1700 emails](http://bailando.sims.berkeley.edu/enron/enron_with_categories.tar.gz) provided on that website.  To test against that data, you type:

  % ruby tc_grading.rb
  
###Notes

* If you lose the original example csv output files, you can always recreate the original copies via `git checkout <foo>.csv` or by looking on the website at [https://github.com/cs186/sp12](https://github.com/cs186/sp12).