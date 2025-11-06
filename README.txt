___________________________________________Flye assembly___________________________________________________________



Flye assembly for Nanopore data —— replace <file_name> with appropriate content 

#copy raw data  into working dir, always copy the original data, do not move
cp /path/to/raw/data path/to/working/dir

#cd to the working_dir 
cd /scratch3/nep003/<working_dir>

#unzip the zipped files 
gunzip *.fastq.gz

#combine the unzipped files into one file and name it after “>” command 

cat *.fastq > <file_name>.fastq


__________________________________________Filtlong__________________________________________________________

#filter long reads, usually already done by nanopore — to use filtlong - cd to respective folder and load required modules and dependencies 
module load filtlong 


#decide target based based on the predicted genome size and coverage required, if you require 100x coverage for 100kb genome, then target_bases will be 100 x 100kb = 10,000,000 bases 

#assuming phage gemone to be of 300kb the --target_bases must be 300kb x 100 = 30,000,000 (30 mil) 

./ means this dir and only have to give the file name in that dir | otherwise need to have path to file dir. 


filtlong --min_length 1000 --keep_percent 90 --target_bases 30000000 ./file_name.fastq > ./*.fastq

note: when both --keep_percent and --target-bases given, the most stringent one will be used. 



#load module and define input and output dir, use specific nane in output if you intend to run multiple iterations

module load flye/2.9.3

flye --nano-hq /scratch3/nep003/nanopore_Dec24_raw_combined/val_phage1/val1_filt.fastq --out-dir /scratch3/nep003/nanopore_Dec24_raw_combined/val_phage1/50x --asm-coverage 50 --genome-size 200k --threads ${SLURM_NTASKS}

flye --nano-hq /scratch3/nep003/nanopore_Dec24_raw_combined/val_phage2/val2.fastq --out-dir /scratch3/nep003/nanopore_Dec24_raw_combined/val_phage2/50x --asm-coverage 50 --genome-size 100k --threads ${SLURM_NTASKS}

flye --nano-hq /scratch3/nep003/nanopore_Dec24_raw_combined/val_phage3/val3.fastq --out-dir /scratch3/nep003/nanopore_Dec24_raw_combined/val_phage3/50x --asm-coverage 50 --genome-size 200k --threads ${SLURM_NTASKS}

flye --nano-hq /scratch3/nep003/nanopore_Dec24_raw_combined/val_phage4/val4.fastq --out-dir /scratch3/nep003/nanopore_Dec24_raw_combined/val_phage4/50x --asm-coverage 50 --genome-size 300k --threads ${SLURM_NTASKS}


Note: when you are in specific folder, not necessary to define path and output can be specified with ./<file_name> which will create a dir into the current dir. 

flye --nano-hq ./<file_name>.fastq --out-dir ./x100 --asm-coverage 100 --genome-size 5.5m --threads ${SLURM_NTASKS}




#FOR PACBIO DATA 

module load samtools 
samtools fastq V260R.bam > VA260R.fastq

flye --pacbio-hifi ./VA260.fastq --out-dir ./x100 --asm-coverage 100 --genome-size 5.5m --threads ${SLURM_NTASKS}


_______________________________________________________Check V _______________________________________________

#load required modules
module load checkv/1.0.3
m
#checkv requires a db, so specify the database or else you have to define db with —-d /path/to/db 

#to add db to your path 

export CHECKVDB=/datasets/work/af-aquaphage/work/database/checkv-db-v1.5/

#always run checkv from scratch or Bowen as it compiles conda/Diamond envs files into your home dir, and may cause issues with quota 

# go to scratch3/path/to/fasta/data, run checkv 

export CHECKVDB=/datasets/work/af-aquaphage/work/database/checkv-db-v1.5/
checkv end_to_end ./file_name  ./checkv


_________________________________________________dnaapler__________________________________________________________________________

module load dnaapler 

cd to final .fasta file path /scratch3/nep003/nanoDec24_8phages/final_assemblies/final
cd 
dnaapler phage -i ./val1_final.fasta -o ./val1_dnappler -f -p val1_dnaapler -t 8
dnaapler phage -i ./val2_final.fasta -o ./val2_dnappler -f -p val2_dnaapler -t 8
dnaapler phage -i ./val3_final.fasta -o ./val3_dnappler -f -p val3_dnaapler -t 8
dnaapler phage -i ./val4_final.fasta -o ./val4_dnappler -f -p val4_dnaapler -t 8
dnaapler phage -i ./val5_final.fasta -o ./val5_dnappler -f -p val5_dnaapler -t 8
dnaapler phage -i ./val6_final.fasta -o ./val6_dnappler -f -p val6_dnaapler -t 8
dnaapler phage -i ./val7_final.fasta -o ./val7_dnappler -f -p val7_dnaapler -t 8
dnaapler phage -i ./val8_final.fasta -o ./val8_dnappler -f -p val8_dnaapler -t 8


_________________________________________________PHAROKKA on HPC__________________________________________________________________________

module load pharokka/1.7.5-py312 

pharokka.py -i ./val1_final.fasta -o ./val1 -f -d /datasets/work/af-aquaphage/work/database/pharokka-db -p val1 -t ${SLURM_NTASKS}

-i input_path
-o output_path
-f force 
-d phrokka_database_path (dir where pharokka db is located) - must be specified 
-p prefix, must be same as the code that will later be used for circilize. if changed, there will be error.  
-t threads 



pharokka_plotter.py -i input.fasta -n pharokka_plot -o pharokka_output_directory 

harokka_plotter.py -i val7_dnaapler_reoriented.fasta -p ./val7_pharokka -n val7_pharokka_plot -o val7_pharokka/



pharokka_plotter.py -i val1_final.fasta -p ./val1 -n pharokka_plot_val1  -o ./val1

-i input fasta file location 
- prefix, should be same as the one that was used in pharokka.py or the file nane in the pharokka output dir 


 pharokka.py -i ./val1_dnaapler_reoriented.fasta -o ./val1_pharokka -p val1_pharokka -d /datasets/work/af-aquaphage/work/database/pharokka-db -t ${SLURM_NTASKS}

 pharokka.py -i ./val2_dnaapler_reoriented.fasta -o ./val2_pharokka -p val2_pharokka -d /datasets/work/af-aquaphage/work/database/pharokka-db -t ${SLURM_NTASKS}

 pharokka.py -i ./val3_dnaapler_reoriented.fasta -o ./val3_pharokka -p val3_pharokka -d /datasets/work/af-aquaphage/work/database/pharokka-db -t ${SLURM_NTASKS}

 pharokka.py -i ./val4_dnaapler_reoriented.fasta -o ./val4_pharokka -p val4_pharokka -d /datasets/work/af-aquaphage/work/database/pharokka-db -t ${SLURM_NTASKS}

 pharokka.py -i ./val5_dnaapler_reoriented.fasta -o ./val5_pharokka -p val5_pharokka -d /datasets/work/af-aquaphage/work/database/pharokka-db -t 64

 pharokka.py -i ./val6_dnaapler_reoriented.fasta -o ./val6_pharokka -p val6_pharokka -d /datasets/work/af-aquaphage/work/database/pharokka-db -t 64

 pharokka.py -i ./val7_dnaapler_reoriented.fasta -o ./val7_pharokka -p val7_pharokka -d /datasets/work/af-aquaphage/work/database/pharokka-db -t ${SLURM_NTASKS}

 pharokka.py -i ./val8_dnaapler_reoriented.fasta -o ./val8_pharokka -p val8_pharokka -d /datasets/work/af-aquaphage/work/database/pharokka-db -t ${SLURM_NTASKS}



_________________________________________________PHOLD on HPC__________________________________________________________________________

phold run -i /scratch3/nep003/nanoDec24_8phages/final_assemblies/final_dnaapler/val1_pharokka/val1_pharokka.gbk  -o val1_phold -f -t ${SLURM_NTASKS}


module load phold 

phold run -i /scratch3/nep003/nanoDec24_8phages/final_assemblies/final_pharokka_gbk/val1_pharokka.gbk  -o ./val1_phold -p val1_phold -t ${SLURM_NTASKS}
phold run -i /scratch3/nep003/nanoDec24_8phages/final_assemblies/final_pharokka_gbk/val2_pharokka.gbk  -o ./val2_phold -p val2_phold -t ${SLURM_NTASKS}
phold run -i /scratch3/nep003/nanoDec24_8phages/final_assemblies/final_pharokka_gbk/val3_pharokka.gbk  -o ./val3_phold -p val3_phold -t ${SLURM_NTASKS}
phold run -i /scratch3/nep003/nanoDec24_8phages/final_assemblies/final_pharokka_gbk/val4_pharokka.gbk  -o ./val4_phold -p val4_phold -t ${SLURM_NTASKS}
phold run -i /scratch3/nep003/nanoDec24_8phages/final_assemblies/final_pharokka_gbk/val5_pharokka.gbk  -o ./val5_phold -p val5_phold -t ${SLURM_NTASKS}
phold run -i /scratch3/nep003/nanoDec24_8phages/final_assemblies/final_pharokka_gbk/val6_pharokka.gbk  -o ./val6_phold -p val6_phold -t ${SLURM_NTASKS}
phold run -i /scratch3/nep003/nanoDec24_8phages/final_assemblies/final_pharokka_gbk/val7_pharokka.gbk  -o ./val7_phold -p val7_phold -t ${SLURM_NTASKS}
phold run -i /scratch3/nep003/nanoDec24_8phages/final_assemblies/final_pharokka_gbk/val8_pharokka.gbk  -o ./val8_phold -p val8_phold -t ${SLURM_NTASKS}


phold run -i /scratch3/nep003/nanoDec24_8phages/05_pharokka_gbk_phold/val6_pharokka.gbk  -o /scratch3/nep003/nanoDec24_8phages/05_pharokka_gbk_phold/val6_phold  -p val6_phold -t 64
