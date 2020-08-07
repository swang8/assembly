# Assembly tutorial
HPRC short course serial

Shichen Wang


## Setup
```
echo $SCRATCH
cd $SCRATCH
mkdir -p NGS_assembly/Long_Reads/PacBio_Assembly
mkdir -p NGS_assembly/Long_Reads/PacBio_Data
cd NGS_assembly/Long_Reads/PacBio_Data
```

### Get the data
```
curl -L -o pacbio.fastq http://gembox.cbcb.umd.edu/mhap/raw/ecoli_p6_25x.filtered.fastq

head -n 4 pacbio.fastq | more
```

## Prepare for submitting the assembly job

Wiki page for running Canu on TAMU HPRC Ada System:
[https://hprc.tamu.edu/wiki/Ada:NGS:Genome_Assembly#Canu](https://hprc.tamu.edu/wiki/Ada:NGS:Genome_Assembly#Canu)


### Check available Canu
```
module spider Canu
```

### `bsub` file
```
# switch to PacBio_Assembly folder
cd $SCRATCH/NGS_assembly/Long_Reads/PacBio_Assembly

# creat a text file
nano run_canu.bsub
```

Copy and paste the following to the file `run_canu.bsub`, then save it `Ctrl-x`:
<pre>
#BSUB -L /bin/bash
#BSUB -J canu_run
#BSUB -o stdout.%J
#BSUB -e stderr.%J
#BSUB -n 20
#BSUB -R "span[ptile=20]"
#BSUB -R "rusage[mem=2000]"
#BSUB -W 4:00

module load Westmere
module load Canu/1.7-intel-2017A-Perl-5.24.0

out_dir=$SCRATCH/NGS_assembly/Long_Reads/PacBio_Assembly
input_dir=$SCRATCH/NGS_assembly/Long_Reads/PacBio_Data

date
echo "start"

canu -p Ecoli -d $out_dir genomeSize=4.8m -pacbio-raw $input_dir/pacbio.fastq useGrid=false

date
echo "finished!"
</pre>

### submit the job
```
bsub <run_canu.bsub
```
