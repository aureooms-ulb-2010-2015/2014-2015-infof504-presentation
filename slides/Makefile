$(VERBOSE).SILENT:

FILENAME = slides
STYLESHEET = stylesheet
BIBLIOGRAPHY = bibliography

TEX = tex
STY = sty
BIB = bib

OK_STRING = $(OK_COLOR)✔
WARN_STRING = $(WARN_COLOR)⚑
ERROR_STRING = $(ERROR_COLOR)✖

ifneq ($(nocolor),true)
	NO_COLOR=\033[0m
	OK_COLOR=\033[92m
	ERROR_COLOR=\033[91m
	WARN_COLOR=\033[93m
	ACTION_COLOR=\033[95m
endif


TEXFILES = $(shell find $(TEX) -regex .*\\.tex | sed 's/:/\\:/g' | sed 's/ /\\ /g')
STYFILES = $(shell find $(STY) -regex .*\\.sty 2>/dev/null | sed 's/:/\\:/g' | sed 's/ /\\ /g')
BIBFILES = $(shell find $(BIB) -regex .*\\.bib 2>/dev/null | sed 's/:/\\:/g' | sed 's/ /\\ /g')

RERUN = "(There were undefined citations|Rerun to get (cross-references|the bars) right)"
UNDEFINED = "((Reference|Citation).*undefined)|(Label.*multiply defined)"
LATEX = xelatex -shell-escape -interaction=nonstopmode -halt-on-error
BIBTEX = bibtex
MAKEINDEX = makeindex


default: all

all: $(FILENAME).pdf

$(FILENAME).pdf: $(FILENAME).tex $(STYLESHEET).sty $(BIBLIOGRAPHY).bib

	echo -n "$(ACTION_COLOR)"link \#1"$(NO_COLOR) "
	$(LATEX) $(FILENAME).tex 1> sys.out || touch sys.err
	if [ -e sys.err ]; then echo "$(ERROR_STRING)" && cat sys.out && echo -n "$(NO_COLOR)"; fi;
	if [ -e sys.err ]; then rm -f sys.err sys.out && false; fi;
	if [ -e $(BIB)/.changed ]; then echo -n "\n$(ACTION_COLOR)"bibtex"$(NO_COLOR) " \
	&& $(BIBTEX) $(FILENAME) 1> sys.out && rm $(BIB)/.changed \
	|| (touch sys.err && rm $(BIB)/.changed); fi
	if [ -e sys.err ]; then echo "$(ERROR_STRING)" && cat sys.out && echo -n "$(NO_COLOR)"; fi;
	if [ -e sys.err ]; then rm -f sys.err sys.out && false; fi;
	if [ -e $(FILENAME).idx ]; then echo -n "\n$(ACTION_COLOR)"makeindex"$(NO_COLOR) " \
	&& $(MAKEINDEX) $(FILENAME) 2> sys.out || touch sys.err; fi
	if [ -e sys.err ]; then echo "$(ERROR_STRING)" && cat sys.out && echo -n "$(NO_COLOR)"; fi;
	if [ -e sys.err ]; then rm -f sys.err sys.out && false; fi;
	echo -n "\n$(ACTION_COLOR)"link \#2"$(NO_COLOR) "
	$(LATEX) $(FILENAME).tex 1> sys.out || touch sys.err
	if [ -e sys.err ]; then echo "$(ERROR_STRING)" && cat sys.out && echo -n "$(NO_COLOR)"; fi;
	if [ -e sys.err ]; then rm -f sys.err sys.out && false; fi;
	if egrep -q $(RERUN) $(FILENAME).log ; then echo -n "\n$(ACTION_COLOR)"link \#3"$(NO_COLOR) " \
	&& $(LATEX) $(FILENAME).tex 1> sys.out || touch sys.err; fi
	if [ -e sys.err ]; then echo "$(ERROR_STRING)" && cat sys.out && echo -n "$(NO_COLOR)"; fi;
	if [ -e sys.err ]; then rm -f sys.err sys.out && false; fi;
	if egrep -q $(RERUN) $(FILENAME).log ; then echo -n "\n$(ACTION_COLOR)"link \#4"$(NO_COLOR) " \
	&& $(LATEX) $(FILENAME).tex 1> sys.out || touch sys.err; fi
	if [ -e sys.err ]; then echo "$(ERROR_STRING)" && cat sys.out && echo -n \
	"$(NO_COLOR)"; elif [ -s sys.out ]; then echo "$(WARN_STRING)" && \
	cat sys.out && echo -n "$(NO_COLOR)"; \
	else echo "$(OK_STRING)$(NO_COLOR)"; fi;
	if [ -e sys.err ]; then rm -f sys.err sys.out && false; else rm -f \
	sys.err sys.out; fi;
	egrep -i $(UNDEFINED) $(FILENAME).log || true


$(FILENAME).tex: $(TEXFILES)
	echo "$(ACTION_COLOR)"tex files changed"$(NO_COLOR)"
	$$ txt concat $(TEX) $(FILENAME).tex

ifeq ($(STYFILES),)

$(STYLESHEET).sty: $(STYFILES)

else

$(STYLESHEET).sty: $(STYFILES)
	echo "$(ACTION_COLOR)"sty files changed"$(NO_COLOR)"
	$$ txt concat $(STY) $(STYLESHEET).sty

endif

ifeq ($(BIBFILES),)

$(BIBLIOGRAPHY).bib: $(BIBFILES)

else

$(BIBLIOGRAPHY).bib: $(BIBFILES)
	echo "$(ACTION_COLOR)"bib files changed"$(NO_COLOR)"
	$$ txt concat $(BIB) $(BIBLIOGRAPHY).bib
	touch $(BIB)/.changed

endif

clean:
	rm -f *.aux *.lof *.log *.bbl *.blg *.toc *.idx *.ilg *.ind *.lol *.out \
	*.loa *.equ *.pyg *.alg *.lomintedobject *.loalgobject *.loe *.asy *.nav *.snm \
	$(FILENAME).tex $(FILENAME).pdf $(STYLESHEET).sty $(BIBLIOGRAPHY).bib
