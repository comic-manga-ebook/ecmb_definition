> [!WARNING]
> This project is still under developement, so please don't use it atm


## Homepage:
[https://comic-manga-ebook.github.io](https://comic-manga-ebook.github.io)


## About this repository:
This is the definition of the *.ecmb file-format! It also contains a validator done in python for validating *.ecmb - files

Published under [MIT License](https://choosealicense.com/licenses/mit/)

# Definition:
*.ecmb is basically a Zip-file containing a XML-file named "ecmb.xml" for the meta-data and all the images stored in a folder "content", organized in subfolders for using chapters. The cover-images are stored in the root of the Zip-file.
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

### ecmb.xml
All rules and requirements for "ecmb.xml" are defined in the XSD ([located here >](https://github.com/metacreature/ecmb_definition/tree/master/schema)), but unfortunately not everything:

**The navigation-links to the contents are relative!** An example says more than 1000 words ... [go to example >](https://github.com/metacreature/ecmb_definition/blob/master/examples/v1.0/advanced_book/advanced_book.ecmb_unpacked/ecmb.xml)

To clarify that more: a chapter links to a folder, that the reader-app can display which chapter you are currently reading. Of course you want to click on the chapter and the reader-app and therefore you have to provide a link to an image (href) which has to be part of the chapter. To enforce this the link to the image is relative to the chapter's folder. Btw. you can link to the 2nd image (or any you want as long its part of the chapter's folder), if eg. the first one is a spacer-image. Sub-chapters and links are also relative to its parent.

I know its a bit complicated, but its a no-go to mix content with navigation and the programs which should build the eBook would have massive problems, if you want to place links before the content is added.
Unfortunately I couldn't find a possibility to validate that directly with XSD, but of course the validator will check this. If you have an idea, please post it here: [https://stackoverflow.com/questions/77667931/cross-validation-of-contents-in-an-xml-using-xsd](https://stackoverflow.com/questions/77667931/cross-validation-of-contents-in-an-xml-using-xsd)

The **width and height** defined in the root-node **should** be the size of the images. It not exact, coz when I was building fan-translated Mangas, all images had a different size and aspect-ratio, **but** its enterly important for the validator to validate the correct placement of double-page-images.

### Double-page-images
Double-page-image have to be stored full and the splitted left and right part seperatly to give the reader-app more opportunities. 

In the "ecmb.xml"'s content-node those images have to be linked with `<dimg src="full.jpg" src_left="left.jpg" src_right="right.jpg" />`. The validator will check if you did a mistake with the image-size, eg. linking a double-page-image in a normal img-tag.

If you link to a double-page-image in the navigation you have to address the src-attribute, and have to specify either #auto, #left or #right. eg. `<chapter label="Chapter 1" dir="/content/chap1" href="/full.jpg#left" />`. Using "#auto, #left or #right" is mandatory for double-page-images, while using this for single-page-images this is forbidden. The XSD can't check this, but of course the validator does.


# Validator
The validator of course validates the XML first. After that it manually checks if there are dead links in the navigation (If you have an idea doing this directly in XSD, please post it here: [https://stackoverflow.com/questions/77667931/cross-validation-of-contents-in-an-xml-using-xsd](https://stackoverflow.com/questions/77667931/cross-validation-of-contents-in-an-xml-using-xsd)).
If you validate a *.ecmb (you can validate a single XML-file too) the validator then checks if all images linked in content are available. Finally it will check if all double-page-images are placed correctly.

### For developers
If you are doing your own library to create an *.ecmb in your prefered programming-language its highly recommended to validate it after the creation. My reader-app will validate it and won't display it if its invalid!

## Using the Python-Validator
### Installation
- download and install Python3 [https://www.python.org/downloads/](https://www.python.org/downloads/)
- download and install Git [https://git-scm.com/downloads](https://git-scm.com/downloads)
- open the git-console and then
    - clone this repositiory `git clone git@github.com:metacreature/ecmb_definition.git`
    - go to the validator-folder `cd ecmb_definition/validator/python/`
    - run `pip install -r requirements.txt`
 
### Using it
- open the git-console and then
    - go to the validator-folder `cd ecmb_definition/validator/python/`
    - run `invoke validate [absolute or relative path to your file]`

Example:
```
$ cd ecmb_definition/validator/python/
$ invoke validate ../../examples/v1.0/advanced_book/advanced_book.ecmb

    File is valid!

$
```


