# Lab Report 3: Grep 
`grep` searches a given file, files, or directory by searching line-by-line for mathces to a specified string of content or pattern. It is a useful command for finding and processing files as its abilities range anywhere between counting the files relevant to the search and the occurrences which the pattern/content is found, all the way to returning every instance within a given number of lines of context and concatenated in an automatic compilation.
The following write-up will demonstrate four options that can be used to specify `grep` function, including examples using [written_2/](https://github.com/ucsd-cse15l-w23/skill-demo1-data) as an example collection of files.

### -v (--invert-match): 
`-v` is a grep search modifieer that will invert the results of the search; where in a normal search, only lines found with content that matches the input pattern/content would be returned, `-v` will return every line within the specified file(s)/directory that *doesn't*.
This function could be useful in, for example, trimming a series of chapters together without their references in order to create a seamless compilation (presumably to also concatenate the references and their respective chapters to the end).
Using written_2/non-fiction/OUP/Castro , each of the 26 chapters can be stripped of their references and compiled together with:
```
grep -h -v -e 'References' -e 'See also' written_2/non-fiction/OUP/Castro/*.txt > CastroFull.txt
```
We use `-e` to keep adding to the specified search content, `-h` to omit filenames precluding each line of result, and `> CastroFull.txt` to write the results into an output file, CastroFull.txt. 
And, in order to demonstrate the inverse results, we can further compile and concatenate the omitted lines of citation along with their corresponding files to the end of the text file with:
```
grep -e 'References' -e 'See also' written_2/non-fiction/OUP/Castro/*.txt >> CastroFull.txt
```
Where `>> CastroFull.txt` appends the output of this search to the end of the exisitng text file CastroFull.txt.
And now we can prove this compilation of the references-trimmed chapter using:
```
grep -m 5 -n -e 'References' -e 'See also' CastroFull.txt
```
which results in an output with the first 5 lines reading:
```
813:written_2/non-fiction/OUP/Castro/chA.txt:See also El Coco; El Cucui; El Kookoóee; Los Matachines
814:written_2/non-fiction/OUP/Castro/chA.txt:References Brown 1978; Cobos 1983; Espinosa 1910; Steele 1992
815:written_2/non-fiction/OUP/Castro/chA.txt:References Arrizón 1998; Herrera-Sobek 1990; Niggli 1938; Salas 1990
816:written_2/non-fiction/OUP/Castro/chA.txt:References Brown 1978; Campa 1937; Espinosa 1985; Glazer 1994; Lucero-White 1941; McDowell 1979; West 1988
817:written_2/non-fiction/OUP/Castro/chA.txt:References Boyd 1974; Brown 1978; Bunting 1964, 1974; De Leon 1982; Graham 1991; Romero and Larkin 1994; Weigle and White 1988; West 1988
```
Where the first numbers (prompted by modifier `-n` that precludes each result line with the number line it occurs in local to the file) starting from 813 and iterating onward indicate that the first occurrence of "See also" or "References" occur after 812 lines where neither occur at all, and then those instances are listed in a sequence of successive lines, like we intended! Therefore, the -v was properly able to identify and omit instances of "See also" and "References" in the rest of the 812 lines of text within the 26 chapters of the written_2/non-fiction/OUP/Castro/ directory. 

`-v` could also be helpful in editing a file for brevity and other criteria, by cutting out lines that contain a certain pattern or marker. An author or researcher could store their writings in a file and leave a unique marker to indicate a line intended to be deleted before publication. This marker could be anything from a '##TODO' to '???' or whatever else uncommonly occuring string. `grep -v' can clear a file of all lines that include this unique signifier. 
As an example, we can remove all lines including a '?' from a very rhetorical-heavy text, ch6 of written_2/non-fiction/OUP/Kauffman/:
```
grep -v '?' written_2/non-fiction/OUP/Kauffman/ch6.txt > newCh6.txt
```
And, to check our work, we can simply:
```
wc written_2/non-fiction/OUP/Kauffman/ch6.txt
```
which returns 
```
  124  9922 61513 written_2/non-fiction/OUP/Kauffman/ch6.txt
```
Indicating that there are 124 total lines in chr6.txt.

Next,
```
grep '?' newCh6.txt
```
which returns 
```
```
and 
```
wc newCh6.txt
```
which returens
```
   91  6859 42923 newCh6.txt
```
And therefore indicates that all lines including '?' were ommitted when ch6.txt was being rewritten line by line into newCh6.txt, and only the 91 (out of a previous 124) lines remain.


### -w (--word regexp): 
`-w` is another helpful search modifier that only returns results that form what is recognized as a whole word. This means that either the found instance must be preceeded and succeeded by either an end of the line or by a character that is not a letter, number, or underscore.
This can be used to search for number of instances of specific word (and not any of its variants, such as varying prefixes/suffiixes/tenses/etc) within a directory.
To demonstrate, we can search to count the number of instances of the word "food" within the written/travel-guides/ directory:
```
grep -w 'food' written_2/travel_guides/*/*.txt | wc
```
Where the `| wc` feed the grep output directly into the `wc` (word count) function.
The results look like:
```
    199   10310   74437
```
Meaning that the exact word "food" has occured in written_2/travvel_guides/ 199 times! 
This is in comparison with the looser search
```
grep 'food' written_2/travel_guides/*/*.txt | wc
```
Which results in
```
 275   13773   99721
```

`-w` can similarly be helpful in avoiding mix-up file results due to loose search bounds. For example, a search for "tan" like this:
```
grep 'tan' written_2/travel_guides/*/*.txt
```
Results in unwanted inclusions to the search results, as demonstrated by the inclusion of "outst*tan*ding" and "bands*tan*d" when the human intention of the search was just "tan" as in _suntan_.
Instead, a narrower search:
```
grep -w 'tan' written_2/travel_guides/*/*.txt
```
results in a much narrower:
```
written_2/travel_guides/berlitz1/IntroFWI.txt:        white, tan, or black volcanic sand. And the sunsets are
written_2/travel_guides/berlitz1/WhatToFWI.txt:        tan better on black sand.
written_2/travel_guides/berlitz1/WhatToIbiza.txt:        good tan. At all times it’s a good idea to wear a T-shirt or something
written_2/travel_guides/berlitz1/WhereToFWI.txt:        Anse-Bertrand is the Anse Laborde, a public beach of tan sand with
written_2/travel_guides/berlitz1/WhereToFWI.txt:        beach of magnificent tan sand is most agreeably reached by boat.
```
As intended by the client search.

### --C NUM (--context=NUM):
`--C NUM` is a grep option by which you can alter the amount of context (as in, the lines immediately surrounding the specific searched content) that is returned in additon to the search resutl itself. NUM is an input integer that specifies the 

### -m NUM (--max-count=NUM):

### Citations:
**General:**
- man grep : built-in documentation for grep
- [written_2/](https://github.com/ucsd-cse15l-w23/skill-demo1-data): collection of text files used in examples
- [Computer Hope (2019)](https://www.computerhope.com/unix/ugrep.htm#): general grep purpose, syntax, examples
- [Goran Jevtic (via phoenixNAP, 2020)](https://phoenixnap.com/kb/grep-multiple-strings): Syntax reference for passing multiple content/pattern arguments into a grep search
