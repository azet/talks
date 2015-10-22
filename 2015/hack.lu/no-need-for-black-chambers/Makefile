####################################################
# ------------------------------------------------------------------------
# Minimalistic Makefile 
# ------------------------------------------------------------------------
####################################################

LATEX = pdflatex -shell-escape # Needed for minted source highlighting

SLIDES = noneedforblackchambers

# Always rebuild slides
.PHONY: $(SLIDES).tex

all: $(SLIDES).pdf clean

%.pdf: %.tex  
	$(LATEX) $<
	$(LATEX) $<
	if [ -f $*.pdfpc ] ; \
		then \
			sed -i 's/\\\\/\n/g' $*.pdfpc ; \
			sed -i 's/\\par/\n\n/g' $*.pdfpc ; \
	fi;

clean:
	rm -f *.aux *.log 
	rm -f *.bbl *.blg
	rm -f *.nav *.out
	rm -f *.toc *.snm

dist-clean: clean
	rm -f *.pdf

