# README - SIMULATION FOR PLATO-PL-KUL-0020

This readme file gives a detailed explaination of the data products produced as part of the Technical Note (TN): PLATO-PL-KUL-TN-0020. For an ellaboration on the input and configuration parameters that have been used in these simulations we refer to the aforementioned TN.

---

## Important notes

NOTE 1: Several data product are stored in the binary format called 'feather', which has the file extention ".ftr". Feather is a high efficiency format for I/O writing and storage. Apart from its high efficiency usage, this format was chosen in order to more efficiently investigate/work with the files in Python. Like the pickle binary format, feather data can be loaded directly into a Pandas data frame, however, feather is less dependent on the Python version compared to pickle. We refer to the following page for a direct comprarison on the different data formats that efficiently can be used in combination with the pandas library: https://towardsdatascience.com/the-best-format-to-save-pandas-data-414dca023e0d (reference validation check the 2022-04-26). To load any of the feather files into a pandas data frame simply use following from a Python shell:

	import pandas as pd
	df = pd.read_feather("filename.ftr") 

NOTE 2: As the P1 and P5 simulations both include pointing errors and camera misalignments we here note that 12 stars were never unobservable by any of the N-CAMs for Q23 and Q24:
000000002
000000139
000000337
000000601
000000719
000000855
000000932
000000936
000000944
000000953
000000985
000000992

---

## Data structure

From the location of this readme file the following folders are visible:

* `inputfiles/`	      : All inputfiles used to run PlatoSim.
* `jobscripts/`	      : PBS job scripts used to run simulations on cluster.
* `P1/`           	  : Data products for the P1 sample. 
* `P5/`           	  : Data products for the P5 sample.
* `P*/lightcurve/`	  : Light curves for each target star.
* `P*/varsource/` 	  : Input noise-less light curves used for each simulation. 

The folders found in at the same location as this README file are the following:


### inputfiles/

* `AOCS_4mHz_4mas_180d_Q23-24.txt`: AOCS jitter time series for Q23-24, 180 days, 2.5s cadence. File contains the columns:  time from BOL [s], yaw [arcsec], pitch [arcsec], and roll [arcsec] 

* `TED_4mHz_6arcsec_180d_Q23-24.txt`: TED drfit time series  for Q23-24, 180 days, 25 s cadence. File contains the columns:   time from BOL [s], yaw [arcsec], pitch [arcsec], and roll [arcsec] 
* `TE_4mHz_Q23-24.txt`: Transmission Efficiency (TE) for Q23-24, 180 days, 25 s cadence. File contains columns: time from BOL [s], TE [val from 0-1]


###	jobscripts/

This folder contains the job script used to simulate both the noise-less light curves together with the P1 and P5 light curves on the VCS cluster.

NOTE note that the folling convention was used tp produce noise-less light curves using the files:

	data_hpc_varsource_P1.txt 
	data_hpc_varsource_P5.txt
	
Besides the physical parameters (M,R,Teff,logg) the "xsource" flag takes the values:

	0 -> Std/constant (roAp with 1-2 hamonics)
	1 -> Gran, Puls
	2 -> Gran, puls, Spot
	3 -> Gran, Puls, Spots, Exo
	4 -> Constant stars


###	P1/ & P5/

As mentioned the folder `P1/` and `P5/` contain the data products produced by PlatoSim and post-processed with the L1 pipeline. For both samples each star-camera-quarter simulation has a final light curve stored in a file called e.g. `000000001_Ncam1.1_Q23.ftr`, referring here to the star ID (000000001), N-CAM camera-group no. and camera no. (Ncam1.1), and the quarter (Q23). Similarly the files with the extension `.cat` (e.g. `000000001_Ncam1.1_Q23.cat`) are the stellar catalogue produced by the L1 pipeline. We here present all the files and what kind of information that are stored within them:

NOTE for P1 sample: The folder `P1/lightcurves_corrected` contains some corrections to the `P1/lightcurves`. The error in the old dataset was due to a pandas index error when saving the output files and more columns were present when more stellar contaminants were saved to file. This has been corrected for in the new dataset.

NOTE for P5 sample: Two datasets exist for the P5 sample. The first dataset, produced together with the P1 sample, were without mask updates. A new dataset is currently being produced with mask updates. This dataset is however using the lastest version of PlatoSim (v.3.6.0) and may not be directly comparable to the ones without mask updates.

#### Files contained in P1/lightcurve:

"*.ftr": Final extracted P1 sample data with the following columns: 
	time		: [seconds] Time points with zero-ponint at mission BOL
	flux		: [electrons] Extracted flux 
	cx		: [pixel] X centroid pixel within 6x6 pixel subfield 
	cy           	: [pixel] Y centroid pixel within 6x6 pixel subfield 
	bg		: [electrons] Background flux   
	flux_err	: [electrons] Flux error    
	cx_err    	: [pixel] X centroid pixel error
	cy_err     	: [pixel] Y centroid pixel error
	bg_err       	: [electrons] Background flux error
	chi2  		: Chi-sqaured of PSF fit
	iter          	: Iterations ols f converges for PSF fitting 


