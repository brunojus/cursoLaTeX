# É só escrever o comando "make". Entro com "make clean" para limpar a sujeira e
# "make buildclean" para deletar o pdf

INPUT = elsarticle-template

SUBDIR_ROOTS := ambientes figuras fixos historico intro tables
DIRS := . $(shell find $(SUBDIR_ROOTS) -type d)
GARBAGE_PATTERNS := *.aux *.log *.toc *.bbl *.bak *.blg *.out *.lof *.lot *.lol *.glg *.glo *.ist *.xdy *.gls *.acn *.acr *.idx *.alg *.nav *.snm *.vrb *.fls *.fdb_latexmk
GARBAGE := $(foreach DIR,$(DIRS),$(addprefix $(DIR)/,$(GARBAGE_PATTERNS)))

all: clean optimize

history:
	./latex-git-log --author --width=5 > ./conteudo/commit_log.tex

do: *.tex
	if test -f *.bib ;\
	then \
		pdflatex $(INPUT);\
		echo -n "Buscando citações";\
		grep -v "\%" conteudo/*.tex > search.temp;\
		echo " ";\
		echo -n "Montando bibliografias..." ;\
		pdflatex $(INPUT);\
		pdflatex -interaction=batchmode $(INPUT);\
		bibtex $(INPUT) -terse;\
		pdflatex -interaction=batchmode $(INPUT);\
		makeglossaries $(INPUT);\
		makeindex $(INPUT).glo -s $(INPUT).ist -t $(INPUT).glg -o $(INPUT).gls;\
		pdflatex -interaction=batchmode $(INPUT);\
		pdflatex -interaction=batchmode $(INPUT);\
		echo "Feito.";\
	else \
		echo "Arquivo de bibliografias inexistente.";\
		pdflatex $(INPUT);\
		pdflatex -interaction=batchmode $(INPUT);\
		makeindex $(INPUT).glo -s $(INPUT).ist -t $(INPUT).glg -o $(INPUT).gls;\
		pdflatex $(INPUT);\
	fi;
	rm -rf search.temp
	@make clean

# Compila a cada alteração de qualquer arquivo *.tex ou de qualquer *.vhd dentro da pasta 'src'
$(INPUT).pdf: conteudo/*.tex *.bib clean
	clear
#	pdflatex -interaction errorstopmode -interaction=batchmode $(INPUT).tex
	pdflatex $(INPUT).tex
	clear
	@echo "Compilado pela primeira vez...Feito."
	make bib
	@echo "Compilando pela segunda vez:"
	@pdflatex -interaction=batchmode $(INPUT).tex
	@echo -n "Feito\nCompilando pela ultima vez:\n"
	@pdflatex -interaction=batchmode $(INPUT).tex
	@echo -n "Limpando sujeira..."
	@make clean
	@echo "Feito."
	
optimize: do
	clear
	mv $(INPUT).pdf "$(notdir $(PWD)).pdf"
	@echo "Informações do arquivo gerado:" $(notdir $(PWD)).pdf
	pdfinfo "$(notdir $(PWD)).pdf"
	# rm -rf $(INPUT).pdf
	
# Limpa qualquer sujeira que reste após compilação
# Útil que objetos de linguagens são incluidos e ficam relatando erros após retirados.
clean:
	rm -rfv $(GARBAGE)
	
buildclean:
	rm -rf *.pdf
	
# Por algum motivo o *.pdf sumia da pasta. Gerado apenas para guardar uma copia de segurança na pasta
backup: $(INPUT).pdf
	pdfopt $(INPUT).pdf $(notdir $(PWD)).pdf

bib: *.bib *.tex
	if test -f *.bib ;\
	then \
		echo -n "Buscando citações";\
		grep -v "\%" *.tex > search.temp;\
		if grep '\\cite{'  search.temp -qn;\
		then \
			echo " ";\
			echo -n "Montando bibliografias..." ;\
			bibtex $(INPUT);\
			echo "Feito.";\
		else \
			echo " ... Nenhuma encontrada";\
		fi;\
	else \
		echo "Arquivo de bibliografias inexistente.";\
	fi;
	rm -rf search.temp

configure:
#	if test -d fts; then echo "hello world!";else echo "Not find!"; fi
	grep -v "\%" *.tex > search.temp
	grep '\\cite{'  search.temp
	rm -rv search.temp
#	grep '^%' *.tex
	
# .SILENT:
