VectorClust
=============

VectorClust is matlab program for manually clustering data via a graphical user interface.  Each data element is a raw array of data called a vector (for example a sound snippet, or a voltage trace).  Each vector is also associated with a vector of features which can be computed by vectorClust.  The features can be scalars (e.g mean, std, mean pitch) or vector themselves (e.g. pitch waveform, smooth waveform). VectorClust allows you to visualize these features, and then define clusters using polygons in 2d feature projections. 

VectorClust includes a few functions to import data, export data and compute features, and it is easy to write your own code for these purposes.  Any function in the VectorClust directory with the prefix vc_imp will appear as an import option.  Any function with the prefix vc_exp will be listed as an export options.  And any function with the prefix vc_cmf will be listed a option for computing vector features.

VectorClust was initially written for the purpose of clustering birdsong song syllables.  Therefore many of the included importers, exporters, and features relate to song syllables and make reference to tools used and written by myself and other from the laboratory of Michale Fee.

Note that VectorClust is still a work in progress.

It is useful to understand the data structures used by vectorClust.  The main data structure is the vcdb (vector clust database):

Assuming you have n data elements, vcdb includes the following data:

	%raw data and data features:
	vcdb.d.v (raw vectors, cell array, nx1)
	     d.sf (scalar features, matrix (n x num scalar features)
	     d.vf (vector features, cell array (num vector featuresx1) of cell arrays(nx1)
	     d.cn (cluster number, array, nx1, initially NaN)
	     c.icn (imported cluster number)
	     d.t (time, array, nx1, if not relevant leave as NaN)
            d.i (identity, cell array, created on import, for use by export function, not used internally)	

	%features descriptions of both scalar features (e.g mean) and vector features (e.g. smoothed)
	vcdb.f.sfname = {}; (name of each scalar feature, num scalar features x 1)
		f.sffcn = {}; (function to produce feature?, num scalar features x 1)
		f.sfparam = {}; (function parameters, num scalar features x 1)
		f.vfname = {}; (num vector features x 1)
		f.vffcn = {}; (num vector features x 1)
		f.vfparam = {}; (num vector features x 1)            

	%clusters information:
       vcdb.c is an array of of structures
	vcdb.c(1).number (the number of the cluster)
	         .str    (the name of the cluster)
	         .color  (the color of the cluster)
	        .polys   (polygons that define the cluster)
	         .polys{n}.xfeat   (the feature on the x-axis of the polygon: -1 indicates 
				   the prev cluster num, -2 the next cluster num.)
			   .yfeat   (the feature on the y-axis of the polygon: -1 indicates 
				   the prev cluster num, -2 the next cluster num.)
			   .xverts  (The x-coords of the polygon vertices)
			   .yverts  (The y-coords of the polygon vertices)
	         .incs     (indices of forced cluster inclusions)
	         .excs	    (indices of forced cluster exclusions)
     
       vcdb.fileName: name of the original dbase file (when importing from ElectroGui) %%% added for Tatsuo
       vcdb.pathName: path name of that file %%% added for Tatsuo
	
vectorClust also maintains some internal state, that is not saved between sessions:
    
	vcg.vcdbFilename (filename where vcbase is saved).
    vcg.vcdir (the folder containing the vector clust functions)
    vcg.bSel  (boolean vector specifying which vectors are selected)(can't be true unless bDraw and bFilt are true)
    vcg.bDraw (boolean vector specifying which scatter points should be drawn given the current plot type)
    vcg.bFilt (boolean vector specifying which points meet filter criteria)
    vcg.bRand (the current random permutation)(might not exist before initial randomization)
    vcg.hScat (handles to the scatter group)(Userdata of each child is ud.ndx = index within vcdb.d.v) 
              (plotted in order with filtered-out(~bFilt) and undrawn(~bDraw))
    vcg.hVect (handles to the vectors) 
    vcg.mVectMat (the aligned vectors in matrix form, only valid when hVect is not empty).
    vcg.feat2colorMap (map for feature to color conversion)
    vcg.feat2colorFeat (feature which will be mapped to color)
    vcg.feat2colorRange (range for color)
    vcg.subsamp.p (subsample ratio p/q)
    vcg.subsamp.q
    vcg.subsamp.bFast (whether to use filtering)
    vcg.stretch.n (fixed length)
    vcg.stretch.bFast (whether to use filtering)
    vcg.vectRange (for vector outlier removal)
    vcg.specgram.fs
    vcg.specgram.freqRange
    vcg.specgram.colorRange
    vcg.options.largeSetThreshold
               .unclusteredColor
               .unclusteredMarker
               .unclusteredSize
               .maxSets   (maximum number different filter sets)
               
    

0)
Importing:  
[vectors, scalar_features, vector_features, cluster, time, identity, scalar_feature_names, vector_feature_names] = vc_imp_*(varargin);
dmitri_dbase_segments
dmitri_dbase_events
aaron_annotation
aaron_cell_withWave
aaron_cell_noWaves
Notes: empty arrays return if import fails.
If varargin{1} = 'parameters', then returns. params.name = '';
If varargin{1} = 'batch', then varargin{2} should be the search string.


1) 
(Auto-Save) checkbox
Computing Features
	[scalar_featurs, vector_features, sf_names, vf_names, sf_param, vfparam] = vc_cmf_*(vcdb);
	SAPFeatures
	AAFeatures
	SpikeFeatures


2)
Exporting:
	[errorString] = vc_exp_*(vcdb);	
    errorString: [] if successful, 'cancel' if canceled
	cluster identity straight to matlab.
	To dbase
	to Annot
	to misc

Ideas:
next chronological vector as features
prev chronological vector as features.

TODO:

- Deal with nan features not resulting in handles.
- Add compute princible components.
- Add import dbase event.
- Add import dbase segments.
- Add import annotation.
- Add histogram to y-feature list.
- Add export graphs to powerpoint.
- Complete conflicts panel.
- Reset VCG vales at approriate time.
- Add prev cluster and next cluster as features (select sort)
- Add previous time cluster, next time cluster (select sort)
- Add isi feature.
- Add icn feature.
- Add time-of-day and time feature.
- When color change, update html.
- Set Store xlim/ylim for each view.
- Make selection smarter, clear selection?, reset selection, substract from selection, augment selection
- Key-press to undo-redo last polygon.
- Limit to One polygon per cluster per view.
- Make global options editable.
- Add new cluster properties: markersize, markertype?
- Add Run Macro button.
- Make subsets only load subset from file.
- Option to not load vector features.
- Make imp/exp/cmp file lists use 'parameters' to put correct name in popup.