#### Files contained in P5/lightcurve:

"*.ftr": Final extracted P5 sample data with the following columns: 
        time  		: [seconds] Time points with zero-ponint at mission BOL
        flux        	: [electrons] Extracted flux
        xc        	: [pixel] X centroid pixel within 6x6 pixel subfield
        yc  		: [pixel] Y centroid pixel within 6x6 pixel subfield
        flux_cor 	: [electrons] Flux error

"*.fits": Mask-update file for each target star Marchiori et al. (2019).

"*.spr": Stellar Polution Ratio (SPR) information of target star as per Marchiori et al. (2019).


#### Files indentical for P1 and P5 sample:

"*.cat": Star catalogue information of the target and the contaminants: 
	No 		: Star ID of nine digits
	RA  		: [degree] ICRS Right Ascension 
	DECL  		: [degree] ICRS Declination
	X  		: [pixel] X centroid pixel within 6x6 pixel subfield of first image
	Y 		: [pixel] Y centroid pixel within 6x6 pixel subfield of first image
	MAG 		: PALTO passband magnitude P
	CCD		: CCD ID for star (subfield) location
	Xccd 		: [pixel] X centroid pixel coordinate of full frame CCD of first image
	Yccd 		: [pixel] Y centroid pixel coordinate of full frame CCD of first image
	Xfp 		: [degree] X focal plane coordinate of first image
	Yfp		: [degree] Y focal plane coordinate of first image

"*.invert": Information about the PSF inversion. For more information see: PLATO-PL-LESIA-TN-0069 and PLATO-PL-LESIA-TN-0070.


###	P*/varsource/

The varsource/ folder contains two sub-folders, located within each of the "P1/" and "P5/" folders, contains the noise-less light curves used in the respective simulations for the P1 sample and P5 sample. For each sample there are 1000 ascii files with a nine digits prefix of the star ID and with file extentions of ".txt". These files are the direct input for PlatoSim. The first column is the time points expressed in seconds and the second columns it the intensities expressed in relative magnitude. The remaining 1000 binary files, likewise with a nice digit prefix and following a "_components.ftr" naming scheme, contains all the variability components in seperate columns. The first column is the time in unit of seconds, and the additional columns (if more than one) are the intensities of each model components in units of ppm. E.g. a full set of components (if applicable) for the corresponding target are: [time, spot, gran, puls, tran, sum] - i.e. the time, granulation, pulsations, transits, and summed light curve. To sum up we have the following files:

xxxxxxxxx.txt
 	time 		: [seconds] Time points with zero-ponint at mission BOL
 	dmag		: Relative magnitude measurements
 	
xxxxxxxxx_components.ftr:
	time 		: [second] Time points with zero-ponint at mission BOL
	spot 		: [ppm] Relative flux of spot modulation model
	gran 		: [ppm] Relative flux of granulation model 
	puls 		: [ppm] Relative flux of pulsation model
	tran 		: [ppm] Relative flux of transit model
	sum 		: [ppm] Relative flux of all models combined

table_varsource_P1.ftr and table_varsource_P5.ftr: 
	ID		: Star ID of nine digits
	Ms_Msun	: [Msun] Stellar mass relative to the Sun	
	Rs_Rsun	: [Rsun] Stellar radius relative to the Sun
	Teff_K		: [kelvin] Stellar effective temperature
	logg		: log10 of surface gravity
	Z		: Metallicity
	alpha		: Alpha element abundance
	BV		: Stellar colour (in the Johnson-Cousin passband)
	logRHK		: log10 of activity index
	Prot_day	: [days] Mean rotation period
	Pmin_day	: [days] Minimum spot period (equivalent to minimum latitude of spots)
	Pmax_day	: [days] Maximum spot period (equivalent to maximum latitude of spots) 
	lmax_deg	: [degree] Maximum spot latitude
	Pcyc_year	: [years] Activity cycle period
	Povl_year	: [years] Overlap period between consecutive activity cycles
	Acyc		: Activity cycle amplitude (controls the activity rate)
	is_deg		: [degree] Inclination of stellar rotation axis
	numax_muHz	: [micro Hertz] Frequency of maximum power
	deltanu_muHz	: [micro Hertz] Large frequency seperation
	bol_coeff	: Bolometric passband coefficent
	dphi_deg	: [degree] Phase offset between first and second harmonic (photometric standard)
	a1		: Relative amplitude of the first (main) harmonic (photometric standard)
	a2		: Relative amplitude of the second harmonic (photometric standard)
	scale		: [mili magnitude] Scaling factor applied to the releative amplitudes (photometric standard)
	Mp_Mearth	: [Mearth] Planet mass relative to Earth's mass
	Rp_Rearth 	: [Rearth] Planet radius relative to Earth's radius
	a_Rstar	: [Rstar] Semi-major axis relative to stellar radius
	P_day		: [days] Orbital period
	t0_day		: [days] Time of ephemeris (known transit time)
	e		: Eccentricity
	ip_deg		: Inclination of the planet's orbital plane
	w_deg		: [degree] Argument of periastron
	u1		: Linear coefficient of limb darkening
	u2		: Quadratic coefficient of limb darkening


