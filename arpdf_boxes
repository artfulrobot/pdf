#!/usr/bin/perl -w
# Requires: libpdf-api2-perl
# Also, see http://www.prepressure.com/pdf/basics/page-boxes
# and podofo* utils do more than this script does...
use strict;
use PDF::API2;
use Data::Dumper;
use constant mm => 25.4 / 72;
use constant in => 1 / 72;
use constant pt => 1;

print "opening $ARGV[0]\n";
my $pdf = PDF::API2->open ($ARGV[0]);
my $page = $pdf->openpage (1);

my @mediaBox = map $_*mm, $page->get_mediabox();
my @cropBox = map $_*mm, $page->get_cropbox();
my @bleedBox = map $_*mm, $page->get_bleedbox();
my @trimBox = map $_*mm, $page->get_trimbox();

my ($widthT, $heightT) = ( $trimBox[2]-$trimBox[0], $trimBox[3]-$trimBox[1] );

printf "Trimed page size: %0.1f x %0.1f mm\n", $widthT, $heightT;
# now how much bleed?
my @bleedOffsets =  ( $trimBox[0]-$bleedBox[0], $trimBox[1]-$bleedBox[1],$bleedBox[2]-$trimBox[2],$bleedBox[3]-$trimBox[3]);
my $symetric = 1;
for ( 0 .. 2 ) { $symetric = 0 unless sprintf('%0.1f',$bleedOffsets[$_]) == sprintf('%0.1f',$bleedOffsets[$_+1]); }
if ($symetric) { printf "bleedOffsets: %0.1f mm symetric\n", $bleedOffsets[0]; }
else { printf "bleedOffsets: %0.9f, %0.9f, %0.9f, %0.9f mm\n", @bleedOffsets; }

$symetric = 1;
my @cropOffsets =  ( $bleedBox[0]-$cropBox[0], $bleedBox[1]-$cropBox[1],$cropBox[2]-$bleedBox[2],$cropBox[3]-$bleedBox[3]);
for ( 0 .. 2 ) { $symetric = 0 unless sprintf('%0.1f',$cropOffsets[$_]) == sprintf('%0.1f',$cropOffsets[$_+1]); }
if ($symetric) { printf "cropOffsets: %0.1f mm symetric\n", $cropOffsets[0]; }
else { printf "cropOffsets: %0.9f, %0.9f, %0.9f, %0.9f mm\n", @cropOffsets; }

# checks, these should be the same
# printf "trim + bleed + crop width: %0.1f cf media width %0.1f\n", ( $bleedOffsets[0] + $bleedOffsets[2] + $cropOffsets[0] + $cropOffsets[2] + $widthT), $mediaBox[2];
# printf "trim + bleed + crop height: %0.1f cf media height %0.1f\n", ( $bleedOffsets[1] + $bleedOffsets[3] + $cropOffsets[1] + $cropOffsets[3] + $heightT), $mediaBox[3];



exit;
my @artBox = map $_*mm, $page->get_artbox();

print "bleedBox: " . Dumper \@bleedBox ;
print "trimBox: " . Dumper \@trimBox ;
print "mediaBox (mm): " . Dumper \@mediaBox ;
print "cropBox: " . Dumper \@cropBox ;
print "artBox: " . Dumper \@artBox ;

$page->cropbox (@mediaBox);
#$pdf->saveas( "_$ARGV[0]");


