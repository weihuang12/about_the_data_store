## PROCEDURE FOR ADDING A NEW DATA SET TO THE LEGUME FEDERATION DATA STORE

NOTE: The instructions below are for curators working on any instance of
LegFed Data Store - at e.g. soybase.org, peanutbase.org, legumeinfo.org etc. 
If you are a researcher or user of and you have a data set that you would like
to contribute, PLEASE <a href="https://legumeinfo.org/contact">CONTACT US!</a>. 
We would love to work with you. You are welcome to use the templates in this 
directory and begin preparing your data for submission, but the final uploading
will need to be done by curators with the affiliated database projects.

### Upload the data to the local Data Store file system
The data store is accessible via command line from several servers.
As of winter, 2018, any of these servers can be used:
  - lis-stage.usda.iastate.edu 
  - soybase-stage.usda.iastate.edu 
  - legumefederation.usda.iastate.edu 
  - peanutbase-stage.usda.iastate.edu

Upload (scp) data to the private directory (and appropriate subdirectory) here:
  /usr/local/www/data/private/
  e.g.
  /usr/local/www/data/private/Glycine_max

### Name the directories and files
Apply directory names, following the patterns described in 
  https://legumeinfo.org/data/about_the_data_store/about_the_Data_Store.md
... and including a key from http://bit.ly/LegFed_registry (and make appropriate changes at the registry)
For example, for methylation data initially at methylation/kim_et_al_2015/ 
```
  mv Gmax2.0 Wm82.gnm2.met1.K8RT
```

Apply file names, following the patterns described in
  https://legumeinfo.org/data/about_the_data_store/about_the_Data_Store.md
  
```
  cd  Wm82.gnm2.met1.K8RT
  mv W82_L1_Gm275.ctable.gz   glyma.Wm82.gnm2.met1.K8RT.methylation_L1.txt.gz 
  mv W82_L2_Gm275.ctable.gz   glyma.Wm82.gnm2.met1.K8RT.methylation_L2.txt.gz
  mv W82_RH1_Gm275.ctable.gz  glyma.Wm82.gnm2.met1.K8RT.methylation_RH1.txt.gz
  mv W82_RH2_Gm275.ctable.gz  glyma.Wm82.gnm2.met1.K8RT.methylation_RH2.txt.gz
  mv W82_SR1_Gm275.ctable.gz  glyma.Wm82.gnm2.met1.K8RT.methylation_SR1.txt.gz
  mv W82_SR2_Gm275.ctable.gz  glyma.Wm82.gnm2.met1.K8RT.methylation_SR2.txt.gz
```

### Fill out the README and MANIFEST files
Fill out the README file. The empty template is at 
https://legumeinfo.org/data/about_the_data_store/templates
but it is often easiest to copy a README from another data collection for the 
same species and then change the fields that need to be changed.

```
  cp /usr/local/www/data/public/Glycine_max/Wm82.gnm2.ann1.RVB6/README.RVB6.yml .
  mv README.RVB6.yml README.K8RT.yml
  perl -pi -e 's/RVB6/K8RT/' README.K8RT.yml
```

Note that the README and MANIFEST files are in yml format. See a basic description:
https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html
or the full yml specification here (this is overkill for our purposes):
at http://yaml.org/spec/1.2/spec.html#id2781553
We use just a few of the yml features - basically, ...
The header directive (%YAML 1.2), http://yaml.org/spec/1.2/spec.html#id2781553
The "key : value pattern"
The list form, in which All members of a list are lines beginning at the same 
indentation level starting with a "- " (a dash and a space).

Fill out the MANIFEST files.
```
  cp /usr/local/www/data/about_the_data_store/templates/template__M* .
  rename 's/template__//' tem*
  rename 's/KEY/K8RT/' *KEY*
```

### Calculate the CHECKSUMs
Note the -r flag for the md5 command.
```
  KEY=K8RT
  rm CHECKSUM*
  md5 -r * > CHECKSUM.$KEY.md5
```

### Move the collection from public to private
Move the directory from from public to private, e.g.
```
  DIR=MY_NEW_DIRECTORY
  mv /usr/local/www/data/private/Glycine_max/$DIR /usr/local/www/data/public/Glycine_max/$DIR
```
Also, note the change in the status file in private/Species_dir/, e.g. 
```
  echo $'\n'"Moved Wm82.gnm2.met1.K8RT to public on 2018-04-19 by YOUR NAME"$'\n' \
    >> private/Glycine_max/status.glyma.txt
```

### Note in the Registry that the collection is public 
In the row/entry for your collection at http://bit.ly/LegFed_registry, 
change "private" to "PUBLIC" and change the status field to "[your initials] - done" 

### Update the html header file for the set of collections for this species
Each outer-level directory has a summary of the data in each collection at the top of the
page, e.g. https://legumeinfo.org/data/public/Glycine_max/
This summary is generated by script, pulling from the "subject" field in the README 
for each collection. Call the script like so (after putting it on your path) - 
passing in the outer-level directory name:
```
  /usr/local/www/data/about_the_data_store/metadata_summarize.sh Glycine_max
```

### Update the CyVerse Data Store
Change to the species directory where your new collection sits and then 
also use the irods icommands to cd to the corresponding location at CyVerse:

```
  cd /usr/local/www/data/public/Glycine_max/
  ipwd     # see your path at CyVerse
  icd /iplant/home/shared/Legume_Federation/Glycine_max    # cd into the LegFed directory at CyVerse
  ils
```
If the directory exists at CyVerse, then icd into the directory and just push the updated files to it.
If the directories don't exist, then recursively push the directory.
If the directories are wrong, then BLOW AWAY the CyVerse directories (CAREFULLY) and 
recursively push the correct ones.

```
  DIR=MY_NEW_DIRECTORY
  ipwd
  ils
  iput -rf $DIR  # copy directory & files - assuming we are at the correct location locally and at CyVerse
  ils
  ils $DIR
```
  
### UPDATE the metadata files at GitHub, for an existing repository
The metadata files (README, MANIFESTs, CHECKSUM) should be updated GitHub:
https://github.com/LegumeFederationDataStore
There is a repository for each outer-level directory (Glycine_max, Gene_families, etc.).

Example (modify as appropriate to your situation):
```
  COLLECTION=Glycine_max

  cd /usr/local/www/data/public/$COLLECTION
  git status
  git add -A
  git commit -m "updated README and MANIFESTs"
  git push -u origin master
```

Then refresh the page at github to check that the repository was correctly pushed
  https://github.com/LegumeFederationDataStore/Glycine_max
and you can check "git status" from the command line.


### For a NEW repository (for a new species) make a new repository at GitHub
If you have a NEW species-level directory (not just a new data collection within a species directory), 
then you will need to make a new git repo. Starting from the github GUI,
https://github.com/LegumeFederationDataStore, click the green "New" button on upper left, then
enter the repo name, e.g. Lupinus_albus. Leave the repository as "Public" and click "Create repository".

Then copypaste to the terminal, from within your new repo directory:
```
  COLLECTION=Lupinus_albus
  cd /usr/local/www/data/public/$COLLECTION
  git init
  cp /usr/local/www/data/about_the_data_store/templates/DS.gitignore .gitignore
  git add * .gitignore
  git commit -m "initial import"
  git remote add origin https://github.com/LegumeFederationDataStore/$COLLECTION.git
  git push -u origin master
```
Refresh the page at github to check that the repository was correctly pushed
and you can check "git status" from the command line.