---

## Data reproducibility

Test simulation:

	platonium 50 1 1 23 --project kul20 --sample P5 --seed 1234 --nexp 30


### Simulations 2022 - P1 sample & P5 sample without mask-update

PlatoSim version: 3.5.3-19-g18d87597
commit 18d875972184b2e312fde90631a92b570946a53c (HEAD)
Merge: a395acc7 0100bcbd
Author: Nicholas Jannsen <36379445+nicholasjannsen@users.noreply.github.com>
Date:   Sun Mar 27 22:37:03 2022 +0200


### Simulations 2023 - P5 sample with mask-update

#### PlatoSim "git log"

commit bf1197705a3987a4b3bf409d003f1626ccf2d917
Merge: 6585869 cbb11d1
Author: Nicholas Jannsen <36379445+nicholasjannsen@users.noreply.github.com>
Date:   Thu Jun 15 23:33:15 2023 +0200

#### Plato "svn info"

Use "svn info":
Path: .
Working Copy Root Path: /vsc-hard-mounts/leuven-data/341/vsc34166/plato/algorithms
URL: https://version-lesia.obspm.fr/repos/PLATO/algorithms
Repository Root: https://version-lesia.obspm.fr/repos/PLATO
Repository UUID: d89e5041-8581-4b43-9152-9417a181a36f
Revision: 5399
Node Kind: directory
Schedule: normal
Last Changed Author: grolleau
Last Changed Rev: 5399
Last Changed Date: 2023-05-23 11:37:45 +0200 (Tue, 23 May 2023)


### VSC info

Below is a full list of modules that was loaded on the VSC cluster upon execution:

```
  1) Emacs/26.1-GCCcore-6.4.0                25) NASM/2.15.05-GCCcore-10.3.0
  2) intel-compilers/2021.2.0                26) libjpeg-turbo/2.0.6-GCCcore-10.3.0
  3) UCX/1.10.0-GCCcore-10.3.0               27) gompi/2021a
  4) impi/2021.2.0-intel-compilers-2021.2.0  28) OpenMPI/4.1.1-GCC-10.3.0
  5) iimpi/2021a                             29) FFTW/3.3.9-gompi-2021a
  6) imkl/2021.2.0-iimpi-2021a               30) ScaLAPACK/2.1.0-gompi-2021a-fb
  7) intel/2021a                             31) foss/2021a
  8) expat/2.2.9-GCCcore-10.3.0              32) bzip2/1.0.8-GCCcore-10.3.0
  9) ncurses/6.2-GCCcore-10.3.0              33) zlib/1.2.11-GCCcore-10.3.0
 10) DB/18.1.40-GCCcore-10.3.0               34) libreadline/8.1-GCCcore-10.3.0
 11) OpenSSL/1.1                             35) Tcl/8.6.11-GCCcore-10.3.0
 12) Perl/5.32.1-GCCcore-10.3.0              36) SQLite/3.35.4-GCCcore-10.3.0
 13) CUDA/11.4.1                             37) cURL/7.76.0-GCCcore-10.3.0
 14) binutils/2.36.1-GCCcore-10.3.0          38) libarchive/3.5.1-GCCcore-10.3.0
 15) numactl/2.0.14-GCCcore-10.3.0           39) CMake/3.20.1-GCCcore-10.3.0
 16) XZ/5.2.5-GCCcore-10.3.0                 40) GMP/6.2.1-GCCcore-10.3.0
 17) libxml2/2.9.10-GCCcore-10.3.0           41) GCCcore/10.3.0
 18) libpciaccess/0.16-GCCcore-10.3.0        42) libffi/3.3-GCCcore-10.3.0
 19) hwloc/2.4.1-GCCcore-10.3.0              43) GCC/10.3.0
 20) libevent/2.1.12-GCCcore-10.3.0          44) GSL/2.7-GCC-10.3.0
 21) libfabric/1.12.1-GCCcore-10.3.0         45) OpenBLAS/0.3.15-GCC-10.3.0
 22) PMIx/3.2.3-GCCcore-10.3.0               46) gengetopt/2.23-GCCcore-10.3.0
 23) FlexiBLAS/3.0.4-GCC-10.3.0              47) worker/1.6.12-foss-2021a
 24) libpng/1.6.37-GCCcore-10.3.0
```
