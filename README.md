# LaTeX-tutorial

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

## LaTeX/Tips and Tricks
[Click](https://en.wikibooks.org/wiki/LaTeX/Tips_and_Tricks)

- [x] Spell-checking and Word Counting

If you want to spell-check your document, you can use the command-line aspell, hunspell (preferably), or ispell programs.

  ```
ispell yourfile.tex
aspell --mode=tex -c yourfile.tex
hunspell -l -t -i utf-8 yourfile.tex
```

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

## Top 5 Beamer Tips

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
