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

Resolution is indicated using the form `e<Number>`,
with the number being the number of pixels
in the equatorial ring.
For example e360 is a pixelation with 360 pixels
at the equatorial ring.

### Identification

Each general model is kept
in its own repository,
usually the files are identified
by the name of the model,
the kind of file,
the spatial resolution,
and time resolution.
For example `earthbyte-motion-360-5.tab`
is the plate motion model
of the earthbyte model,
with a e360 pixelation,
and a time steps of 5 million years.

### Model types

There are two kind of models:
*plate motion models*
and *landscape models*.

A *plate motion model* is identified as `*-motion-*.tab`
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

A *landscape model* is identified by its model name
and contains pixel values
(a landscape identifier)
at different times.
In this case pixel identities
across different time periods are not preserved.
To ease identification of the pixel values
a key file identified with `*-key.tab`
is provided for each landscape model.

Additionally,
a *plate pixel model* is identified as `*-pixels-*.tab`
and is used to link pixels
with tectonic plate.

## Models

### Plate motion models

Model                         |       Pixelation | Time frame | Time step | Landscape
----------------------------- | ---------------- | ---------- | --------- | ---------------
[Earthbyte](https://github.com/js-arias/gm-earthbyte) | e360, e180, e120 |    400 - 0 |         5 | Cao et al. 2017

## Citation and data license

The models in the collection
are processed from the ones provided by their original authors.
As such,
the original publication should be cited when the model is used.
In each model,
the relevant papers are given in a README file,
and provided as bibTeX.

It is not required to cite this repository,
but if you do not include the model in the supplementary material
of your publication,
it might be a good idea to link this repository
and help others to replicate or re-use your analysis.

## Building your own model

To build your own model,
it is expected that you build it previously on [GPlates](https://www.gplates.org/).
The file(s) containing the plate features
must be in GPML format
(do not use the compressed gpmlz).
The rotation file must be a rot file.

The process uses the command `plates`
that is included in the [earth package](https://github.com/js-arias/earth).

### Import a plate motion model

First,
transform all the tectonic features files
into pixel files.
For example to read the file `contient.gpml`
to produce `continent.tab`,
a pixelation file with 360 pixels at equator:

```bash
plates pixels import -e 360 -o continent.tab continent.gpml
```

If yo have multiple pixel files,
you can merge them:

```bash
plates pixels cat -o pixels.tab continent.tab volcanic.tab
```

To check that pixelation is right
you can make an image of the pixelation:

```bash
plates pixels map -o pixels.png pixels.tab
```

After pixelation is build,
you can make the rotation,
defining the starting time and time steps,
or a predefined series of time stages.
For example here we make the file `model.tab`
from the pixels that we build before,
from 420 million years,
in 5 million years time stages,
and using `rotations.rot` rotation file:

```bash
plates rotate --from 420 --step 5 --pix pixels.tab --rot rotations.tab model.tab
```

### Import a paleo-landscape model

There are several forms to build paleo-landscape models.

To transform a plate motion model into
a paleo-landscape model
use the command `timepix add`,
if no time frame is indicated,
the plate motion model time frame will be used.
For example using the plate motion model at `model.tab`
to create a paleo-landscape model `timepix.tab`
with the value of 1:

```bash
plates timepix add --from 400 --in model.tab --val 1 timepix.tab
```

In the case of only a unique time slice is to be added,
use the flag `--at`.
In this example,
we add the pixels of the model `sea-rot.tab`
to paleo-landscape model `timepix.tab`
at 100 million years
with the value of 2:

```bash
plates timepix add --at 100 --in sea-rot.tab --val 2 timepix.tab
```

In some cases,
for example the [Cao model included in Earthbyte](https://www.earthbyte.org/gplates-2-3-software-and-data-sets/),
a GPML files contains layers
at different time stages,
with different polygons to be rotated
at a particular time stage.
In such cases,
first we must make the pixelation,
then rotate the pixelation model,
and finally add the pixels.
For example adding pixels from `lm.gpml`
with a value of 3
at 120 million years
to the paleo-landscape model at `timepix.tab`:

```bash
plates pixels import -e 360 --at 120 -o lm-pix-120.tab lm.gpml
plates rotate --pix lm-pix-120.tab --rot rotations.rot lm-rot-120.tab 120
plates timepix add --at 120 --in lm-rot-120.tab --val 3 timepix.tab
```

In other case
you have a mask image file
(a black and white image,
only white pixels will be used).
In this example,
we add pixels from the mask `mask-lowsea.png`
with a value of 2
in the present time
to the paleo-landscape model at `timepix.tab`:

```bash
plates timepix add --at 0 -f mask --in mask-lowsea.png --val 2 timepix.tab
```
