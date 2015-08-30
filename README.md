# Artful Robot PDF tools

Set of mostly command-line tools for working with PDFs. Many of these are
convenience wrappers around other (great) command line tools. Some are more
involved.

## Command line tools included here

* *arpdf_add_header.pl* Adds page headers/footers, inc. page numbers.
* *arpdf_boxes* modify (and inspect) art, crop, media boxes -prepress tool.
* *arpdf_scale* Scale page size to a5 (possibly any other)
* *arpdf_from_images* Efficient way to put raster images into a pdf.
* *arpdf_from_documents* make pdfs from .odt etc. files.
* *arpdf_shrink* gnarly tool to make low res small file size versions.

## Other tools, tips and notes

* *pdftk* extract, rearrange, splice, rotate (90) on a page basis.

...will document how to do other stuff..

## Scale PDF paper size

Say you designed an A4 page and you want to use it as an A5 flyer. You can scale
it as follows:

    % arpdf_scale -s a5 input.pdf output.pdf

See `arpdf_scale --help` for more.



