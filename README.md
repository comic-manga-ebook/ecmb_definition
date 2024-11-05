## *.ecmb - The new Comic-Manga-eBook 

### Homepage: [https://github.com/comic-manga-ebook/](https://github.com/comic-manga-ebook/)

### Table of contents
- [About this repository](#about-this-repository)
- [Definition](#definition)
  - [File-structure](#file-structure)
  - [Allowed image-types](#allowed-image-types)
  - [Organize the content](#organize-the-content)
  - [ecmb.xml](#ecmbxml)
  - [Navigation](#navigation)
  - [Double-page-images](#double-page-images)
  - [Finally](#finally)
- [Validator](#validator)
  - [For developers](#for-developers)
- [Using the Python-Validator](#using-the-python-validator)
  - [Installation](#installation)
  - [Using it](#using-it)


## About this repository
This is the definition of the *.ecmb file-format! It also contains a validator done in python for validating *.ecmb - files.

Published under [MIT License](https://choosealicense.com/licenses/mit/)

**Copyright:**

Copyright (c) 2023 Clemens K. (https://github.com/metacreature)

**If you like it, I would be happy if you  [donate on checkya](https://checkya.com/1hhp2cpit9eha/payme)**<br /><br />

## Definition
*.ecmb is basically a Zip-file containing an XML-file named "ecmb.xml" for the meta-data and all the images stored in a folder "content", organized in subfolders for using chapters. The cover-images are stored in the root of the Zip-file.
Beside the meta-data, the "ecmb.xml" contains the spine and of course a navigation which gives you many opportunities.
### File-structure:
```
myfile.ecmb
    ˪ ecmb.xml
    ˪ cover.jpeg
    ˪ content/
        ˪ chapter1/
            ˪ page1.jpeg
            ˪ page2.jpeg
```
### Allowed image-types:
jpeg, jpg, png, webp - I would recommend to use webp

### Organize the content
If you want to use chapters (and subchapters) in navigation you have to organize the images in folders, coz a chapter points to a folder. You can add images directly to the root as well for example the introduction, table of contents, spacer-images between the chapters, … 

Chapters with an uneven page-count are supported, also double-pages on an even page and uneven page-count of the book …

### ecmb.xml
All rules and requirements for "ecmb.xml" are defined in the XSD ([located here >](https://github.com/metacreature/ecmb_definition/tree/master/schema)), but unfortunately not everything:

**The navigation-links to the contents are relative!** An example says more than 1000 words ... [go to example >](https://github.com/metacreature/ecmb_definition/blob/master/examples/v1.0/advanced_book/advanced_book.ecmb_unpacked/ecmb.xml)

To clarify that more: a chapter links to a folder, that the reader-app can display which chapter you are currently reading. Of course you want to click on the chapter in the reader-app and therefore you have to provide a link to an image (href) which has to be part of the chapter. To enforce this the link to the image is relative to the chapter's folder. Btw. you can link to the 2nd image (or any you want as long it is part of the chapter's folder), if e.g. the first one is a spacer-image. Subchapters and links are also relative to it is parent.

I know it is a bit complicated, but it is a no-go to mix content with navigation and the programs which should build the eBook would have massive problems, if you want to place links before the content is added.
Unfortunately I couldn't find a possibility to validate that directly with XSD, but of course the validator will check this. If you have an idea, please post it here: [https://stackoverflow.com/questions/77667931/cross-validation-of-contents-in-an-xml-using-xsd](https://stackoverflow.com/questions/77667931/cross-validation-of-contents-in-an-xml-using-xsd)

The **width and height** defined in the root-node **should** be the size of the images. It is not exact, coz when I was building fan-translated Mangas, all images had a different size and aspect-ratio, **but** the aspect-ratio is entirely important for the validator to validate the correct placement of double-page-images `(Formula: id_double = (img_width / img_height) > (book_width / book_height * 1.5))`

Use the **original**-node if the book was (fan-) translated. It is recommended to add the authors to the original book, and leave them empty at main meta-data.

If the book based on e.g. a light-novel and you want to give that credit, you can place the information at the **basedon**-node.

### Navigation
Navigation is optional. If you don’t add it the app will automatically generate a navigation based on the folder-structure.

You can use headlines, links to images and chapters. Chapters point to folders and you have to specify an image to start with. This is useful if the first image of the chapter is e.g. a spacer-image or if there is a prologue and you want to point to the chapter's title-image if the user clicks on the chapter-link.

### Double-page-images
Double-page-image have to be stored in the "dimg"-tag (`<dimg src="doublepage.jpg" />`)  while single-paged images have to be stored in the "img"-tag (`<img src="singlepage.jpg" />`)

The validator will check for incorrectly positioned images.

If you link to a double-page-image in the navigation you have to address the src-attribute, and have to specify either #auto, #left or #right. e.g. `<chapter label="Chapter 1" dir="/content/chap1" href="/doublepage.jpg#left" />`. Using "#auto, #left or #right" is mandatory for double-page-images, while using this for single-page-images this is forbidden. The XSD can't check this, but of course the validator does.

### Finally
Maybe ecmblib's documentation is also helpful for you: [https://comic-manga-ebook.github.io/ecmblib](https://comic-manga-ebook.github.io/ecmblib)<br /><br />

## Validator
The validator of course validates the XML first. After that it manually checks if there are dead links in the navigation (If you have an idea doing this directly in XSD, please post it here: [https://stackoverflow.com/questions/77667931/cross-validation-of-contents-in-an-xml-using-xsd](https://stackoverflow.com/questions/77667931/cross-validation-of-contents-in-an-xml-using-xsd)).
If you validate a *.ecmb (you can validate a single XML-file too) the validator then checks if all images linked in content are available. Finally it will check if all double-page-images are placed correctly.

### For developers
If you are doing your own library to create an *.ecmb in your preferred programming-language it is highly recommended to validate it after the creation. My reader-app will validate it and won't display it if it is invalid!<br /><br />

## Using the Python-Validator
### Installation
- download and install Python3 [https://www.python.org/downloads/](https://www.python.org/downloads/)
- download or clone this repository
- open the console and then
    - go to the validator-folder `cd ecmb_definition/validator/python/`
    - run `pip install -r requirements.txt`
 
### Using it
- open the console and then
    - go to the validator-folder `cd ecmb_definition/validator/python/`
    - run `invoke validate [absolute or relative path to your file]`

Example:
```
$ cd ecmb_definition/validator/python/
$ invoke validate ../../examples/v1.0/advanced_book/advanced_book.ecmb

    File is valid!

$
```


