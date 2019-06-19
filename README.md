# neon-data-tools

---

## fan's version to get available neon raw sequences 

1. browse or search for needed sequences here `https://data.neonscience.org/static/browse.html` (eg., 16S) and get the product ID. 

2. run [R script](https://github.com/fandemonium/R_code/blob/master/rscripts/get_neon_csv_w_rawseq_urls.R) with a product ID as the input to get the CSV tables containing all raw sequence urls. ID could be looped.   
   ```
   Rscript /PATH/TO/FAN'S/get_neon_csv_w_rawseq_urls.R "DP1.20086.001" 
   # remove empty files:  
   find . -name "*.sh" -size -40k -delete
   ```
  
3. create a new dir to keep all csv tables with urls downloaded from step 2. 
   ```
   mkdir neon_csv && cd neon_csv
   bash ../*_curl_command.sh
   ```
   
4. combine all downloaded csv files to get urls into a list and generate another curl script. 
   ```
   cd neon_csv
   cat *.csv | sed '/uid/d' | rev | cut -d "," -f 4 | rev | cut -d '"' -f 2 > ../all_fq.url
   while read line; do echo "curl -O $line"; done < ../all_fq.url > ../get_all_fq.sh
   ```
   
5. Download 
   ```
   cd neon_csv 
   mkdir ../neon_fqs && cd ../neon_fqs
   bash ../get_all_fq.sh`  
   ```
   
6. untar into new dir names and get rid of all unneeded dir levels  
   ```
   cd neon_fqs
   for i in *.tar.gz; do mkdir ${i//.fastq.tar.gz//}; tar -zxvf $i -C ${i//.fastq.tar.gz//}; done
   # remove gz files
   rm *.gz
   for i in */; do cd /PATH/TO/$i; find . -type f -name "*.fastq" | tr '\n' '\0' | xargs -0 -I {} mv {} .; done
   for i in */; do rm -r $i/hpc; done
   ```
   
7. the fastq names can be linked to DNA sample ID, dates, and sites using the CSV table from step 3.  
   + neon field site to state information [here](https://github.com/germs-lab/neon-data-tools/blob/master/neon_field-sites.csv)

   
   
