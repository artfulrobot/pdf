#!/usr/bin/perl -w
use strict;

# fred.pdf				fred.ps
#						_tmp.pdf
# _tmp.ps				_tmp.ps
# fred_screen.pdf		fred_screen.pdf	


my $usage = "Usage: $0 [-r|--report] [-n|--nasty] [-q option[,option[,option...]]] input.pdf [input.pdf...]\n" .
	"\t--report\n\t-r\tReport finished sizes\n" .
	"\t--keep-colours\n\t-k\tDon't change colourspace.\n" .
	"\t--nasty\n\t-n\tNasty: use lower resolutions.\n" .
	"\t-q\tQualities: comma-separated list. Valid options are screen,ebook,printer or all\n";

die $usage if (@ARGV < 2);

# declare vars:
my ( $report, $nasty,$cols ) = (0,0,0);
our $q='all';
our %resolutions=();

sub process # {{{
{
	# remove .pdf, .ps
	my ($filename, $prefix, $psSource, $dir) = ($_[0],$_[0],0,$_[0]);
	$prefix =~ s/\..*?$//;
	$dir =~ s{^(.*/).*?$}{$1};
	# print "entering dir: $dir\n";
	chdir $dir;
	my $size=`stat -c%s "$filename"`;
	my $unit='KB';
	$size /= 1024;
	if ( $size>1024) 
	{	
		$size /= 1024;
		$unit='MB';
	}
	printf "%0.1f%s\t%s\n",$size,$unit, "$filename" if ($report);

	# is it postscript?
	if ( $filename =~ m/\.ps$/ )
	{
		# yes, make initial high quality pdf called _tmp.pdf
		# print "extra conversion for source postscript file: $prefix.ps\n";
		system( 'ps2pdf14', '-dPDFSETTINGS=/default',$filename,'_tmp.pdf');
		$filename = '_tmp.pdf';
		$psSource = 1;
	}
	
	# create level3 postscript
	# print "preparing postscript file $filename\n";
	system( 'pdftops','-level3',$filename, '_tmp_psl3.ps');
	foreach (split (/,/, $q))
	{
		# print "doing $_ quality for $prefix...\n";
		
		my @cmd = ( 'gs',
			qw{-q -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dEmbedAllFonts=true -dSubsetFonts=true -dColorImageDownsampleType=/Bicubic -dGrayImageDownsampleType=/Bicubic -dMonoImageDownsampleType=/Bicubic},
			"-dPDFSETTINGS=/$_",
			"-dColorImageResolution=".$resolutions{$_}{'tonal'},
			"-dGrayImageResolution=".$resolutions{$_}{'tonal'},
			"-dMonoImageResolution=".$resolutions{$_}{'bw'},
			"-sOutputFile=${prefix}_$_.pdf");
		push @cmd,'-dColorConversionStrategy=/LeaveColorUnchanged' if $cols;
		push @cmd,'_tmp_psl3.ps';
		# xxxx print "@cmd\n";
		my $out = system @cmd;
		my $size=`stat -c%s "${prefix}_$_.pdf"`;
		my $unit='KB';
		$size /= 1024;
		if ( $size>1024) 
		{	
			$size /= 1024;
			$unit='MB';
		}
		printf "%0.1f%s\t%s\n",$size,$unit, "${prefix}_$_.pdf" if ($report);
		die '**Exiting due to errors running ghostscript**' unless $out == 0;
	}

	print "\n" if ( $report);
#	unlink ('_tmp_psl3.ps'); unlink ('_tmp.pdf') if $psSource;

} # }}}

my @files = ();

while ($_ = shift @ARGV )
{
	if ( $_ eq '-r' 	|| $_ eq '--report'	) { $report = 1; }
	elsif ( $_ eq '-n' 	|| $_ eq '--nasty'	) { $nasty=1; }
	elsif ( $_ eq '-k' 	|| $_ eq '--keep-colours'	) { $cols=1; }
	elsif ( $_ eq '-q' ) 
	{
		# quality
		$q = shift @ARGV;
		my $all =0;
		foreach (split (/,/ , $q))
		{
			die $usage unless m/^(prepress|screen|ebook|printer|all)$/i;
			$all = m/^all$/i;
		}
		$q = 'screen,ebook,printer' if ($all);
	}
	elsif ( substr($_,0,1) eq '-' ) { die $usage; }
	else { push @files, $_; }
}
die $usage unless (@files>0);
foreach (@files) { die "file: $_ doesn't exist\n" unless -f $_ ; }

if ($nasty)
{
	$resolutions{'screen'}{'tonal'}=72;
	$resolutions{'screen'}{'bw'}=300;
	$resolutions{'ebook'}{'tonal'}=72;
	$resolutions{'ebook'}{'bw'}=300;
	$resolutions{'printer'}{'tonal'}=250;
	$resolutions{'printer'}{'bw'}=300;
	$resolutions{'prepress'}{'tonal'}=600;
	$resolutions{'prepress'}{'bw'}=1200;
}
else
{
	$resolutions{'screen'}{'tonal'}=120;
	$resolutions{'screen'}{'bw'}=300;
	$resolutions{'ebook'}{'tonal'}=120;
	$resolutions{'ebook'}{'bw'}=300;
	$resolutions{'printer'}{'tonal'}=300;
	$resolutions{'printer'}{'bw'}=600;
	$resolutions{'prepress'}{'tonal'}=600;
	$resolutions{'prepress'}{'bw'}=1200;
}

process $_ foreach (@files) ;
