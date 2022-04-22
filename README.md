# Latex-tutorial

## Makefile for latex compilation

[Click](https://gitlab.com/frasottile/latex-and-makefile/-/blob/master/Makefile)

```
PROJECT = 'latex_and_Makefile' 
MAIN = article
TEX_SOURCES = Makefile \
              $(MAIN).tex \
                section1.tex \
                section2.tex \
                section3.tex \
              references.bib \
              mystyle.sty 

FIGURES := $(shell find images/* -type f)

SHELL=/bin/bash
DATE = $(shell date +"%d%b%Y")
OPT = --interaction=nonstopmode

all: $(MAIN).pdf

final: final.pdf
	$(MAKE) clean

final.pdf: cover.pdf $(MAIN).pdf 
	pdftk cover.pdf $(MAIN).pdf cat output final.pdf

$(MAIN).pdf: $(TEX_SOURCES) $(FIGURES)
	pdflatex $(MAIN)
	makeindex $(MAIN)
	bibtex $(MAIN)
	pdflatex $(MAIN)
	pdflatex $(MAIN)
	@#latexmk -pdf -pvc -pdflatex="pdflatex $(OPT)" $(MAIN) 


once: 
	pdflatex $(MAIN)

clean: 
	-rm -f $(MAIN).{pdf,log,blg,bbl,aux,out,toc,idx,bcf,run.xml,ind,ilg,fls,fdb_latexmk} final.pdf

targz:
	$(MAKE) clean
	$(MAKE) all
	$(MAKE) clean
	tar czf $(PROJECT)_$(DATE).tgz $(TEX_SOURCES) $(FIGURES) cover.pdf

zip:
	$(MAKE) clean
	$(MAKE) all
	$(MAKE) clean
	zip -q $(PROJECT)_$(DATE).zip $(TEX_SOURCES) $(FIGURES) cover.pdf
	
.PHONY: clean all
```

## How to create own package

```
\ProvidesPackage{mystyle}
\usepackage[margin=2cm,bottom=3cm]{geometry}
\usepackage{graphicx}
\usepackage{lipsum}
\usepackage{makeidx}
\makeindex
\usepackage[backend=bibtex,style=phys]{biblatex}
\addbibresource{references.bib}
\author{Francesco}
\title{Compiling \LaTeX\ projects :: use of Makefile \includegraphics[width=1cm]{images/splash.png}
```

## LaTeX/Tips and Tricks
[Click](https://en.wikibooks.org/wiki/LaTeX/Tips_and_Tricks)

- [x] Spell-checking and Word Counting

If you want to spell-check your document, you can use the command-line aspell, hunspell (preferably), or ispell programs.

  ```
ispell yourfile.tex
aspell --mode=tex -c yourfile.tex
hunspell -l -t -i utf-8 yourfile.tex
```

## Demo mode for images

```
\usepackage[demo]{graphicsx}
```

## List of figures, list of tables, and table of contents

```
\tableofcontents
\listoffigures
\listoftables
```

## Leaving something out of the Table of Contents

If you insert an asterisks after chapter, it will leave it off of the table of contents:

```
\chapter*{Super Cool Chapter}
```

## Adjusting the Table of Contents depth

The automatic table of contents is nice but by default it shows everything: chapters, sections, sub sections, sub sub sections, etc.  You can change this by using:

```
\setcounter{tocdepth}{1}
```

0 means only chapters, 1 includes sections, 2 includes subsections, etc.

## PDF links

```
\usepackage{hyperref}
```
This can sometimes cause problems if you use labels for figures.  This can be fixed by putting this line in the preamble:
```
\AtBeginDocument{\let\textlabel\label}
```

## Float Barrier

```
\FloatBarrier (capitalization important) 
````
that basically says, any floats that were included above this point in the code cannot appear after this line. 

## Multiple Captions

```
\caption[This appears in the list of figures]{This appears under the figure.}
```

## Add to Table of Contents

```
\addcontentsline{toc}{chapter}{Bibliography}
```

For more informatios click the [link.](https://thewanderingengineer.com/2015/06/23/latex-tips-and-tricks-for-thesis-writing/)

## How to have a normal line of text inside a math environment and align it correctly in LaTeX?

- [x] This sounds like a job for `\intertext` from amsmath:

```
\documentclass{article}

\usepackage{mathtools}

\begin{document}


\begin{align}
  \tilde{w}_{ij} &=
    \begin{cases}
      w_{ij} & \text{mit $P(i)$}\\
      0 & \text{sonst}
    \end{cases}\\       
\intertext{This text be on the far left}
  \tilde{w}_{ij} &= w_{ij} * P(i)      
\end{align}

\end{document}
```

## How to embed a video on a beamer slide?

First ddd the below segment in the preamble.

```
%------------------------------------------------------------
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% \embedvideo{<poster or text>}{<video file (MP4+H264)>}
% \embedvideo*{...}{...}                     % auto-play
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\usepackage{hyperref}
\usepackage{pdfbase}
\ExplSyntaxOn
\NewDocumentCommand\embedvideo{smm}{
  \group_begin:
  \leavevmode
  \tl_if_exist:cTF{file_\file_mdfive_hash:n{#3}}{
    \tl_set_eq:Nc\video{file_\file_mdfive_hash:n{#3}}
  }{
    \IfFileExists{#3}{}{\GenericError{}{File~`#3'~not~found}{}{}}
    \pbs_pdfobj:nnn{}{fstream}{{}{#3}}
    \pbs_pdfobj:nnn{}{dict}{
      /Type/Filespec/F~(#3)/UF~(#3)
      /EF~<</F~\pbs_pdflastobj:>>
    }
    \tl_set:Nx\video{\pbs_pdflastobj:}
    \tl_gset_eq:cN{file_\file_mdfive_hash:n{#3}}\video
  }
  %
  \pbs_pdfobj:nnn{}{dict}{
    /Type/RichMediaInstance/Subtype/Video
    /Asset~\video
    /Params~<</FlashVars (
      source=#3&
      skin=SkinOverAllNoFullNoCaption.swf&
      skinAutoHide=true&
      skinBackgroundColor=0x5F5F5F&
      skinBackgroundAlpha=0.75
    )>>
  }
  %
  \pbs_pdfobj:nnn{}{dict}{
    /Type/RichMediaConfiguration/Subtype/Video
    /Instances~[\pbs_pdflastobj:]
  }
  %
  \pbs_pdfobj:nnn{}{dict}{
    /Type/RichMediaContent
    /Assets~<<
      /Names~[(#3)~\video]
    >>
    /Configurations~[\pbs_pdflastobj:]
  }
  \tl_set:Nx\rmcontent{\pbs_pdflastobj:}
  %
  \pbs_pdfobj:nnn{}{dict}{
    /Activation~<<
      /Condition/\IfBooleanTF{#1}{PV}{XA}
      /Presentation~<</Style/Embedded>>
    >>
    /Deactivation~<</Condition/PI>>
  }
  %
  \hbox_set:Nn\l_tmpa_box{#2}
  \tl_set:Nx\l_box_wd_tl{\dim_use:N\box_wd:N\l_tmpa_box}
  \tl_set:Nx\l_box_ht_tl{\dim_use:N\box_ht:N\l_tmpa_box}
  \tl_set:Nx\l_box_dp_tl{\dim_use:N\box_dp:N\l_tmpa_box}
  \pbs_pdfxform:nnnnn{1}{1}{}{}{\l_tmpa_box}
  %
  \pbs_pdfannot:nnnn{\l_box_wd_tl}{\l_box_ht_tl}{\l_box_dp_tl}{
    /Subtype/RichMedia
    /BS~<</W~0/S/S>>
    /Contents~(embedded~video~file:#3)
    /NM~(rma:#3)
    /AP~<</N~\pbs_pdflastxform:>>
    /RichMediaSettings~\pbs_pdflastobj:
    /RichMediaContent~\rmcontent
  }
  \phantom{#2}
  \group_end:
}
\ExplSyntaxOff
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
```

Then inclde a frame as below -

```
 Here I embed a movie in my slide
%--------------------------------------------------------
\begin{frame}
\frametitle{Quantum molecular dynamics of water}
%\section{autoplay}
\centering
\embedvideo*{\includegraphics[page=1]{example-movie}}{/Users/tsahoo/Documents/write-ups-for-academic-positions/images/ice_freezing_video_small.mov}
%\section{play on click}
%\embedvideo{\includegraphics[page=1]{example-movie}}{/Users/tsahoo/Documents/write-ups-for-academic-positions/images/ice_freezing_video_small.mov}
\end{frame}
%--------------------------------------------------------
```

## How to open a .pdf on `acrobat reader` from command line?

```
open -a Adobe\ Acrobat talk-Tapas-Sahoo.pdf
```
## Beamer references for placing the references at footnote

```
\documentclass[10pt]{beamer}

\usetheme[width=4em]{PaloAlto}
\usecolortheme{crane}

\addtobeamertemplate{footnote}{\vspace{-6pt}\advance\hsize-0.5cm}{\vspace{6pt}}
\makeatletter
% Alternative A: footnote rule
\renewcommand*{\footnoterule}{\kern -3pt \hrule \@width 2in \kern 8.6pt}
% Alternative B: no footnote rule
% \renewcommand*{\footnoterule}{\kern 6pt}
\makeatother

\usepackage[utf8]{inputenc}
\usepackage[spanish,es-tabla]{babel}  
\usepackage[style=verbose,autocite=footnote]{biblatex}

\usepackage{filecontents}

\begin{filecontents}{\jobname.bib}
@Book{Taflove2000,
    Day = {30},
    Edition = {2},
    Month = {jun},
    Publisher = {Artech House Publishers},
    author = {Taflove, Allen and Hagness, Susan C.},
    title = {Computational Electrodynamics: The Finite-Difference Time-Domain Method},
    year = {2000},
}
\end{filecontents}

\addbibresource{\jobname.bib}

\begin{document}

\begin{frame}{The FDTD Method}
  \begin{columns}
    \begin{column}{0.4\textwidth}
      \begin{itemize}
      \item Discretization of both space and time by central differences.
      \item Yee Cell.\footnotemark 
      \end{itemize}
    \end{column}

    \begin{column}{0.5\textwidth}
      \begin{figure}
      \centering
      \rule{0.9\columnwidth}{2cm}% placeholder for graphic
      \caption{Unit Yee Cell.}
      \end{figure}
    \end{column}
  \end{columns}
\footcitetext{Taflove2000}
\end{frame}

\end{document}
```

For more information [see](https://tex.stackexchange.com/questions/44217/how-can-i-stop-footcite-from-hijacking-my-beamer-columns)




## [Top 5 Beamer Tips](https://nhigham.com/2013/01/18/top-5-beamer-tips/)

- [x] Navigation Symbols

I’ve seen hundreds of talks with slides prepared in Beamer. I’ve yet to see anyone use the navigation symbols that Beamer puts at the bottom right-hand corner of the screen. These should be turned off with

```
\setbeamertemplate{navigation symbols}{}
```

- [x] Columns

A good way to line up graphics side by side, especially if they are of different heights, is with Beamer’s columns environment:


```
\begin{columns}[c] % Columns centered vertically.
\column{5.5cm}     % Adjust column width to taste.
\includegraphics ...
\column{5cm}
\includegraphics ...
\end{columns}
```

- [x] Extra Slides

If you include extra slides at the end of your talk, you can stop them affecting the page count (usually shown in the footer) as follows:

```
\usepackage{appendixnumberbeamer} 
...
\appendix 
\begin{frame}{First Extra slide}
...
```

The appendixnumberbeamer package might not be included in your LaTeX distribution, in which case you can download it from [CTAN](https://www.ctan.org/pkg/appendixnumberbeamer).

- [x] References

You can include references in your slides using the following code, best placed after \appendix:

```
\begin{frame}[allowframebreaks]{References}
\def\newblock{}
\bibliographystyle{plain}
\bibliography{mybib}
\end{frame}
```
- [x] Sections

In a longer talk it can be useful to break the talk into sections and produce an outline slide at the start of each section. This can be done by putting in the preamble the code

```
\setbeamerfont{myTOC}{series=\bfseries,size=\Large}
\AtBeginSection[]{\frame{\frametitle{Outline}%
                  \usebeamerfont{myTOC}\tableofcontents[current]}}
```

and then typing `\section[short_title]{long_title}` between frames, where `short_title` is used for the headline (the line at the top of the screen that can contain various types of information, intended to help the audience know where you are in the talk). In this code I have increased the size of the font used for the outline and made it bold. If you don’t want the headline it can be turned off with

```
\setbeamertemplate{headline}{}
```
## Cross out with arrow as in "goes to zero" [duplicate]

- [x] Invoke the package `\usepackage{cancel}` then use the following command `\cancelto{0}{x}` in math mode where `x` is being cancelled to the number zero. A `\cancel{x}` cross the term out without any number.

## Text under equation

```
\documentclass[12pt]{article}
\pagestyle{plain}
\usepackage[margin=1.8cm]{geometry}
\geometry{a4paper}
\usepackage[parfill]{parskip}
\usepackage{amsmath}
\usepackage{amssymb}
\usepackage[usenames]{color}
\definecolor{cyan}{rgb}{0,1,1}
\newcommand{\cyan}[1]{\textcolor{cyan}{#1}}
\definecolor{black}{rgb}{0,0,0}
\newcommand{\black}[1]{\textcolor{black}{#1}}

\begin{document}

\[
  \text{read stall cycles} =
  \cyan{\underbrace{\black{\frac{\text{reads}}{\text{program}}
        \times \parbox{48pt}{read miss \\ rate (\%)}}}_{\text{read
        miss per program}}} \times
  \cyan{\underbrace{\black{\parbox{76pt}{read miss \\ penalty
          (cycle)}}}_{\parbox{62pt}{\scriptsize\centering number of cycles \\
        per read miss}}}
\]

\end{document}
```

See also 

```
\documentclass{article}
\usepackage{amsmath}
\usepackage{xcolor}

\definecolor{underbrace}{RGB}{30,199,166}

\newcommand{\textfrac}[1]{%
  \begin{tabular}{@{}l@{}}#1\end{tabular}%
}

\begin{document}
\[
\colorlet{currentcolor}{.}
\text{read stall cycles}=
  \textcolor{underbrace}{%
    {\underbrace{%
       \textcolor{currentcolor}{%
         \textfrac{reads\\\hline program}
         \times
         \textfrac{read miss\\[\arrayrulewidth]rate (\%)}
       }
     }_{\text{read miss per program}}
    }
  }
  \times
  \textcolor{underbrace}{%
    {\underbrace{%
       \textcolor{currentcolor}{%
         \textfrac{read miss\\[\arrayrulewidth]penalty (cycle)}
       }
     }_{\substack{\text{number of cycles}\\\text{per read miss}}}
    }
  }
\]

\end{document}
```
# Important tricks for Tikz

## [PGFPlots Gallery](http://pgfplots.sourceforge.net/gallery.html)

- [x] See also [1](https://towardsdatascience.com/how-to-create-publication-ready-plots-with-latex-4a095eb2f1bd)

- [x] [Various options of tikz plot](How to Create Publication-Ready Plots with LaTeX)

## [How to Plot a Function and Data in LaTeX](https://latexdraw.com/plot-a-function-and-data-in-latex/)

Here is a piece of code that uses the above parameters: 

```
\documentclass{standalone}
 
\usepackage{pgfplots}
 
\pgfplotsset{compat = newest}
 
\begin{document}
 
\begin{tikzpicture}
 
\begin{axis}[
    xmin = 0, xmax = 30,
    ymin = -1.5, ymax = 2.0,
    xtick distance = 2.5,
    ytick distance = 0.5,
    grid = both,
    minor tick num = 1,
    major grid style = {lightgray},
    minor grid style = {lightgray!25},
    width = \textwidth,
    height = 0.5\textwidth]
    \addplot[
        domain = 0:30,
        samples = 200,
        smooth,
        thick,
        blue,
    ] {exp(-x/10)*( cos(deg(x)) + sin(deg(x))/10 )};
\end{axis}
 
\end{tikzpicture}
\end{document}
```
# Guidelines for Biblatex customerization

## [Top BibTeX Tips](https://nhigham.com/tag/bibtex/)

## If one needs not to let some fields be visible in the reference list, use the below statements in the preamble.

```
\AtEveryBibitem{\clearfield{month}}
\AtEveryCitekey{\clearfield{month}}
\AtEveryBibitem{\clearfield{issue}}
\AtEveryCitekey{\clearfield{issue}}
\AtEveryBibitem{\clearfield{number}}
\AtEveryCitekey{\clearfield{number}}
```
## How to adjust the breaking in the bibliography?

- [x] See [it](https://tex.stackexchange.com/questions/442308/how-to-adjust-the-breaking-in-the-bibliography)

## Tips on Writing a Thesis in LaTeX

- [x] See [it](http://www.khirevich.com/latex/biblatex/)
- [x] [Most important one](https://tex.stackexchange.com/questions/12806/guidelines-for-customizing-biblatex-styles)

## Using biblatex to conveniently highlight your author name

biblatex, the follow on to the venerable bibtex, offers some new functionality that may be useful for CVs. That is, it is typical in CV publication lists to highlight your name. Traditionally, you would hack the generated bibtex BBL files to do that, which can be time consuming and error prone, even when using scripts. You can also use LaTeX to, say, embolden your name directly into a bibtex database entry, but that's a brittle approach as that runs the risk of breaking bibtex database parsing.

However, biblatex now supports "annotations" that allow you to highlight your name in bibliographies without intrusive hacking on the bibtex database or in the generated BBL.

So, for example, in the technical report entry note the "Author+an" field where I specify "highlight" for the third author (me).

```
@techreport{ORNL-TM-2017-24,

    Address = {Oak Ridge, TN 37831-6283 USA},

	Author = {Melissa Allen and H M Abdul Aziz and Mark Coletti and Joseph H Kennedy and Sujithkumar S Nair and OluFemi A Omitaomu},

	Author+an = {3=highlight},

	Institution = {Oak Ridge National Laboratory},

	Number = {ORNL/TM-2017/24},

	Title = {Workshop on Human Activity at Scale in Earth System Models},

	url = "https://portal17.ornl.gov/PTS/SitePages/PTS.aspx?mode=viewDoc&pubid=72539&docid=96196",

	Year = 2017}
```
In my preamble, I add this:
```
\renewcommand*{\mkbibnamegiven}[1]{%

\ifitemannotation{highlight}

{\textbf{#1}}

{#1}}



\renewcommand*{\mkbibnamefamily}[1]{%

\ifitemannotation{highlight}

{\textbf{#1}}

{#1}}
```
