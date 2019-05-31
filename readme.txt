Files used to perform simulations and process output for Kim et
al. PLoSCompBiol 2013, 9(3):e1002953. doi:
10.1371/journal.pcbi.1002953

1. The root level folder (same as for this readme) contains xml files
to run NeuroRD for both DSI simulations and long term synaptic
plasticity simulations.  UchiModel*.xml is the top level file for the
DSI simulations with one random seed (used for single trace figures,
i.e., Fig 2A1, 2B1 and Fig 5C).  mglurMaster*.xml is the top level
file for the long term synaptic plasticity simulations for one randome
seed (used for single trace figures, i.e. Fig 5A1, B1, C, D).

2.  The Robust folder contains the xml files used to run robustness
simulations (Fig 7).  Most robust*.xml files are alternative reaction
files (replace mglu_2ag_reac12mar20desens025.xml in the top level
model file). Robust*Init*.xml are alternative initial condition file
(replace mglur_2ag_init12mar1lowrGabg.xml in the top level model
file).

3. The MorphLong folder contains the xml files used to run simulations
using the longer morphology (Fig 9, 10).  Because of the larger
volume, the calcium input has been increased, thus the stimulation
file is different than the default.  For a similar reason, the
diffusion of dhpg has been slowed down to make the dhpg at the
stimulated spine similar to that in the default. The output scheme
file specifies fewer molecules in order to decrease file size.

4. Version of NeuroRD used to run simulations above:
NeuroRD2.1.1newtableNP30.jar
(Above jar file is included in this archive)

The main simulation run commands are (the below each on one line):

java -jar NeuroRD2.1.1newtableNP30.jar mglurMaster20hz12mar20desens025lowrG.xml 20hzLTD.out >> 20HzLTD.txt

java -jar NeuroRD2.1.1newtableNP30.jar mglurMasterTheta12mar20desens025lowrG.xml ThetaLTP.out >> ThetaLTP.txt

Note that some of these simulations are lengthy to run (days).  A
shorter one is:

java -jar NeuroRD2.1.1newtableNP30.jar
UchiModel12mar20desen025lowrG_1s5dhpg20sCa12-3.xml sample_output.txt >>
sample_log.txt

which took about an hour and a half on a 2013 MacBook Pro laptop.

...

PostProcess.tar.gz, which can be downloaded from the software page,
has items 5 and 6:

5. Version of NRDpost (c++) used to process the conc.txt files to
obtain single molecules region averages:

nrdpostAB.cpp
nrdpostAB.h

compile the code using the following line:

c++ nrdpostAB.cpp -o NRDPostAB

NRDPostAB - h will list all the different options for use.
The following was used to process the data:

NRDPostAB -i[UchiModel12mar20desen025lowrG_5s0dhpg20sCa12-3-slow-conc.txt] -m[Pkct] -r[UchiModel12mar20desen025lowrG_5s0dhpg20sCa12-3-mesh.txt] -n -o[12mar205s0]

This generates the region averages for active PKC.  Note that any
spines called "FAKE" that were created solely to provide dendritic
injection will be ignored and not included in the output file.

6. python code

MoleculesAUC.py reads in the dataprocessed by NRDPostAB (file name
with _avg.txt as suffix). It assumes there are 3 different stimulus
durations and 4 dhpg concentrations.  It calculates the area under the
curve for PKC and 2AG for each of these 12 files, and then calculates
the ratio with respect to the 0dhpg condition.  Additional molecules
can be processed by specifying them in the mollist variable.
Presently, it only operates on the second to last column in the
NRDPostAB output file, but additional columns can be processed by
adding their column numbers to the meancol variable.

LTPratioMultiSpine.py was used to extract area under the curve and
ratios from the long morphology simulations.  This reads in the data
processed by NRDPostAB (file name with _avg.txt as suffix).  The
number of spines in the morphology must be specified (it will vary
with the spine seed), the time that stimulation is applied (stimtime),
the time to end calculation of molecule activation (this should be
determined by qualitiative evaluation of the results.  Do not include
too much time after activation returns to basal, but this time has to
be the same for all stimulation conditions.

LTPratio.py was used to extract area under the curve and ratios from
the default morphology - both default and robustness reaction and
initial conditions.  It is very similar to LTPratioMultiSpine.py This
reads in the data processed by NRDPostAB (file name with _avg.txt as
suffix).  The list of columns to process from the avg.txt file must be
specified.

ReadHeaderAvg.py

This python program can process input that has not first been
processed by NRDPostAB and provide output similar to that of
NRDPostAB.  The limitation is that each molecule must be in a separate
file (this can be achieved using a different output scheme file for
NeuroRD).  It can process multiple molecules and multiple files at
once by specifying them in mollist and fnamelist variables. Headername
specifies a file which contains the first header line output from
NeuroRD - it can either be one of the files in the list above, or a
separate file which contains ony the first line of one of the output
files in the list.  The name of only one mesh file should be
specified.  So, if you are processing multiple files they all must
have used the same morphology. fake is the label given to the tiny
injection spines that were created solely to provide input to the
dendrite.  By default, the average over each spine, and other regions
is caculated.  Optionally it will provide a spatial average over the
dendrite.  This means it will take the entire dendrite, and subdivide
it into 10 (or more or less) areas along a single dimension.  This
spatial average is useful for calculating space constant for
decay/spread of a molecule along the dendrite. The output of this file
can be used as input to LTPratioMultiSpine.py.
