
run_dbcan3
========================

Status
----
[![dbcan](https://github.com/linnabrown/run_dbcan/actions/workflows/ci.yml/badge.svg?branch=master)](https://github.com/linnabrown/run_dbcan/actions/workflows/ci.yml)
[![Package status](https://img.shields.io/pypi/status/dbcan.svg)](https://pypi.org/project/dbcan/#files)
[![GitHub license](https://img.shields.io/badge/license-GUN3.0-blue.svg)](https://github.com/linnabrown/run_dbcan/blob/master/LICENSE)
[![GitHub downloads](https://img.shields.io/pypi/dm/run-dbcan.svg)](https://pypi.org/project/dbcan/#files)
[![GitHub versions](https://img.shields.io/pypi/pyversions/dbcan.svg)](https://pypi.org/project/dbcan/#files)
[![Package version](https://img.shields.io/pypi/v/dbcan.svg)](https://pypi.org/project/dbcan/#files)

A standalone tool of http://bcb.unl.edu/dbCAN2/

Rewritten by Huang Le in the Zhang Lab at NKU; V1 version was written by Tanner Yohe of the Yin lab at NIU.

Update Info
---
1. 3.0.7 Fix the bug in cgc_parser.py.
2. hmmdb, cazydb, tf-1, tf-2, stp and tcdb are updated. **Please update all of the databases**.

[Previous update information](https://github.com/linnabrown/run_dbcan/wiki/Update-information-Archive)

Function
----
- Accepts user input
- Predicts genes if needed
- Runs input against HMMER, DIAMOND, and eCAMI
- Optionally predicts CGCs with CGCFinder

Support Platform
-----
Linux(Ubuntu, CentOS), MacOS

Installation via Bioconda
-----
1. Please install [Anoconda](https://www.anaconda.com) first.

2. Create virtual environment with dbcan and activate the virtual environment.

```
conda create -n run_dbcan python=3.8 dbcan -c conda-forge -c bioconda
conda activate run_dbcan
```

3. Database Installation.
```
test -d db || mkdir db
cd db \
    && wget http://bcb.unl.edu/dbCAN2/download/Databases/V11/CAZyDB.08062022.fa && diamond makedb --in CAZyDB.08062022.fa -d CAZy \
    && wget https://bcb.unl.edu/dbCAN2/download/Databases/V11/dbCAN-HMMdb-V11.txt && mv dbCAN-HMMdb-V11.txt dbCAN.txt && hmmpress dbCAN.txt \
    && wget https://bcb.unl.edu/dbCAN2/download/Databases/V11/tcdb.fa && diamond makedb --in tcdb.fa -d tcdb \
    && wget http://bcb.unl.edu/dbCAN2/download/Databases/V11/tf-1.hmm && hmmpress tf-1.hmm \
    && wget http://bcb.unl.edu/dbCAN2/download/Databases/V11/tf-2.hmm && hmmpress tf-2.hmm \
    && wget https://bcb.unl.edu/dbCAN2/download/Databases/V11/stp.hmm && hmmpress stp.hmm \
    && cd ../ && wget http://bcb.unl.edu/dbCAN2/download/Samples/EscheriaColiK12MG1655.fna \
    && wget http://bcb.unl.edu/dbCAN2/download/Samples/EscheriaColiK12MG1655.faa \
    && wget http://bcb.unl.edu/dbCAN2/download/Samples/EscheriaColiK12MG1655.gff
```
4. (Optional) SignalP Installation.
Our program include Signalp Petitide prediction with SignalP. Make sure to set `use_signalP=True` and *have to* obtain your own academic license of SignalP and download it from [here](https://services.healthtech.dtu.dk/service.php?SignalP-4.1), and then move the perl file from the tarball file (signalp-4.1g.Linux.tar.gz) into `/usr/bin/signalp` by yourself. Following statement is singalP-4.1 installation instruction.

Decompress signalp-4.1g.Linux.tar.gz than open the directory

```
tar -xvf signalp-4.1g.Linux.tar.gz && cd signalp-4.1
```

Then you can find those files/directories located in `signalp-4.1` directory
```
(base) lehuang@lehuang:~/Downloads/signalp-4.1$ ls
bin  lib  signalp  signalp.1  signalp-4.1.readme  syn  test
```

*signalp* is the perl file that you will use in your program
Edit the paragraph labeled  "GENERAL SETTINGS, CUSTOMIZE ..." in the top of
   the file 'signalp'. The following twovmandatory variables need to be set:

   	**SIGNALP**		full path to the signalp-4.1 directory on your system
	**outputDir**	where to store temporary files (writable to all users)
	**MAX_ALLOWED_ENTRIES** the number of input sequences allowed per run.
```
Here is the example for me to change line 13, line 17 and line 20 in `singalp` file. I suggest you to set MAX_ALLOWED_ENTRIES as 100000
###############################################################################
#               GENERAL SETTINGS: CUSTOMIZE TO YOUR SITE
###############################################################################

# full path to the signalp-4.1 directory on your system (mandatory)
BEGIN {
    $ENV{SIGNALP} = '/home/lehuang/Downloads/signalp-4.1';
}

# determine where to store temporary files (must be writable to all users)
my $outputDir = "/home/lehuang/Downloads/signalp-4.1/output";

# max number of sequences per run (any number can be handled)
my $MAX_ALLOWED_ENTRIES=100000;
```

And then, use this command:

```
sudo cp signalp /usr/bin/signalp
sudo chmod 755 /usr/bin/signalp
```
If you don't have the permission to access `/usr/bin`, you can use the parameter `-sp` or `--signalP_path` to indicate your `signalp` file path in the run_dbcan program. Please see the step 6.
6. Check Program.
```
run_dbcan EscheriaColiK12MG1655.fna prok --out_dir output_EscheriaColiK12MG1655
```

If you want to run the code with SignalP
```
run_dbcan EscheriaColiK12MG1655.fna prok --out_dir output_EscheriaColiK12MG1655 --use_signalP=TRUE

```
If you don't have the permission to access `/usr/bin` when running with signalP, you can use the parameter `-sp` or `--signalP_path` to indicate your `signalp` file path in the run_dbcan program. 

```
run_dbcan EscheriaColiK12MG1655.fna prok --out_dir output_EscheriaColiK12MG1655 --use_signalP=TRUE -sp /home/lehuang/Downloads/signalp-4.1/signalp 
```
Installation via Docker
----
1. Make sure docker is installed on your computer successfully.
2. Docker pull image
```
docker pull haidyi/run_dbcan:latest
```
3. Run. Mount `input sequence file` and `output directory` to the container.
```
docker run --name <preferred_name> -v <host-path>:<container-path> -it haidyi/run_dbcan:latest run_dbcan <input_file> [params] --out_dir <output_dir>
```



REQUIREMENTS
----

**TOOLS**

----
P.S.: You do not need to download `CGCFinder` and `hmmscan-parser` because they are included in run_dbcan V2. If you use python package or docker, you don't need to download Prodigal because they includes these denpendencies. Otherwise we recommend you to install and copy them into `/usr/bin` as system application or add their path into system envrionmental profile.


[Python3]--Be sure to use python3, not python2

[DIAMOND](https://github.com/bbuchfink/diamond)-- Included in dbCAN2.

[HMMER](hmmer.org)

[hmmscan-parser](https://github.com/linnabrown/run_dbcan/blob/master/hmmscan-parser.py)--This is included in dbCAN2.

[eCAMI-Python](https://github.com/zhanglabNKU/eCAMI.git)--This newest version is included in eCAMI.

[signalp](http://www.cbs.dtu.dk/services/SignalP/)--please download and install if you need.

[Prodigal](https://github.com/hyattpd/Prodigal)--please download and install if you need.

[!we no longer use FragGeneScan to predict genes from meta genome, we use Prodigal instead][FragGeneScan](http://omics.informatics.indiana.edu/FragGeneScan/)--please download and install if you need.

[CGCFinder](https://github.com/linnabrown/run_dbcan/blob/master/CGCFinder.py)--This newest version is included in dbCAN2 project.

**DATABASES Installation**

----
[Databse](http://bcb.unl.edu/dbCAN2/download/Databases) -- Database Folder

[CAZy.fa](http://bcb.unl.edu/dbCAN2/download/Databases/CAZyDB.09242021.fa)--use `diamond makedb --in CAZyDB.09242021.fa -d CAZy`

[CAZyme]:included in eCAMI.

[EC]: included in eCAMI.

[dbCAN-HMMdb-V10.txt](http://bcb.unl.edu/dbCAN2/download/Databases/V10/dbCAN-HMMdb-V10.txt)--First use `mv dbCAN-HMMdb-V10.txt dbCAN.txt`, then use `hmmpress dbCAN.txt`

[tcdb.fa](http://bcb.unl.edu/dbCAN2/download/Databases/tcdb.fa)--use `diamond makedb --in tcdb.fa -d tcdb`

[tf-1.hmm](http://bcb.unl.edu/dbCAN2/download/Databases/tf-1.hmm)--use `hmmpress tf-1.hmm`

[tf-2.hmm](http://bcb.unl.edu/dbCAN2/download/Databases/tf-2.hmm)--use `hmmpress tf-2.hmm`

[stp.hmm](http://bcb.unl.edu/dbCAN2/download/Databases/stp.hmm)--use `hmmpress stp.hmm`


Params
----
[inputFile] - FASTA format file of either nucleotide or protein sequences

[inputType] - protein=proteome, prok=prokaryote, meta=metagenome/mRNA/CDSs/short DNA seqs

[--out_dir] - REQUIRED, user specifies an output directory.

[-c AuxillaryFile]- optional, include to enable CGCFinder. If using a proteome input,
the AuxillaryFile must be a GFF or BED format file containing gene positioning
information. Otherwise, the AuxillaryFile may be left blank.

[-t Tools] 		- optional, allows user to select a combination of tools to run. The options are any combination of 'diamond', 'hmmer', and 'eCAMI'. The default value is 'all' which runs all three tools.

[--dbCANFile]   - optional, allows user to set the file name of dbCAN HMM Database.

[--dia_eval]    - optional, allows user to set the DIAMOND E Value. Default = 1e-102.

[--dia_cpu]     - optional, allows user to set how many CPU cores DIAMOND can use. Default = 2.

[--hmm_eval]    - optional, allows user to set the HMMER E Value. Default = 1e-15.

[--hmm_cov]     - optional, allows user to set the HMMER Coverage value. Default = 0.35.

[--hmm_cpu]     - optional, allows user to set how many CPU cores HMMER can use. Default = 1.

[--eCAMI_kmer_db] -optional, allows user to set n_mer directories path for prediction. Default=Cazyme.

[--eCAMI_k_mer] -optional, allows user to set peptide length for prediction. Default=8.

[--eCAMI_jobs] -optional, number of jobs for prediction. Default=8.

[--eCAMI_important_k_mer_number] -optional, Minimum number of n_mer for prediction. Default=5.

[--eCAMI_beta] -optional, Minimum sum of percentage of frequency of n_mer for prediction. Default=2.

[--tf_eval]     - optional, allows user to set tf.hmm HMMER E Value. Default = 1e-4.

[--tf_cov]     - optional, allows user to set tf.hmm HMMER Coverage val. Default = 0.35.

[--tf_cpu]     - optional, allows user to tf.hmm Number of CPU cores that HMMER is allowed to use. Default = 1.

[--stp_eval]     - optional, allows user to set stp.hmm HMMER E Value. Default = 1e-4.

[--tf_cov]     - optional, allows user to set stp.hmm HMMER Coverage val. Default = 0.3.

[--tf_cpu]     - optional, allows user to stp.hmm Number of CPU cores that HMMER is allowed to use. Default = 1.

[--out_pre]     - optional, allows user to set a prefix for all output files.

[--db_dir]      - optional, allows user to specify a database directory. Default = db/

[--cgc_dis]     - optional, allows user to specify CGCFinder Distance value. Allowed values are integers between 0-10. Default = 2.

[--use_signalP] - optional, Use signalP or not, remember, you need to setup signalP tool first. Because of signalP license, python package does not have signalP. If your input is proteome/prokaryote nucleotide, please also certify the "--gram"(in the below). Default = False.

[use_signalP] - optional, Use signalP or not, remember, you need to setup signalP tool first. Because of signalP license, Docker version does not have signalP.
[signalP_path] - optional, The path for signalp. Default location is signalp

[--gram] - optional, Choose gram+(p) or gram-(n) for proteome/prokaryote nucleotide, which are params of SignalP, only if you use SignalP. Only you set use_signalP. The options are: "all"(gram positive + gram negative), "n"(gram negative), "p"(gram positive). Default = "all".



RUN & OUTPUT
----
Use following command to run the program.
```
run_dbcan [inputFile] [inputType] [-c AuxillaryFile] [-t Tools] etc.
```

Several files will be produced via `run_dbcan`. They are as follows:

	uniInput - The unified input file for the rest of the tools
			(created by prodigal if a nucleotide sequence was used)

	eCAMI.out - the output from the eCAMI run

	diamond.out - the output from the diamond blast

	hmmer.out - the output from the hmmer run

	tf.out - the output from the diamond blast predicting TF's for CGCFinder

	tc.out - the output from the diamond blast predicting TC's for CGCFinder

	cgc.gff - GFF input file for CGCFinder

	cgc.out - ouput from the CGCFinder run

	overview.txt - Details the CAZyme predictions across the three tools with signalp results

EXAMPLE
----

An example setup is available in the example directory. Included in this directory are two FASTA sequences (one protein, one nucleotide).

To run this example type, run:

```
run_dbcan EscheriaColiK12MG1655.fna prok --out_dir output_EscheriaColiK12MG1655
```

or

```
run_dbcan EscheriaColiK12MG1655.faa protein --out_dir output_EscheriaColiK12MG1655
```

While this example directory contains all the databases you will need (already formatted) and the eCAMI and Prodigal programs, you will still need to have the remaining programs installed on your machine (DIAMOND, HMMER, etc.).

To run the examples with CGCFinder turned on, run:
```
run_dbcan EscheriaColiK12MG1655.fna prok -c cluster --out_dir output_EscheriaColiK12MG1655
```

or

```
run_dbcan EscheriaColiK12MG1655.faa protein -c EscheriaColiK12MG1655.gff --out_dir output_EscheriaColiK12MG1655
```

Notice that the protein command has a GFF file following the -c option. A GFF or BED format file with gene position information is required to run CGCFinder when using a protein input.

If you have any questions, please feel free to contact with Dr. Yin (yanbin.yin@gmail.com or yyin@unl.edu) or me (Le Huang) on [Issue Dashboard](https://github.com/linnabrown/run_dbcan/issues).


Reference
----

This is the standalone version of dbCAN annotation tool for automated CAZyme annotation (known as run_dbCAN), written by Le Huang and Tanner Yohe.

If you want to use our dbCAN2 webserver, please go to http://bcb.unl.edu/dbCAN2/.

If you use dbCAN standalone tool or/and our web server for publication, please cite us:

*Han Zhang, Tanner Yohe, **Le Huang**, Sarah Entwistle, Peizhi Wu, Zhenglu Yang, Peter K Busk, Ying Xu, Yanbin Yin;
dbCAN2: a meta server for automated carbohydrate-active enzyme annotation, Nucleic Acids Research,
Volume 46, Issue W1, 2 July 2018, Pages W95–W101, https://doi.org/10.1093/nar/gky418*
```
@article{doi:10.1093/nar/gky418,
author = {Zhang, Han and Yohe, Tanner and Huang, Le and Entwistle, Sarah and Wu, Peizhi and Yang, Zhenglu and Busk, Peter K and Xu, Ying and Yin, Yanbin},
title = {dbCAN2: a meta server for automated carbohydrate-active enzyme annotation},
journal = {Nucleic Acids Research},
volume = {46},
number = {W1},
pages = {W95-W101},
year = {2018},
doi = {10.1093/nar/gky418},
URL = {http://dx.doi.org/10.1093/nar/gky418},
eprint = {/oup/backfile/content_public/journal/nar/46/w1/10.1093_nar_gky418/1/gky418.pdf}
}
```

If you want to use pre-computed bacterial CAZyme sequences/annotations directly, please go to http://bcb.unl.edu/dbCAN_seq/ and cite us:

**Le Huang**, Han Zhang, Peizhi Wu, Sarah Entwistle, Xueqiong Li, Tanner Yohe, Haidong Yi, Zhenglu Yang, Yanbin Yin;
dbCAN-seq: a database of carbohydrate-active enzyme (CAZyme) sequence and annotation, Nucleic Acids Research,
Volume 46, Issue D1, 4 January 2018, Pages D516–D521, https://doi.org/10.1093/nar/gkx894*
```
@article{doi:10.1093/nar/gkx894,
author = {Huang, Le and Zhang, Han and Wu, Peizhi and Entwistle, Sarah and Li, Xueqiong and Yohe, Tanner and Yi, Haidong and Yang, Zhenglu and Yin, Yanbin},
title = {dbCAN-seq: a database of carbohydrate-active enzyme (CAZyme) sequence and annotation},
journal = {Nucleic Acids Research},
volume = {46},
number = {D1},
pages = {D516-D521},
year = {2018},
doi = {10.1093/nar/gkx894},
URL = {http://dx.doi.org/10.1093/nar/gkx894},
eprint = {/oup/backfile/content_public/journal/nar/46/d1/10.1093_nar_gkx894/2/gkx894.pdf}
}
```



## Commit History

## Commit History

[![Commit History Chart](https://commit-history-api.herokuapp.com/svg?repos=linnabrown/run_dbcan&type=Date)](https://the-commit-history.vercel.app/#linnabrown/run_dbcan&Date)


