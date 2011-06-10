LASTFREQ = /homes/mccallum/research/data/resources/personname/ssdi.last
FIRSTFREQ = /homes/mccallum/research/data/resources/personname/ssdi.first
CAPWORDFREQ = /homes/whizard/com/whizbang/corp/info/extract/resources/capword-freq.lst

FILES = \
ssdi.last.printable \
ssdi.first.printable \
ssdi.freqhighest.last \
ssdi.freqhighest.first \
ssdi.freqhigh.last \
ssdi.freqhigh.first \
ssdi.freqmed.last \
ssdi.freqmed.first \
ssdi.freqrare.last \
ssdi.freqrare.first \
ssdi.prlasthighest \
ssdi.prlasthigh \
ssdi.prlastmed \
ssdi.prlastlow \
ssdi.prlastlowest \
ssdi.prfirsthighest \
ssdi.prfirsthigh \
ssdi.prfirstmed \
ssdi.prfirstlow \
ssdi.prfirstlowest \
ssdi.lastonly \
ssdi.firstonly \
ssdi.missinglast \
ssdi.missingfirst \
ssdi.prcapwordhighest \
multiword-locations

all: $(FILES)


# Remove lines containing junk characters
ssdi.last.printable: $(LASTFREQ)
	cat $< | egrep -v '[^[:alnum:][:blank:]]' >| $@

# Remove lines containing junk characters
ssdi.first.printable: $(FIRSTFREQ)
	cat $< | egrep -v '[^[:alnum:][:blank:]]' >| $@

# Last names with count information.  Last field is approximiation of P(name|word)
# only incudes words that are on both the ssdi.last and capword-freq lists
ssdi.prlast: ssdi.last.printable
	capwordcount=`cat $(CAPWORDFREQ) | awk '{sum+=$$2+1} END {print sum}'` ; \
	echo capwordcount = $$capwordcount ; \
	lastcount=`cat ssdi.last.printable | awk '{sum+=$$2+1} END {print sum}'` ; \
	echo lastcount = $$lastcount ; \
	sort $(CAPWORDFREQ) | join - ssdi.last.printable \
	| awk --assign capwordcount=$$capwordcount --assign lastcount=$$lastcount \
	 '{print $$1, $$2, $$3, ($$2+1)/capwordcount, ($$3+1)/lastcount, (($$3+1)/lastcount)/(($$2+1)/capwordcount)}'\
	| sort -r -n +5 \
	>| $@


# First names with count information.  Last field is approximiation of P(name|word)
# only incudes words that are on both the ssdi.first and capword-freq lists
ssdi.prfirst: ssdi.first.printable
	capwordcount=`cat $(CAPWORDFREQ) | awk '{sum+=$$2+1} END {print sum}'` ; \
	echo capwordcount = $$capwordcount ; \
	firstcount=`cat ssdi.first.printable | awk '{sum+=$$2+1} END {print sum}'` ; \
	echo firstcount = $$firstcount ; \
	sort $(CAPWORDFREQ) | join - ssdi.first.printable \
	| awk --assign capwordcount=$$capwordcount --assign firstcount=$$firstcount \
	 '{print $$1, $$2, $$3, ($$2+1)/capwordcount, ($$3+1)/firstcount, (($$3+1)/firstcount)/(($$2+1)/capwordcount)}'\
	| sort -r -n +5 \
	>| $@


ssdi.prfirsthighest: ssdi.prfirst
	cat $< | awk '$$6 > 10 {print $$1}' | sort > $@
ssdi.prfirsthigh: ssdi.prfirst
	cat $< | awk '$$6 < 10 && $$6 > 2 {print $$1}' | sort > $@
ssdi.prfirstmed: ssdi.prfirst
	cat $< | awk '$$6 < 2 && $$6 > 0.5 {print $$1}' | sort > $@
ssdi.prfirstlow: ssdi.prfirst
	cat $< | awk '$$6 < 0.5 && $$6 > 0.13 {print $$1}' | sort > $@
# ...also remove words of length two
ssdi.prfirstlowest: ssdi.prfirst
	cat $< | awk '$$6 < 0.13 {print $$1}' | egrep -v '^..$$' |sort > $@

ssdi.prlasthighest: ssdi.prlast
	cat $< | awk '$$6 > 10 {print $$1}' |sort > $@
	echo seldeneck >> $@
ssdi.prlasthigh: ssdi.prlast
	cat $< | awk '$$6 < 10 && $$6 > 2 {print $$1}' | sort > $@
