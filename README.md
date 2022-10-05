# hse22_hw1
Зиновьев Павел, группа 2
# Обязательная часть задания
## Создание символических ссылок
``` 
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
``` 
## Выбор случайных чтений
``` 
seqtk sample -s 504 oil_R1.fastq 5000000 > paired-end-1.fastq
seqtk sample -s 504 oil_R2.fastq 5000000 > paired-end-2.fastq
seqtk sample -s 504 oilMP_S4_L001_R1_001.fastq 1500000 > mate-pairs-1.fastq
seqtk sample -s 504 oilMP_S4_L001_R2_001.fastq 1500000 > mate-pairs-2.fastq
``` 
## Оценка качества исходных чтений
``` 
mkdir fastqc_reports
fastqc -o fastqc_reports paired-end-1.fastq
fastqc -o fastqc_reports paired-end-2.fastq
fastqc -o fastqc_reports mate-pairs-1.fastq
fastqc -o fastqc_reports mate-pairs-2.fastq
mkdir multiqc_report
multiqc -o multiqc_report fastqc_reports
``` 
![Quality Scores](https://user-images.githubusercontent.com/87602071/194011151-ec55c2bd-3a1c-4fc8-a29b-b653ea7d2a41.png)
![Statistic](https://user-images.githubusercontent.com/87602071/194011180-da62bfb3-8dd6-49c3-9df2-f8a66d3ca042.png)
## Подрезание чтений по качеству и удаление адаптеров
``` 
platanus_trim paired-end-1.fastq paired-end-2.fastq
platanus_internal_trim mate-pairs-1.fastq mate-pairs-2.fastq
``` 
### Удаление fastq файлов
``` 
rm paired-end-1.fastq paired-end-2.fastq
rm mate-pairs-1.fastq mate-pairs-2.fastq
``` 
## Оценка качества подрезанных чтений
``` 
mkdir fastqc_trimmed_reports
fastqc -o fastqc_trimmed_reports paired-end-1.fastq.trimmed
fastqc -o fastqc_trimmed_reports paired-end-2.fastq.trimmed
fastqc -o fastqc_trimmed_reports mate-pairs-1.fastq.int_trimmed
fastqc -o fastqc_trimmed_reports mate-pairs-2.fastq.int_trimmed
mkdir multiqc_trimmed_report
multiqc -o multiqc_trimmed_report fastqc_trimmed_reports
``` 
![Quality Scores Trimmed](https://user-images.githubusercontent.com/87602071/194014569-633460a7-34a2-4c5e-8dbb-80d0f27d73c4.png)
![Statistic Trimmed](https://user-images.githubusercontent.com/87602071/194014521-dd4492ad-e0a7-41aa-a67d-729cefbab51d.png)
## Собирание контигов из подрезанных чтений
``` 
platanus assemble -o Bacteria -f paired-end-1.fastq.trimmed paired-end-2.fastq.trimmed 2> assemble.log
``` 
``` 
Общее количество:  623
Общая длина:  3926270
Самая большая длина:  130338
N50:  52802
``` 
## Собирание скаффолдов из контигов и подрезанных чтений
``` 
platanus scaffold -o Bacteria -c Bacteria_contig.fa -IP1 paired-end-1.fastq.trimmed paired-end-2.fastq.trimmed -OP2 mate-pairs-1.fastq.int_trimmed mate-pairs-2.fastq.int_trimmed 2> scaffold.log
``` 
``` 
Общее количество:  75
Общая длина:  3876169
Самая большая длина:  3834738
N50:  3834738
Количество гэпов:  65
Общая длина гэпов:  6285
``` 
## Уменьшение числа гэпов с помощью подрезанных чтений 
``` 
platanus gap_close -o Bacteria -c Bacteria_scaffold.fa -IP1 paired-end-1.fastq.trimmed paired-end-2.fastq.trimmed -OP2 mate-pairs-1.fastq.int_trimmed mate-pairs-2.fastq.int_trimmed 2> gapclose.log
``` 
``` 
Количество гэпов:  8
Общая длина гэпов:  1423
```
### Удаление fastq файлов
```
rm paired-end-1.fastq.trimmed paired-end-2.fastq.trimmed
rm mate-pairs-1.fastq.int_trimmed mate-pairs-2.fastq.int_trimmed
```
