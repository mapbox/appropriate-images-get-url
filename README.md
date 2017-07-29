# @mapbox/appropriate-images-get-url

[![Build Status](https://travis-ci.org/mapbox/appropriate-images-get-url.svg?branch=master)](https://travis-ci.org/mapbox/appropriate-images-get-url)

🚧🚧 **EXPERIMENTAL! WORK IN PROGRESS!** 🚧🚧

**Use in combination with [appropriate-images].**

After you've generated resized, optimized images with [appropriate-images], you'll want to use them in the browser.
In order to do that, you'll need to determine *which variant* of the image to load — which size, and whether to load `.webp` or not.
That's what this module is for.
This is how the image configuration used by [appropriate-images] can be *reused in the browser* to select the appropriate image to load at runtime.

**If you're thinking about using this function in combination with React, check out the [appropriate-images-react](https://github.com/mapbox/appropriate-images-react) repo.**

## Installation

```
npm install @mapbox/appropriate-images-get-url
```

## API

### getAppropriateImageUrl

`getAppropriateImageUrl(options)`

Uses your image configuration and a width value to figure out the URL of the image variant that should be loaded.
Returns a URL for the appropriate image variant that you created with [available-images].

The returned URL will account for

- the available width,
- the resolution of the screen, and
- whether or not the browser supports `webp`.

The image variant that is selected will be **the narrowest variant that is at least as wide as the available width, or else, if the available width exceeds all sizes, the widest variant**.

#### options

##### imageId

Type: `string`.
**Required.**

Id of the image to be loaded.
Image ids correspond to keys in your [appropriate-images configuration].

##### imageConfig

Type: `Object`.
**Required.**

Your [appropriate-images configuration] object.

##### availableWidth

Type `number`.
Default: `Infinity`.

Not technically required, but you should provide it.
This is the width available to the image.
This is key to figuring out which size variant to load.

##### hiResRatio

Type `number`.
Default: `1.3`.

The ratio at which you want to consider a screen "high resolution".
If the browser judges that the screen is high resolution, according to this ratio, the [`availableWidth`] provided will be multiplied by this ratio when determining which size variant to load.
This means that in a `300px`-wide space but *on a Retina screen*, the image at least `600px` wide will be loaded.

##### imageDirectory

Type `string`.

If provided, this will be prepended to the URL.

## Examples

```js
const getAppropriateImageUrl = require('@mapbox/appropriate-images-get-url');

const imageConfig = {
  bear: {
    basename: 'bear.png',
    sizes: [{ width: 300 }, { width: 600 }]
  },
  montaraz: {
    basename: 'montaraz.jpg',
    sizes: [
      { width: 600, height: 500 },
      { width: 1200, height: 800, crop: 'north' },
      { width: 200, height: 200, crop: 'southeast' },
    ]
  }
};

getAppropriateImageUrl({ imageConfig, imageId: 'bear', width: 280 });
// On a regular-resolution screen: bear-300.png or webp
// On a high-resolution screen: bear-600.png or webp

getAppropriateImageUrl({ imageConfig, imageId: 'bear', width: 550 });
// bear-600.png or webp

getAppropriateImageUrl({ imageConfig, imageId: 'bear', width: 800 });
// bear-600.png or webp

getAppropriateImageUrl({
  imageConfig,  
  imageId: 'montaraz',
  width: 400,
  imageDirectory: 'img/optimized/'
});
// On a regular-resolution screen: img/optimized/montaraz-600x500.jpg or webp
// On a high-resolution screen: img/optimized/montaraz-1200x800.jpg or webp
```

[appropriate-images]: https://github.com/mapbox/appropriate-images
[appropriate-images configuration]: https://github.com/mapbox/appropriate-images#image-configuration
[`availableWidth`]: #availablewidth
