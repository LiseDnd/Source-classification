### Main Objective 
Classification of radio sources according to their morphology and creation of png and fits at different resolutions


### Repository Use
The repository is almost complete. You can download it as is. Next, download the [ELAIS-N1 data release](https://lofar-surveys.org/hd-en1.html) facets in the three resolutions (0.3", 0.6" and 1.2"). To avoid having to make any changes to the code, I recommend that you save the facets in folders named ‘_03_Facets’, ‘_06_Facets’ and ‘_12_Facets’. You will also need to download the [6" wild-field](https://lofar-surveys.org/downloads/deepfields/ELAIS_N1/image_full_ampphase_di_m.NS_shift.int.facetRestored.blanked.scaled-crop.fits). The 3 folders and the fits should be placed directly with the rest. Once everything has been downloaded, the codes should be usable without a problem.


### Methodology Description
The file 'candidate_sample_new.fits' is a catalog with 259 radio sources selected by DeJong and Mingo. Each source has among other things:
  - Coordinates that will be used to locate the source and make cutouts from this position,
  - The facet where it is located for cutouts of resolution 1.2", 0.6" and 0.3",
  - An identifier,
  - The coordinates of its optical host helping with classification,
  - An estimate of the size of the source.

The idea is to run the code 'candidate_make_png.ipynb' which will therefore make cutouts for each source at each resolution. For the png composed of the 4 resolutions, the size estimate already present in the catalog is preferred if it is not equal to 0, otherwise a new size estimate based on the 6" observations will be preferred. For FITS files, the size estimate on the 6" observations will always be used.

For this size estimation, the code therefore takes the 6" wild-field and cuts out a square of 50 pixels around the coordinates of the source studied. It calculates the noise of this image and identifies the pixels above this noise. From these pixels which are therefore considered as belonging to the source, the code estimates the size of the source and cuts out the square of 50 pixels accordingly.

It is now time to review the pngs that have just been created and identify the sources:
  - That are too zoomed in and created from the size estimate present in the catalog (generally for this case the 6" image is only composed of a few pixels),
  - That present several sources on the same image.

One technique to do this is to use the code 'Classification.ipynb' and create a fake catalog where the sources of the first case have the type "US" and the sources of the second case "NC". Then all that remains is to display the list of sources (with "Source_ID") having the type "US" then the type "NC" and copy/paste the latter into the appropriate code (in the "extract" function, change the list "sources_ID"):
  - If the source is too zoomed in, you must apply the code 'candidate_make_png_newcutout.ipynb' which remakes the png with the size estimate made on the 6" observations,
  - If the image contains multiple sources, you must make a mask on one of the sources using DS9 for each resolution, name it like this: {Source_ID}_{label}, with label being 'Mingo', '12', '06' or '03'. 'Mingo' referring to the 6" resolution. And place the masks in the Mask folder. All you have to do is apply the "candidate_make_png_masked.ipynb" code. For the moment, each image containing multiple sources is present in the catalog as many times as sources it contains. If this is not the case, this way of doing things does not work and you will have to apply modifications to the already existing code.

There is still the case where the image is still too zoomed in or not zoomed in enough which is not resolved and which would be good to take into account for a better classification. Note that the png and the fits are created in the folder 'Final_facet_subimages'

For classification, you have to use 'Classification.ipynb' which will open an application letting you choose for each png between 'FRI', 'FRII', 'NC' (No classifiable) and 'US' (Unsure). After that, the code creates a copy of the starting catalog with the additional keyword 'type' containing the classification information. A first version of this catalog, 'cat_classified_v0.fits', is available. It is recommended that someone with more expertise check or redo the classification.
