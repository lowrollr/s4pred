![S4PRED Logo](./images/s4pred.png)
 
# S4PRED

A tool for accurate prediction of a protein's secondary structure from only it's amino acid sequence. 

This repo contains the fully trained inference model built in Pytorch. Its a simple one line command to run it on a fasta file containing a protein sequence. S4PRED is a product of our paper [A Deep Semi-Supervised Framework for Accurate Modelling of Orphan Sequences](https://www.biorxiv.org/content/10.1101/2020.07.13.201459v2).

We also provide the 1.08M example pseudo-labelled training set used for training S4PRED. 

S4PRED is a state-of-the-art single-sequence model meaning it doesn't use homology information to make predictions, only the primary amino acid sequence. 



# Requirements

- Python 3.7 or greater
- Pytorch 1.5.1 

The script hasn't been tested on newer Pytorch builds but it should run without any issues. 

# Installation

Clone the directory
```bash
git clone https://github.com/psipred/s4pred
cd s4pred
```
Make the `weights/` directory and `cd` into it, then download the model weights from our public server.
```bash
mkdir weights
cd weights
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/s4pred/weights_1.pt
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/s4pred/weights_2.pt
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/s4pred/weights_3.pt
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/s4pred/weights_4.pt
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/s4pred/weights_5.pt
``` 
Each weight file is ~86MB and so all together make up roughly 430MB. If you have python and Pytorch installed you should be ready to go. 

# Usage

The S4PRED model can be used to predict a sequence's secondary structure with the following:
```bash
python run_model.py YOUR_FASTA.fas > YOUR_OUTPUT.ss2
```
The results of the prediction are piped to `stdout` and prediction should take less than a second. 
There are two optional arguments you can give:

- `--device`
    - This can be either `cpu` or `gpu`. 
This specifies if you want to run the model on the GPU or the CPU. By default it uses the CPU and in the vast majority of cases this will be sufficient. Also, the model should use less than 1GB of system memory. 

- `--outfmt`
    - This can be either `ss2` or `fas`. 
This specifies which output format to use. `ss2` is the default and it corresponds to the PSIPRED vertical format (PSIPRED VFORMAT). Here is an example of what it looks like:
```
# PSIPRED VFORMAT (S4PRED V1.0)

   1 M C   0.997  0.000  0.002
   2 G C   0.984  0.000  0.016
   3 D C   0.812  0.001  0.187
   ...
```
A full example output of this file is located in `examples/1qys.ss2`.

The alternative `fas` output returns the sequence FASTA file with the predicted secondary structure concatenated on a second line. This is similar to the FASTA flat-file that the RCSB PDB provides for all sequences and their DSSP based secondary structure (Downloaded from [here](https://cdn.rcsb.org/etl/kabschSander/ss.txt.gz)). Here is an example:
```
>1QYS_1|Chain A|TOP7|Computationally Designed Sequence
MGDIQVQVNIDDNGKNFDYTYTVTTESELQKVLNELMDYIKKQGAKRVRISITARTKKEAEKFAAILIKVFAELGYNDINVTFDGDTVTVEGQL
CCCEEEEEEECCCCCEEEEEEEECCHHHHHHHHHHHHHHHHHCCCCEEEEEEEECCHHHHHHHHHHHHHHHHHCCCCEEEEEEECCEEEEEEEC
```
The above example output of this file is located in `examples/1qys_ss.fas`.

## Example Run
The following is an example run on the sequence of TOP7 (PDB ID: 1QYS) using the GPU and output to the FASTA like format. The corresponding fasta input file is located in `examples/1qys.fas` (this is the PDB FASTA file stripped of the 6-HIS tag on the C-Terminus). 
```bash
python run_model.py --device gpu --outfmt fas example/1qys.fas > 1qys_ss.fas
```


# Dataset
We have made the pseudo-labelled training set available to download from our public server. These are in a simple FASTA flat file `pseudo-labelled training set`.
```bash
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/s4pred/s4pred_train.fas
```
There are 1080886 examples in the set and the contents of the flat file look like this:
```
>A0A1J5K7I4
MYVCVCNGITEEMLDTAQKQGLSDREILNRLGVGNSCGVCVIDALDNMRSNSLKSQKTSNRKDSKKS
CEEEECCCCCHHHHHHHHHCCCCHHHHHHHHCCCCCCCHHHHHHHHHHHHHHHHHHHHHCCCCCCCC
>A0A2S4ZHT0
MRPDLMGPGFVQVLARTTLVSRHRPRQQLPSGTVRHAMVQVTERFFSHGPHRPAPGFTPMVRQRYCCA
CCCCCCCCCHHHHHEECEECCCCCCHHCCCCHHHHHEEEEEEHHHHHCCCCCCCCCCCHHHCCCCCCC
>A0A076ETI5
MGALSPSHWAIIAVVLVVLFGSKKLPDAARGLGRSMRILKTEVGELQADAPELEK
CCCCCHHHHHHHHHHHHHHHCCCCCHHHHHHHHHHHHHHHHHHHHHHCCCHHHCC
...
```
The examples go label, then sequence on a new line, and then the 3 class predicted secondary structure on a new line.
This doesn't adhere to the old 80 char line limit of FASTA files so it's easier to parse. 
If you'd like a quick parser script raise an issue or submit a pull request. 
The label is the Uniprot ID of the representative sequence of the Uniclust30 cluster that the example came from.

Importantly, this training set has had several different filters applied (see our paper) to remove homology from the CB513 test set.
This makes the dataset ideal for training not just secondary structure predictors but also unsupervised sequence models. 
In both cases, using this training set with CB513 as a test set provides a strong test of generalization.     

# Citation

If you use S4PRED in your work please cite the following bioRxiv pre-print: 

**A Deep Semi-Supervised Framework for Accurate Modelling of Orphan Sequences**</br>
Lewis Moffat and David T. Jones;
bioRxiv, 2020-11-02,
DOI:10.1101/2020.07.13.201459 
[LINK](https://www.biorxiv.org/content/10.1101/2020.07.13.201459v2)

Here is the corresponding BibTex 
```bibtex
@article {Moffat2020.07.13.201459,
	author = {Moffat, Lewis and Jones, David T.},
	title = {A Deep Semi-Supervised Framework for Accurate Modelling of Orphan Sequences},
	elocation-id = {2020.07.13.201459},
	year = {2020},
	doi = {10.1101/2020.07.13.201459},
}
```
This will be updated to point to the published article when it is released. 



