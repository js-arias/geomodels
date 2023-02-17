# GeoModels

*GeoModels* is a collection of paleogeography models
for historical biogeography.

## Basic details of the models

### Pixelation

The models are build using an equal area isolatitude pixelation:
Earth is divided in latitude rings,
and each ring is divided according to its circumference,
using the size of a pixel in the equatorial ring.
Each pole has its own pixel,
and odd numbered rings has a half pixel offset
for the starting pixel.
The pixel IDs start at 0 in the North Pole,
and then increased by one,
starting at the Sunday-Monday line
in west-east direction.
This pixelation is implemented
in the [earth package](https://github.com/js-arias/earth).

### Identification

Each general model is kept
in a particular directory,
with the general name of the model
followed by the number of pixels at the equator
and the size  of the time steps,
for example `earthbyte-360-5`
is the earthbyte model,
with a pixelation of 360 pixels at the equator,
and with time steps of 5 million years.

### Model types

There are two kind of models:
*plate motion models*
and *paleogeography models*.

A *plate motion model* is prefixed as `geomod-*`
and contains the pixel location
at different times.
Note that as a pixelation is used,
sometimes a present pixel might be represented
by two or more pixels in the past.
On the other hand,
it is possible that several present pixels
might be set in the same pixel in the past.
This motion model is also know as a rotation model,
because it is based on Euler rotations over a sphere.

A *paleogeography model* is prefixed as `timepix-*`
and contains pixel values
(for example a land-scape identifier)
at different times.
In this case pixel identities
across different time periods are not preserved.
To ease identification of the pixel values
a key file prefixed `key-*`
is provided for each paleogeography model.

Additionally,
a *plate pixel model* is prefixed as `pixels-*`
and is used to link pixels
with tectonic plate.

## Citation and data license

The models in the collection
are processed from the ones provided by their original authors.
As such,
the original publication should be cited when the model is used.
In each model,
the relevant papers are given in a README file,
and provided as bibtex.

It is not required to cite this repository,
but if you do not include the model in the supplementary material
of your publication,
it might be a good idea to link this repository
and help others to replicate or re-use your analysis.
