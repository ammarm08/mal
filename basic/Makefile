MODE = cbm
BASICPP_OPTS = --mode $(MODE)

STEPS4_A = step4_if_fn_do.bas step5_tco.bas step6_file.bas \
           step7_quote.bas step8_macros.bas step9_try.bas stepA_mal.bas
STEPS3_A = step3_env.bas $(STEPS4_A)
STEPS1_A = step1_read_print.bas step2_eval.bas $(STEPS3_A)
STEPS0_A = step0_repl.bas $(STEPS1_A)

all: $(STEPS0_A)

step%.bas: step%.in.bas
	./basicpp.py $(BASICPP_OPTS) $< > $@

$(STEPS0_A): readline.in.bas
$(STEPS1_A): debug.in.bas types.in.bas reader.in.bas printer.in.bas
$(STEPS3_A): env.in.bas
$(STEPS4_A): core.in.bas

tests/%.bas: tests/%.in.bas
	./basicpp.py $(BASICPP_OPTS) $< > $@


# CBM/C64 image rules

%.prg: %.bas
	cat $< | tr "A-Z" "a-z" > $<.tmp
	#cat $< | sed 's/["]\@<!\<\w\+\>["]\@!/\L&/g' > $<.tmp
	petcat -w2 -nc -o $@ $<.tmp
	#rm $<.tmp

mal.prg: stepA_mal.prg
	cp $< $@

.args.mal.prg: .args.mal
	petcat -text -w2 -o $@ $<

core.mal.prg: ../core.mal
	petcat -text -w2 -o $@ $<

mal.d64: mal.prg .args.mal.prg core.mal.prg
	c1541 -format "mal,01" d64 $@ \
	    -attach $@ \
	    -write $< mal \
	    -write .args.mal.prg .args.mal \
	    -write core.mal.prg core.mal


# Clean and Stats rules

.PHONY: clean stats

clean:
	rm -f $(STEPS0_A) *.d64 *.prg


SOURCES_LISP = env.in.bas core.in.bas stepA_mal.in.bas
SOURCES = readline.in.bas types.in.bas reader.in.bas printer.in.bas $(SOURCES_LISP)

stats: $(SOURCES)
	@wc $^
	@printf "%5s %5s %5s %s\n" `grep -E "^[[:space:]]*#|^[[:space:]]*REM |^[[:space:]]*$$" $^ | wc` "[comments/blanks]"
stats-lisp: $(SOURCES_LISP)
	@wc $^
	@printf "%5s %5s %5s %s\n" `grep -E "^[[:space:]]*#|^[[:space:]]*REM |^[[:space:]]*$$" $^ | wc` "[comments/blanks]"

