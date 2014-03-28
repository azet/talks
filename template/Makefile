TALK     := presentation.pdf
TEX_SUFS := .aux .log .nav .out .snm .toc .vrb .fdb_latexmk .fls

%.pdf:%.tex
	latexmk -pdf $<

all: $(TALK)

genocide: clean clean-pdf

clean:
	$(RM) $(foreach suf, ${TEX_SUFS}, $(TALK:.pdf=${suf})) *~
	$(RM) -r auto

clean-pdf:
	$(RM) *.pdf

