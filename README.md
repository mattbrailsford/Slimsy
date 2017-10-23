Slimsy
============
**Effortless Responsive Images with LazySizes and Umbraco**

# Slimsy v2 is not compatible with Slimsy v1 at all, if you upgrade you will have to refactor all of your code! #

![](Slimsy.png)

__Release Downloads__ 

NuGet Package: [![NuGet release](https://img.shields.io/nuget/v/Our.Umbraco.Slimsy.svg)](https://www.nuget.org/packages/Our.Umbraco.Slimsy/)
Umbraco Package: [![Our Umbraco project page](https://img.shields.io/badge/our-umbraco-orange.svg)](https://our.umbraco.org/projects/website-utilities/slimsy) 

__Prerelease Downloads__ 

NuGet Package: [![MyGet build](https://img.shields.io/myget/slimsy-v2/vpre/Our.Umbraco.Slimsy.svg)](https://www.myget.org/gallery/slimsy)

Umbraco Package (zip file): [![AppVeyor Artifacts](https://img.shields.io/badge/appveyor-umbraco-orange.svg)](https://ci.appveyor.com/project/JeavonLeopold/slimsy/build/artifacts) 

[![Build status](https://ci.appveyor.com/api/projects/status/a7rxrfkxc5dx8cuo?svg=true)](https://ci.appveyor.com/project/JeavonLeopold/slimsy)

**Note** Slimsy v2.0.0+ requires Umbraco v7.6.0+

LazySizes.js used in conjunction with ImageProcessor.Web and the built-in Umbraco Image Cropper will make your responsive websites images both adaptive and "retina" quality (if supported by the client browser).

Slimsy includes lazysizes.min.js and picturefill.min.js and some helper methods.


## Implementing post package installation

### 1. Add lazysizes.min.js & picturefill.min.js to your pages

In your master template add the  Javascript files

```
    <script src="/scripts/picturefill.min.js"></script>
    <script src="/scripts/lazysizes.min.js" async=""></script>
```

You can of course bundle these together. If you don't already have js bundling in place you should take a look at the [Optimus](http://our.umbraco.org/projects/developer-tools/optimus) package, it will allow you to bundle them together in minutes.

### 2. Adjust your image src attributes

Use the GetImgSrcSet or GetCropSrcSet methods on your media items. For these methods to function correctly your image property types should use the built-in **Image Cropper**.

#### GetImgSrcSet(width, height)
Use this method for setting the crop dimensions in your Razor code, assumes your image cropper property alias is "umbracoFile"

e.g. An initial image size of 270 x 161. This example is looping pages, each page has a media picker with property alias "Image"

    @foreach (var feature in featuredPages)
    {
        var featureImage = Umbraco.TypedMedia(feature.GetPropertyValue<int>("image"));
        <div class="3u">
            <!-- Feature -->
            <section class="is-feature">
                <img src="@featureImage.GetCropUrl(270, 161, quality:30)" data-srcset="@featureImage.GetImgSrcSet(270, 161)" data-src="@featureImage.GetCropUrl(270, 161)" sizes="auto" class="lazyload" />
            </section>
        </div>
    }

This example uses the LQIP (low quality image placeholder) technique.

#### GetCropSrcSet(cropAlias, propertyAlias)

Use this method when you want to use a predefined crop, assumes your image cropper property alias is "umbracoFile".

    @foreach (var feature in featuredPages)
    {
        <div class="3u">
            <section class="is-feature">
                @if (feature.HasValue("Image"))
                {
                    var featureImage = Umbraco.TypedMedia(feature.GetPropertyValue<int>("image"));
                    <a href="@feature.Url" class="image image-full">
                        <img data-srcset="@featureImage.GetCropSrcSet("home", "umbracoFile")" data-src="@featureImage.GetCropUrl("umbracoFile", "home")" sizes="auto" class="lazyload"/>
                    </a>
                }
                <h3><a href="@feature.Url">@feature.Name</a>
                </h3>
                @Umbraco.Truncate(feature.GetPropertyValue<string>("bodyText"), 100)
            </section>
        </div>
    }

# Using `<picture>` element

Below is an example of how to use the `<picture>` element to provide automated WebP versions of your images using the [ImageProcessor WebP plugin](http://imageprocessor.org/imageprocessor/plugins/#webp).

    @foreach (var feature in featuredPages)
    {
        var featureImage = Umbraco.TypedMedia(feature.GetPropertyValue<int>("image"));
        <div class="3u">
            <!-- Feature -->
            <section class="is-feature">

                <picture>
                    <!--[if IE 9]><video style="display: none"><![endif]-->
                    <source data-srcset="@featureImage.GetImgSrcSet(270, 161, "umbracoFile", "webp")" type="image/webp" data-sizes="auto"/>
                    <source data-srcset="@featureImage.GetImgSrcSet(270, 161)" type="image/jpeg" data-sizes="auto"/>
                    <!--[if IE 9]></video><![endif]-->
                    <img
                        src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw=="
                        data-src="@featureImage.GetCropUrl(270, 161)"
                        class="lazyload"
                        alt="image with artdirection"
                        data-sizes="auto"/>
                </picture>
            </section>
        </div>
    }

# Advanced Options

By default Slimsy will request that all images generated by ImageProcessor are jpg as this provides the best compression and optimisation, this is regardless of the source image type. You can disable this behaviour by adding the following appsetting to web.config.

    <add key="Slimsy:Format" value="false"/>

The above overloaded methods specifying outputFormat will of course take precedence over this default.

If you really wanted to have all default URLs return pngs you could also do this

    <add key="Slimsy:Format" value="png"/>

You can specify the default background color by added another appsetting to web.config. As an example this setting is used if ImageProcessor is converting a png to a jpg and it as some transparency

	<add key="Slimsy:BGColor" value="fff"/>

# Test Site & Source Code

A test site is included in the solution, the username and password for Umbraco are admin/password.
By default the test site is configured to use full IIS (due to IIS Express SQL CE persistence issue) on the domain slimsy.local, you can change it to use IIS Express if you prefer.

Visual Studio 2015 is required for compiling the source code

# Credits and references

This project includes [LazySizes](https://github.com/aFarkas/lazysizes) and [Picturefill](https://github.com/scottjehl/picturefill) Both projects are MIT licensed.

Many thanks to Douglas Robar for naming Slimsy.

# Change log

[Here](Changelog.md)