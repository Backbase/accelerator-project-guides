# How-to: add custom icons
Backbase Design System includes a component to handle and display icons, creating an iconographic subset of Google's Material Icons. These icons are not independent artefacts that can get easily replaced or extended. In fact, Material Icons is a compiled font family where each glyph is an image, making it very consistent, but may set rigid limits to some designers.

This guide aims to explore some approaches and explain how we solve this challenge in our subject.

## Adding different icons to the app

### Image file as an asset

A simple way of adding a new icon is adding it as a single image to the app. In some cases, this approach may be good enough. However, it has a couple of downsides, such as excessive requests to fetch the resources, no control whatsoever over the colour of the icon, necessity of services to handle the URL in multiple places.

Refer to the following documentation for more information on the implementation: [https://community.backbase.com/documentation/foundation\_angular/latest/manage\_static\_assets](https://community.backbase.com/documentation/foundation_angular/latest/manage_static_assets)

### Extend bb-icon to display image as a content

Another option is adding the image file to a component using CSS. In the same way, you can add characters as content to a pseudo-element, it is also possible to set the URL of an image file to render in it. With that, you can use bb-icon to render an SVG that is not part of its font family.

`.bb-icon { &-custom-one { content: url(../../assets/images/my-custom-icon.svg); } }`

However, this approach shares some of the disadvantages of static resources. Colours won't get ruled by CSS, its sizing may not get as expected, and each icon will request its file from the server.

### Create your own iconographic font-family

Like Material Icon has a font family file containing all the images, you can also generate a custom font family for your iconography. There are plenty of tools, such as the well-known "[![](./how-to-add-custom-icons-0.png)IcoMoon](http://icomoon.io) ", that you can use to convert your SVG files into TTF and WOFF.

However, whichever tool you choose, you must integrate it with your team workflow. That may be a disadvantage of "[http://icomoon.io](http://icomoon.io) ", as you need to rely on a third-party tool and maintain a separate project for it. For this user story, we researched alternatives, checking opensource tools to integrate into our SDLC.

[Svgtofont](https://www.npmjs.com/package/svgtofont "https://www.npmjs.com/package/svgtofont") was a great tool found that allow us to generate our package with frontend pipelines. It delivers the same result as "[http://icomoon.io](http://icomoon.io) " but is also fully integrated into the frontend SDLC.  
With this tool, it's possible to create a small CLI script that takes all SVG files in a given folder and converts them into font family files their font faces.

#### Usage instructions

1.  Place all the SVG files in a folder
    
2.  Create a script in your package.json like this:  
      
    
    `"custom-icons": "svgtofont --sources ./themes/cse-theme/assets/icons --output ./themes/cse-theme/assets/fonts/bb-icon-cse --fontName bb-icon-cse"`
    
3.  Once it generates the project in the specified output, import the SCSS file to the theme
    
4.  Add the icon with bb-icon-ui:  
      
    
    `<bb-icon-ui name="cse-my-custom-icon" class="align-middle"></bb-icon-ui>`
    

Name your font family as “bb-icon-{{ your project prefix }}”. This way you can make it work with the Design System Icon component.

The generated classes use the SVG file name, so name them wisely.

This tool require NodeJS 14.x

## Related articles

 

*   Page:
    
    [How-to: group steps horizontally](/wiki/spaces/CSE/pages/3333685249/How-to%3A+group+steps+horizontally)
    
*   Page:
    
    [\[Android\] How-to: Integrate a Flow Journey into the Productized App](/wiki/spaces/CSE/pages/3516760418)
    
*   Page:
    
    [How to: Enable multiple products selection](/wiki/spaces/CSE/pages/3499721196/How+to%3A+Enable+multiple+products+selection)
    
*   Page:
    
    [Add edit button to fields in Review screen](/wiki/spaces/CSE/pages/3471835233/Add+edit+button+to+fields+in+Review+screen)
    
*   Page:
    
    [How-to: add custom icons](/wiki/spaces/CSE/pages/3331981784/How-to%3A+add+custom+icons)