ssdi.prlastmed: ssdi.prlast
	cat $< | awk '$$6 < 2 && $$6 > 0.5 {print $$1}' | sort > $@
ssdi.prlastlow: ssdi.prlast
	cat $< | awk '$$6 < 0.5 && $$6 > 0.13 {print $$1}' | sort > $@
# ...also remove words of length two
ssdi.prlastlowest: ssdi.prlast
	cat $< | awk '$$6 < 0.13 {print $$1}' | egrep -v '^..$$' | sort > $@


# The words that are on in ssdi.last, but not in our capword list; also, remove
# words of length two
ssdi.lastonly: ssdi.last.printable
	lastcount=`cat ssdi.last.printable | awk '{sum+=$$2+1} END {print sum}'` ; \
	echo lastcount = $$lastcount ; \
	sort $(CAPWORDFREQ) | join -v 2 - ssdi.last.printable \
	| awk --assign lastcount=$$lastcount \
	 '{print $$1, $$2, ($$2+1)/lastcount}' \
	| join -v 1 - ../lowercase-usr-dict-words \
	| egrep -v '^may$$' \
	| awk '{print $$1}' \
	| egrep -v '^..$$' \
	>| $@

# The words that are on in ssdi.first, but not in our capword list; also, remove
# words of length two
ssdi.firstonly: ssdi.first.printable
	firstcount=`cat ssdi.first.printable | awk '{sum+=$$2+1} END {print sum}'` ; \
	echo firstcount = $$firstcount ; \
	sort $(CAPWORDFREQ) | join -v 2 - ssdi.first.printable \
	| awk --assign firstcount=$$firstcount \
	 '{print $$1, $$2, ($$2+1)/firstcount}' \
	| join -v 1 - ../lowercase-usr-dict-words \
	| egrep -v '^may$$' \
	| awk '{print $$1}' \
	| egrep -v '^..$$' \
	>| $@


# Highly frequent words in capword that also appear lowercase in /usr/dict/words,
# also exclude words that appear in some of the ssdi.pr{last,first}*
ssdi.prcapwordhighest: ssdi.prlasthighest ssdi.prfirsthighest
	sort -r -g +3 ssdi.prlast \
	| head -n 1000 | sort \
	| join - ../lowercase-usr-dict-words \
	| join -v 1 - ssdi.prlasthighest \
	| join -v 1 - ssdi.prlasthigh \
	| join -v 1 - ssdi.prfirsthighest \
	| join -v 1 - ssdi.prfirsthigh \
	| join -v 1 - ssdi.prfirstmed \
	| awk '{print $$1}' \
	>| $@

# For targets like ssdi.missingfirst and ssdi.missinglast
# Words in the ssdi.{first,last} lists that do not appear in any of our lists!
ssdi.missing%: ssdi.prfirsthighest ssdi.prfirsthigh ssdi.prfirstmed ssdi.prfirstlow ssdi.prfirstlowest ssdi.prlasthighest ssdi.prlasthigh ssdi.prlastmed ssdi.prlastlow ssdi.prlastlowest ssdi.lastonly ssdi.firstonly
	cat $^ \
	| sort | uniq \
	| join -v 2 - ssdi.$*.printable \
	>| $@


nicknames.pruned:
	head -n 1000 $(CAPWORDFREQ) | sort | join -v 2 - nicknames \
	| awk '{print $$1}' >| $@


multiword-locations:
	cat /homes/whizard/com/whizbang/core/entities/resources/cities.rot \
	| cut -d, -f1 \
	| sort -u \
	| grep " " | egrep -v '^#|^[^a-z]|/' | sed -e 's/\([^a-z ]\)/ \1 /g' \
	>| $@
	echo white house >> $@

# Below Not Used


# for targets like ssdi.freqhighest.last
ssdi.freqhighest.%:
	cat ssdi.$* | awk '$$2 > 5000 {print $$1}' > $@

ssdi.freqhigh.%:
	cat ssdi.$* | awk '$$2 > 500 && $$2 < 5000 {print $$1}' > $@

ssdi.freqmed.%:
	cat ssdi.$* | awk '$$2 > 10 && $$2 < 500 {print $$1}' > $@

ssdi.freqrare.%:
	cat ssdi.$* | awk '$$2 > 1 && $$2 < 10 {print $$1}' > $@

clean:
	rm -f $(FILES)

