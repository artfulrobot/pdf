# Artful Robot PDF tools

Set of mostly command-line tools for working with PDFs. Many of these are
convenience wrappers around other (great) command line tools. Some are more
involved.

## Command line tools included here

* [arpdf_scale](#arpdf_scale) Scale page size to a5 (possibly any other)
* [arpdf_poster](#arpdf_poster) Enlarge PDF and split into multipage tiles for poster printing.
* [arpdf_add_header](#arpdf_add_header) Adds page headers/footers, inc. page numbers.
* [arpdf_boxes](#arpdf_boxes) modify (and inspect) art, crop, media boxes -prepress tool.
* [arpdf_from_images](#arpdf_from_images) Efficient way to put raster images into a pdf.
* [arpdf_from_documents](#arpdf_from_documents) make pdfs from .odt etc. files.
* [arpdf_shrink](#arpdf_shrink) gnarly tool to make low res small file size versions.

## Other tools, tips and notes

* *pdftk* extract, rearrange, splice, rotate (90) on a page basis.

...will document how to do other stuff..

## <a name="arpdf_scale"></a>arpdf_scale Scale PDF paper size

Say you designed an A4 page and you want to use it as an A5 flyer. You can scale
it as follows:

    % arpdf_scale -s a5 input.pdf output.pdf

See `arpdf_scale --help` for more.

## <a name="arpdf_poster"></a>arpdf_poster Create tiles to form a big poster

You have an A4 design you have an A4 printer and you want an A3 poster.

    % arpdf_poster input.pdf output.pdf

You can specify A2, A1, A0 too with the `-s` option

    % arpdf_poster -sA1 input.pdf output.pdf

This tool uses "poster" by default. You can also specify `-m pdfposter` to use
the "pdfposter" program instead, but I have found the poster one is more
reliable, and also has the benefit that it adds crop marks. (Nb. `pdfposter` is
included for convenience; probably better to use that command directly if
you want it.)

Requirements: poppler-utils poster pdfposter ghostscript

## <a name="arpdf_add_header"></a>arpdf_add_header Add header and footer text to
## a PDF

Example usage:

    % arpdf_add_header --header "This is my PDF" \
      --footer "Page %n of %N pages" --overwrite

This would add "This is my PDF" at the top of every page, and a page number at
the bottom.

Why is this useful? It was created to help with compiling papers for trustees.
PDFs would come from a variety of sources. I could use this once per file to add
a description of the agenda item the paper related to, then combine all the PDFs
in one, then use it on the final PDF to apply unified page numbers throughout
for easy reference in the meeting/minutes.

Requirements: perl libpdf-api2-perl

## <a name="arpdf_boxes"></a>arpdf_boxes Info on media, crop, bleed, trim boxes.

PDFs have various 'boxes' defined to help with prepress (mostly). This command
extracts info on these for you and presents it in an understandable way.
Example:

    % arpdf_boxes my.pdf
    Trimmed page size: 297.0 x 209.9 mm
    bleedOffsets: 0.0mm symetric
    cropOffsets: 0.0mm symetric

In brief:

* *media box* nothing outside of this should be rendered.
* *crop box* box that contains all that should be displayed/printed but
    not used for prepress.
* *trim box* this is the final intended page size.
* *bleed box* this is usually set to the cropbox and is the box outside of the
  trim box where content is defined for bleed. Useful for designing for press.

Note: there's junk code in here below an `exit` call! You can fairly easily hack
this about to change the box sizes, but I stopped short of implementing an
interface for this as there could be so many different things you might want to
do to a PDF.

See: [introduction to PDF box
meanings](http://www.prepressure.com/pdf/basics/page-boxes)

Requirements: perl libpdf-api2-perl


## <a name="arpdf_from_documents"></a>arpdf_from_documents Create PDFs
## from a set of files openable by OpenOffice/LibreOffice

(This was written when I used openoffice. I now use LibreOffice and I've not
tested it since. Would need to tweak this before use.)

Requirements: LibreOffice

## <a name="arpdf_from_images"></a>arpdf_from_images Create a PDF
## from a set of images, one per page.

Images are scaled to fit each page. The aim is to make the most efficiently
stored PDF. It's particularly useful for (and was probably written for) scanned
pages and includes options to make input files black and white.

Example take all scanned jpegs, convert them to black and white, save as
multipage PDF:

    % arpdf_from_images -bw -threshold 70% -quality printer \
    output.pdf *.jpg

Requirements: sam2p imagemagick pdftk ghostscript

## <a name="arpdf_scale"></a>arpdf_scale Decrease PDF filesize

This is a tool of desparate measure and can produce some truly horrid results.
But occasionally it does the job really well and sometimes filesize is all that
matters.

Example: attempt to shrink a PDF.

    % arpdf_shrink -r -q all input.pdf
    200KB input.pdf
    120KB input_screen.pdf
    130KB input_ebook.pdf
    180KB input_printer.pdf

Sometimes you'll need the `-k` option which keeps the colourspace as it is in
the original file. This is especially the case for the 'printer' preset.

It's a tool to help trial and error aproach; you inspect what it spits out and
decide to keep whichever one you want. The `--nasty` option lowers the
resolution of the images, for when you really want to crunch the thing down!

