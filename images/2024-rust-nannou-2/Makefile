.PHONY: all
all: tikz-images

tikz-images: tikz.pdf
	pdftocairo -png -r 1200 $^

%.pdf: %.tex
	latexmk $^

.PHONY: clean
clean:
	latexmk -C
