qsub-slice
==========

Submit tasks via SGE with a subset of lines from the INPUT_FILE for each one.

Examples:
---------
     # Submit slices of 100 lines per task to run the script build_database
     qsub-slice -l 100 original_inputs.txt build_database
     
     # Run the same as above but run only 10 tasks at any given time
     qsub-slice -l 100 -tc 10 original_inputs.txt build_database
     
     # Run the original task but source ~/build_env.sh before each task is run
     qsub-slice -l 100 -s ~/build_env.sh original_inputs.txt build_database
     
     # Run the original task and run coalese_database once all tasks have finished
     qsub-slice -l 100 --map-script=build_database --reduce_script=coalese_database original_inputs.txt
	    
Submission Options:
------------------
    -h|--help - Print this help message and quit
    --no-submit - Perform all setup but actual skip submission
    --clean [--force] - Remove all created directories and quit,
    No confirmation will be given if --force is specified Directories to delete:
    ./inputs/
    ./outputs/
    ./logs/
    ./merged/

## Internal Implementation Controls: ##
    -S|--shell - Shell variant to use. Can be path or (bash|sh|tcsh|csh).
    Only bash/sh currently implemented.
    --map-instance-script - Override the wrapper for running a map task
    --reduce-instance-script - Override the wrapper for running a map task
	
## Task Instance Scripts: ##
    -I|--iterate - Iterate over task slice and call with values instead of the the whole file
    -s|--setup-script - A BASH script to be sourced a the beginning of each task
    -m|--map-script - The actual script to run from within each map task
    -r|--reduce-script - The actual script to run from within the reduce job
	
## Input Control: ## 
    -d|--job-directory - Where to store the inputs, outputs, and logs for the job [default: pwd]
    -i|--input-file - The input file to slice up
    -l|--lines-per-task - The number of lines to be handled by each task

## Queue Control: ## 
    -a|--qsub-args - A *quoted string* of arguments to be passed to qsub
    -q|--queue - The name of the queue to submit to
    -N|--name - The job name to submit with
    -M|-tc|--max-running-tasks - The maximum number of tasks to run at a time for the job
    -R|--dont-read-qsub-args - Skip extracting additional queing arguments from the submission script

Positional Argument Parsing
--------------------------
* The first argument will be treated as the `INPUT_FILE`, unless the` -i|--input-file` option is used.
* The remaining arguments will be treated as the map script and arguments  unless the `-m|--map-script` argument was provided.


Notes
-----
 * The terms map and reduce are used loosely here. Any similarities to map-reduce 
 * The reduce portions are (yet) run in a recursive manner. Only one reduce job will be executed.
 * Some care should be taken with respect to how many input files (and output files) are created. Generating more than       1000 tasks can put load on the filesystem. A future version will support dynamic extraction of input slices.
