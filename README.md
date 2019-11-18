# IRIDA Uploader Tutorial

## Uploading data to a project

#### Sign into IRIDA


#### Create a new IRIDA project


#### Download and install the IRIDA Uploader


#### Configure your Uploader

#### Download data set


#### Edit your SampleSheet.csv file in the dataset


#### Upload data


## Uploading non-Illumina data

#### File Structure
For simplicity, please keep the data you would like to upload all in one directory

#### Create a SampleList.csv file
Copy the `SampleList.csv` File from this tutorial into the directory where your data is. It will contain the following lines.

[SampleList.csv](../master/SampleList.csv)

```
[Data]
Sample_Name,Project_ID,File_Forward,File_Reverse
```

Add the following information to the `SampleList.csv` file for each sample you wish to upload.

* Sample_Name : This is what the sample will be identified as on IRIDA after upload.

* Project_ID: The IRIDA project the sample will be uploaded to.

* File_Forward: Always needed, the forward read file.

* File_Reverse: Needed for paired end reads, the reverse read file.

__Note__ : All samples in an upload must be either Paired or Single end reads. You __cannot__ mix single and paired end data in a single upload.

When uploading paired end reads, your file names must indicate forward/reverse.

Use the same name between files, with the difference of including `1` / `2`, `F` / `R`, `f` / `r`, as shown below.

__Example SampleList.csv__

```
[Data]
Sample_Name,Project_ID,File_Forward,File_Reverse
my-sample-1,75,file_1.fastq.gz,file_2.fastq.gz
my-sample-2,75,samp_F.fastq.gz,samp_R.fastq.gz
my-sample-3,76,germ_f.fastq.gz,germ_r.fastq.gz
```

Make sure you keep the CSV file format if edit the file with Microsoft Excel or other spreadsheet software.

![](images/save_as_csv.png)

#### Set IRIDA Uploader to Directory Parser
In the configuration screen of the IRIDA Uploader, change the parser field to `directory`

![](images/set_to_directory.png)

Now when opening the directory with your SampleList.csv file and data, you data will be shown in the Uploader.

![](images/directory_run.png)

#### More...
You can see more indepth information on uploading your own data in the Documention: https://irida-uploader.readthedocs.io/en/stable/parsers/directory.html

## Common Errors

#### Incorrectly formatted Sample Sheet

#### Missing or incorrectly named sample files
