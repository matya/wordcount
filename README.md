# wordcount

Counting words in different programming languages.

# Leaderboard

Updated: 26-11-2015

| Rank | Experiment | CPU seconds | User time | Maximum memory |
| :---: | :---: | :---: | :---: | :---: |
| 1 | cpp/wc_vector | 7.14 | 5.32 | 126268 |
| 2 | cpp/wc_hash_nosync | 8.04 | 6.07 | 163404 |
| 3 | python/wordcount_py2.py | 8.68 | 8.48 | 280616 |
| 4 | cpp/wc_baseline_hash | 10.18 | 8.14 | 171656 |
| 5 | java -classpath java WordCount | 13.56 | 15.66 | 404064 |
| 6 | java -classpath java WordCountEntries | 13.8 | 15.77 | 398768 |
| 7 | cpp/wc_baseline | 13.9 | 12.0 | 178084 |
| 8 | python/wordcount_py3.py | 14.14 | 13.86 | 245164 |
| 9 | php php/wordcount.php | 15.16 | 13.15 | 396516 |
| 10 | perl/wordcount.pl | 17.04 | 16.79 | 225352 |
| 11 | nodejs javascript/wordcount.js | 27.9 | 24.93 | 577472 |
| 12 | bash/wordcount.sh | 34.92 | 40.51 | 10768 |


# Format

All programs should read from STDIN and write to STDOUT. The input is always encoded in UTF-8.
The output should contain lines like this:

    freqword <tab> freq

## Example

    $ echo "apple pear apple art" | python2 python/wordcount.py
    apple   2
    art     1
    pear    1

## Test corpus: Hungarian Wikisource

`scripts/create_input.sh` downloads the latest Hungarian Wikisource XML dump.
Why Wikisource? It's not too small not too large and more importantly, it's valid utf8.
Why Hungarian? There are many non-ascii characters and the number of different word types is high.


### Usage

To test on a small sample:

    time cat data/huwikisource-latest-pages-meta-current.xml | head -10000 | python3 python/wordcount_py3.py > python_out

# Using the provided scripts

## Installation

There are two ways to install all the dependencies:

1. Build a Docker image with the provided Dockerfile, which installs all the required packages.
2. Install them manually via a package manager. The Docker image is an Ubuntu image but the same packages work for me on Manjaro Linux as well. Use this command on Ubuntu to install all dependencies, but be prepared for a lot of new packages. You've been warned.
    
    sudo apt-get install wget gcc python npm perl php5 git default-jdk time


## Docker image

You can run the experiment in a Docker container. The Dockerfile is provided, run:

    docker build -t wordcount --rm .

This might take a while.

Load the image into a container:

    docker run -it wordcount bash

You should see the cloned directory in `/root`

    cd wordcount


## Downloading the dataset

    bash scripts/create_input.sh

## Compile/build/whatever the wordcount scripts

    bash scripts/build.sh

## Run tests on one language

`scripts/test.sh` runs all tests for one language, well actually for a single command.

    bash scripts/test.sh "python2 python/wordcount_py2.py"

Or

    bash scripts/test.sh python/wordcount_py2.py

if the file is executable and has a valid shebang line.

The script either prints OK or the list of failed tests and a final FAIL.

## Run tests on all languages

All commands are listed in the file `run_commands.txt` and the script `scripts/test_all.sh` runs test.sh with each command:

    bash scripts/test_all.sh

## Run the actual experiment on a larger dataset

If all tests are passed, the scripts work reasonably well. This does not mean that all output will be the same, see the full test later.
For now, we consider them good enough for testing.

This command will run each test twice and save the results to results.txt.

    bash scripts/compare.sh data/huwikisource-latest-pages-meta-current.xml 2

Or test it on a part of huwikisource:

    bash scripts/compare.sh <( head -10000 data/huwikisource-latest-pages-meta-current.xml) 1

Results.txt in a tab separated file that can be formatted to a Markdown table with this command:

    cat results.txt | python2 scripts/evaluate_results.py

This scripts prints the fastest run for each command in a markup table like this:

| Experiment | CPU seconds | User time | Maximum memory |
| --- | --- | --- | --- |
| cpp/wc_vector | 2.68 | 2.37 | 32168 |
| python/wordcount_py2.py | 2.68 | 2.61 | 71512 |
| bash/wordcount.sh | 3.0 | 4.19 | 10820 |

## Adding a new program

Adding a new programming language or a new version for an existing programming language consists of three steps:

1. add dependencies to the Dockerfile. Basically add the package to the existing apt-get package list.
2. if it needs compiling or any other setup method, add it to `scripts/build.sh`
3. add the actual invoke command to `run_commands.txt`

### Adding your program to this experiment

1. Make sure all dependencies are installed via standard packages and your code compiles.
1. Pass at least the first 3 tests successfully.
1. Make sure it runs for less than two minutes for 100,000 lines of text. If it is slower, it doesn't make much sense to add it.

# Old notes for manual building and running

## Javascript

Nodejs and npm are needed.
Install dependencies:

    cd javascript
    npm install

Run:

    node index.js


## BASH

Set the LC\_COLLATE variable to C to consider non-alphanumeric characters when sorting:

    export LC_COLLATE=C

Run:

    time zcat de.gz | bash wordcount.sh > bash_out


## Java

Usage:

    javac WordCount.java
    time cat de | java WordCount > wc.java

The JVM startup can be measured by e.g.

    time echo "Hello" | java WordCount

# TODO

* compare full output on each language
  * which one should be the oraculum?
