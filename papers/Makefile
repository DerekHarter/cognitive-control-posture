PANDOC_FLAGS=
out := _out
cache := $(out)/.cache
build := $(out)/pdf
mkdir = @mkdir -p $(dir $@)

#TEXINPUTS=".:$(cache):" pdflatex -output-directory="$(cache)" -interaction=nonstopmode -file-line-error $< | grep ":[^:]*:" | grep -v "natbib Warning: Citation"
define pdflatex
TEXINPUTS=".:$(cache):" pdflatex -output-directory="$(cache)" -interaction=nonstopmode -file-line-error $< | grep "Output"
endef

define biber
biber  $(basename $< .tex) | grep "Output"
endef

define rubber
rubber -m pdftex --inplace
endef

define make-depend
../src/deps.py $@ < $< > $(cache)/$*.d
endef


## List of all valid targets in this project:
## ------------------------------------------
## all        : by default generate all pdf documents in this repository.
.PHONY : all
all : $(build)/appendix.pdf $(build)/experiment-notes.pdf


## appendix.pdf
##            : Generate appendix.pdf from markdown/tex templates.
##
appendix-docs := appendix.tex
appendix-bibs := task-switching-replication.bib
appendix-figures := ../figures/figure-task-switching-replication-reaction-time.png \
	../figures/figure-task-switching-replication-accuracy.png
appendix-tables := ../tables/table-task-switching-replication-reaction-time.tex \
	../tables/table-subject-summary.tex
appendix-src := $(appendix-docs) $(appendix-bibs) $(appendix-figures) $(appendix-tables)
appendix-dest := $(addprefix $(cache)/, $(appendix-src))
$(cache)/appendix.tex : PANDOC_FLAGS+=-s -N
$(build)/appendix.pdf : $(appendix-dest)

## experiment-notes.pdf
##            : Generate experiment-notes.pdf from markdown/tex templates.
##
experiment-notes-docs := experiment-notes.tex
experiment-notes-bibs := task-switching-replication.bib
experiment-notes-figures := 
experiment-notes-tables := 
experiment-notes-src := $(experiment-notes-docs) $(experiment-notes-bibs) $(experiment-notes-figures) $(experiment-notes-tables)
experiment-notes-dest := $(addprefix $(cache)/, $(experiment-notes-src))
$(cache)/experiment-notes.tex : PANDOC_FLAGS+=-s -N
$(build)/experiment-notes.pdf : $(experiment-notes-dest)

$(cache)/%.tex : %.pmd
	@echo "\n\n"
	@echo "************ Weaving Markdown: $< **************"
	$(mkdir)
	pweave -f pandoc -o $(cache)/$*.md $<
	pandoc $(PANDOC_FLAGS) -t latex -o $@ $(cache)/$*.md
	$(make-depend)

$(cache)/%.bib : %.bib
	$(mkdir)
	cp $< $@

$(cache)/%.tex : %.tex
	$(mkdir)
	cp $< $@
	$(make-depend)

$(cache)/%.png : %.png
	$(mkdir)
	cp $< $@

$(cache)/%.sty : %.sty
	$(mkdir)
	cp $< $@

$(cache)/%.cls : %.cls
	$(mkdir)
	cp $< $@

$(build)/%.pdf : $(cache)/%.tex
	@echo "\n\n"
	@echo "************ Building PDF: $@ **************"
	$(mkdir)
	#$(rubber) $<
	$(pdflatex)
	$(biber)
	$(pdflatex)
	$(pdflatex)
	cp $(cache)/$*.pdf $@
	cp $(cache)/$*.pdf .



## clean      : Remove auto-generated files for a completely clean rebuild.
.PHONY : clean
clean  :
	rm -f *.aux *.log *.out *.bbl *.blg *.lof *.lot *.toc *.equ *~
	rm -rf $(out)


## help       : Get all build targets supported by this build.
.PHONY : help
help : Makefile
	@sed -n 's/^##//p' $<

