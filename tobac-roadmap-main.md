# Tobac Roadmap

## Overarching Goal
`tobac` is a scientific python package for the detection, tracking and analysis of clouds and their associated properties. In contrast to existing nowcasting and cloud tracking schemes which are commonly designed to be used with specific data types and applications, `tobac` aims to be a simpler, modular, and more general toolset for cloud tracking.

`tobac` has the objectives to be an

* Open-Source python package for everybody without cumbersome licence restriction and commercial interests
* Easy-to-Use modular package system
* Flexible framework for exploratory data analysis in cloud and precipitation research
* Robust interface to existing modern Python tools (like `xarray`, `scipy`, `scikit-image`, `trackpy`)


## Research Applications
Using `tobac` in the following research applications might be beneficial:  

* Lagrangian analysis in a moving frame: Applicable to observations and simulations
  * Process-based assessment of cloud and precipitation formation in NWP (Numerical Weather Prediction) models
  * Object-based model evaluation – Distributions of cloud sizes and lifetimes
* Spatial and structural analysis of cloud and precipitation fields 
  * Climate impact of convective organization
* Tracking of cloud and precipitation cells for monitoring or nowcasting purposes


## Structure of `tobac`

**Modular Framework**  
The modular framework of tobac was designed by Max Heikenfeld as part of the original release of tobac (Heikenfeld et al., 2019; https://doi.org/10.5194/gmd-12-4551-2019). The elements of tobac are laid out as the following:

* data input and output

* feature detection

* segmentation of cloud areas and volumes

* trajectory linking

* object-based analysis and visualisation

Many existing tracking methods, while sophisticated, can be described as 'grey-box' models, which utilise internal parameters whose effects may be unclear to the user. Instead, tobac aims to be modular collection of 'black-box' models, for which the parameters of each can be provided externally by the end user. As a result, tobac is capable of supporting multiple data types, applications and workflows through the mix and match of multiple modular components.

It should be noted that in order to support a diverse range of applications and methodologies, modular components of tobac are not restricted to the specific elements listed above. Modules are not defined by their internal structure, but instead by the data structures through which they interface with other modules. This allows a variety of modules not necessarily confined to those listed above, for example: a module that carries out both feature detection and segmentation, or a refinement module that takes an existing linked trajectory and returns an extended trajectory.

All module components should use only the defined data interfaces listed in the section below for inputs and outputs, in addition to all parameters required by the user to tune the model.

**Themes**  
Tobac themes are end-to-end processes to rapidly include new methods within tobac in a similar manner to existing tracking methodologies.

Note that we need to highlight that themes should not be completely separate from each other. Eventually themes should be worked into methods with common, defined interfaces, allowing users to “mix and match”. Themes incorporated within tobac should be developed into separate modular workflows over time.

**Currently Existing Themes**
* Tobac_v1

**Planned Extensions**
* Fabian Senf’s subsegmentation work (Senf et al., 2018)

* TINT (https://github.com/openradar/TINT)

* Will Jones’ optical flow detection and tracking (tobac-flow, may be an external package)

**Considerations for Themes within the Scope of `tobac`**  
When incoroporating themes within tobac, it should be considered both whether the theme can be separated into an underlying modular workflow, and whether the data requirement of the workflow fit within the data interfaces defined for tobac.

For example, the data structure of TINT is sufficiently similar to tobac for it to be incorporated as an alternate theme, with the ultimate goal of becoming alternative modular components of tobac.

Methodologies which require extensions to this framework (for example, requiring extra data inputs) should be considered for development as external submodules or pre-processors of tobac.

**Object-Oriented `tobac`**  
To achieve the overall goal of a fully modular and adaptable `tobac` framework, an object-oriented approach is to be considered. This approach would involve `tobac` being centred around a `tobac` class. This class would contain all of the defined data interfaces, allowing seamless integration of methodologies from multiple `tobac` themes. The advantages of a class-centred approach are multiple: 
* the ability to handle multiple data interfaces behind the scenes; 
* the ability to combine multiple detection and tracking methodologies without the user requiring knowledge of the underlying code; and improving the reproducibility of tracking methodologies.


## Definition of Data Interfaces

It is planned to build and extend `tobac` using a fully modular structure. Handling different modules (parts of the `tobac` workflow) in an exchangeble manner is only possible if data interfaces are well defined. `tobac` needs to be built common interfaces with common data formats, so that different methods can be used together.

### General Aspects

* data should be loaded and handled using xarray datasets/dataarrays

* netcdf should be the main output format, especially as future inclusion of zarr/memmap in netcdf standard should substantial improve performance

* all modules within `tobac` require a minimal set of input data

### Gridded Input Data

The `tobac` workflow starts with a set of gridded data that might be input from various sources. The input fields should be either loaded by `xarray` functionality or should be converted into `xarray.Dataset` after input.

**For Feature Detection**   
An `xarray.DataArray` should be supplied to the feature detection modules. This `DataArray` should hold information on time and geo-spatial reference.

Currently, the user is responsible to combine multi-variate fields (e.g. from several satellite channels) into one resulting field for which thresholding and feature detection is done. 

*Caveat*: Feature detection schemes of `tobac_v1` use `iris.Cube`s in their internal data handling. Conversion to from `xarray.DaraArray`s to  `iris.Cube`s is done internally, but fails if input data specifications do not fit with the `iris` requirements. This is too strict and these contrains need to be relaxed.

 
### Object and Trajectory Data

**Detected and Tracked Features**   
Feature detections and tracking output data in a tabular form. These data should be stored in `xarray.Dataset`. A consecutive numbering of features might be sufficient for a unique identification of each feature. Information on timing, location, magnitude, and association to tracks can be used for grouping of features and dedicated analysis.

*Split-and-Merge Morphology*: A new hierarchical data format, which captures non-linear relationships between sets of daughter and mother cells, needs to be tested for handling merging and splitting of tracked objects. A cf-compliant data structre would be a major advantage for tobac. It remains open how to best use cf-tree metadata  (Eric Bruning)

*Association with Networks*: In general, features might be interpreted as nodes of a network and tracks are a certain and unique realization of connections between nodes at different times. Interconnections between nodes (in time and space), the edges in the network, can build a rather complex web. A data format might be needed to store informations on these interconnection (e.g. the overlap between cells).

## Validation and Evaluation

Validation processes are required to ensure the accruacy and reliability of cloud detection and tracking methodologies. Although similar methods are used to validate a variety of different existing methods, as far as we are aware no large scale intercomparison of cloud tracking methods has taken place (see Lakshmanan and Smith, 2010, for interesting evaluation concepts). Through its common data format, tobac provides an ideal platform for a common validation framework that can be applied to a wide range of cloud tracking methods using different data types.

A common test dataset should be provided for use with the tobac framework. This dataset would include synthetic and/or real data for a variety of measurements from a variety of platforms, including geostationary satellite observation, ground based cloud radar and model output fields. The use of a common test dataset would enable not only the application of 'ground-truth' observations for validating cloud detection (such as lightning measurements), but could also be combined with a hand-tracked catalogue of clouds to evaluate not only the detection methods but also the ability of a method to accurately track individual cloud objects as well as their splits and merges.


## Development Goals and Future Vision
### Short-term Priorities
* implement the planned data interfaces on a basic level to the existing `tobac` code
* add planned themes as extensions (TINT, Fabian Senf’s subsegmentation work)
* replace all `iris.Cube` operations with `xarray` to allow for analysis a very large datasets
* develop a general concept for hierarchical data handling (not necessarily implemented at this point)

### Long-term Priorities
* planning and thorough implementation of `dask` capabilities for large dataset and parallel workflows
* implement hierarchical data structure for handling complex relationships between cells (e.g. split & merge problem)
* conceptualize an object-based (`tobac` classes) approach to handle exchange between different `tobac` elements (parts of themes), allowing extension to new methods (e.g. Will Jones’ optical flow detection and tracking)

### What feature requests should be within the scope of tobac?
*still under discussion (Caution: need to be selective about what themes are included)*
* dedicated feature detection techniques that go beyond threshold-based segmentation
* additions to the object tracking methodology allowing more flexible associations to tracks
* methods that combine objects to clusters for tracking on a higher hierarchical level



## References

Heikenfeld, M., P. J. Marinescu, M. Christensen, D. Watson-Parris, F. Senf, S. C. van den Heever, and P. Stier, 2019: tobac 1.2: towards a flexible framework for tracking and analysis of clouds in diverse datasets. Geosci. Model Dev., 12, 4551–4570, doi:10.5194/gmd-12-4551-2019.

Lakshmanan, V., and T. Smith, 2010: An Objective Method of Evaluating and Devising Storm-Tracking Algorithms. Wea. Forecasting, 25, 701–709, doi:10.1175/2009WAF2222330.1. 

Senf, F., D. Klocke, and M. Brueck, 2018: Size-Resolved Evaluation of Simulated Deep Tropical Convection. Mon. Wea. Rev., 146, 2161–2182.

   

        



            
