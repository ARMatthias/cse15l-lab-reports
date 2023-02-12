# Lab Report 3: grep 
`grep` searches a given file, files, or directory by searching line-by-line for matches to a specified string of content or pattern. It is a useful command for finding and processing files as its abilities range anywhere between counting the files relevant to the search and the occurrences which the pattern/content is found, all the way to returning every instance within a given number of lines of context and concatenated in an automatic compilation.
The following write-up will demonstrate four options that can be used to specify `grep` function, including examples using [written_2/](https://github.com/ucsd-cse15l-w23/skill-demo1-data) as an example collection of files.

### -v (--invert-match): 
`-v` is a grep search modifier that will invert the results of the search; where in a normal search, only lines found with content that matches the input pattern/content would be returned, `-v` will return every line within the specified file(s)/directory that *doesn't*.
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
Where `>> CastroFull.txt` appends the output of this search to the end of the existing text file CastroFull.txt.
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
which returns
```
   91  6859 42923 newCh6.txt
```
And therefore indicates that all lines including '?' were omitted when ch6.txt was being rewritten line by line into newCh6.txt, and only the 91 (out of a previous 124) lines remain.



### -w (--word regexp): 
`-w` is another helpful search modifier that only returns results that form what is recognized as a whole word. This means that either the found instance must be preceded and succeeded by either an end of the line or by a character that is not a letter, number, or underscore.
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
Meaning that the exact word "food" has occurred in written_2/travvel_guides/ 199 times! 
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
Results in unwanted inclusions to the search results, as demonstrated by the inclusion of "outs*tan*ding" and "bands*tan*d" when the human intention of the search was just "tan" as in _suntan_.
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
Which is closer to the intention of the client search.



### -C NUM (--context=NUM):
`-C NUM` is a grep option by which you can alter the amount of context (as in, the lines immediately surrounding the specific searched content) that is returned in addition to the search result itself. NUM is an input integer that specifies the number of lines of context to be included in the returned instance result. 
 
As an example, a client could need to search for *articles* that include the topic "garrison", and therefore would need more than just the line in which the word "garrison" would appear. A search that could return the target line and 2 additional lines of context surrounding it would look like this:
```
grep -C 2 'garrisons' written_2/travel_guides/berlitz*/*.txt
```
and so a single result of this search would be a lot more illuminating:
```
written_2/travel_guides/berlitz1/HistoryEdinburgh.txt-        The Stewart Dynasty
written_2/travel_guides/berlitz1/HistoryEdinburgh.txt-        During these stormy years, the castle of Edinburgh was
written_2/travel_guides/berlitz1/HistoryEdinburgh.txt:        occupied several times by English garrisons. In 1341 it was taken from
written_2/travel_guides/berlitz1/HistoryEdinburgh.txt-        the English by William of Douglas. The young David II returned from
written_2/travel_guides/berlitz1/HistoryEdinburgh.txt-        exile in France and made it his principal royal residence, building a
```
instead of the same search result without the expanded context, which looks like this
```
written_2/travel_guides/berlitz1/HistoryEdinburgh.txt:        occupied several times by English garrisons. In 1341 it was taken from
```

Similarly, a client could be searching for an article-length excerpt including the topic 'soccer'. 
A search for this with a context of 4 could look like this:
```
grep -C 5 'soccer' written_2/travel_guides/berlitz*/*.txt
```
which, one of the results looks like:
```
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-Horseback-riding. Equestrian holidays are increasingly popular in Poland; ask your travel agent about Orbis horseback-riding vacations. If you just want to get in the saddle a time or two, contact: Pa-ta-Taj Horse-Riding School (Szkola Jazdy Konnej) (ul. Krótka 9, Tel. 758 58 35) or Sport and Recreation (Limanki, 26 Luze, Tel. 792 08 42) in Warsaw. There are a couple of dozen stables and riding schools in the environs of the capital; ask for additional information from Tourist Information Centers or your hotel.
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-Skiing. The hotspot for skiing is Zakopane, at the foot of the High Tatra mountains in southeastern Poland. Skiing is excellent, inexpensive and very popular with Poles and some foreign vacationers on ski packages, even though facilities lag behind resorts in the Alps and Pyrenees.
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-Swimming and Water Sports. In Warsaw, the Victoria, Marriott, and Bristol hotels have pools. Less ritzy are these pools: Spartanska (ul. Spartanska 1, Tel. 848 67 46), OSIR (ul. Kasprzaka 1, Tel. 631 48 67), and Merlini’s Water Park (ul. Merliniego, Tel. 854 01 30). Kraków also has several pools open to visitors: Wodny Water Park (ul. 
Dobrego Pasterza 126, Tel. 413 73 99), Sofitel Hotel (ul. M. Konopnickiej 28, Tel. 261 92 12), and Korona (ul. Kalwaryjska 9, Tel. 656 13 68).
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-The vast majority of boating and other water sports is concentrated in the Mazurian Lakes district in northeastern Poland and the towns along 
the Bay of Gdansk, on the Baltic Sea.
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-Spectator Sports
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt:Football (soccer). Football is Poland’s most popular spectator sport. Warsaw’s two first-division football teams are Legia Warszawa (ul. Lazienkowska 3, Tel. 628 43 03) and Polonia Warszawa (ul. Konwiktors,a 6, Tel. 635 14 01).
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-Children’s Poland
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-Poland is not an obvious place to take children, perhaps, and in fact few foreigners who haven’t been posted overseas in Poland seem to do it. Traveling with children in Poland is a matter of being flexible, creative, and putting together disparate activities to keep the kids interested when palaces, castles, and rebuilt old towns impress them less than they do their parents. Many of the activities listed below are in the capital, Warsaw, where there is simply a greater abundance of facilities.
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-To begin with the most obvious option, the Warsaw Zoo (ul. Ratuszowa 1-3, Tel. 619 40 41, <www.warszawa.um .gov.pl./zoo>) has been going strong since 1928. The habitats of some 4000 animals, including Siberian tigers, kangaroos, cheetahs, crocs, snow leopards, and an unusual red panda are spread across 40 hectares (99 acres). The zoo also has a free-flight bird hall. Another option in the capital for small children is the Baj Puppet Theater (ul. Jagiellonska 28, Tel. 818 08 21) housed in a former Jewish school in Praga, across the river from Old Town. Playgrounds — the kind with tunnels, jungle gyms, and inflatable cages filled with rubber balls — are found in the capital: Stratosfera (ul. Ostrobramska 75c, Tel. 611 35 83); Kolorado Ursynów (ul. Herbsta 4, Tel. 664 88 77).
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-For kids with energy to burn, there are water parks and swimming pools (see above) in summer and ice rinks in winter. To go ice skating, check out the Stegny Skating Track (Tor Lyzwiarski Stegny), (ul. Inspektowa 1, Tel. 842 27 68) or Torwar (ul. Lazienkowska, Tel. 621 44 71) in Warsaw. Another activity that is all the rage is paintball, and there are multiple fields where you can inflict paint rather than pain. Try Marcus-Graf (ul. Widok 10, in Beniaminów near Warsaw; Tel. 816 10 08), TM Pretor (ul. Zaaruskiego 6, Warsaw; Tel. 838 25 35), Rival Paintball Guns (ul. Bartosika 10/77, Warsaw; Tel. 671 22 63), or Extreme Sports (ul. Klaudyny 18/5, Warsaw; Tel. 833 73 73). Paintballers in Kraków should check out Ultimate Sports (near fort Bodzanów; Tel. 431 20 61).
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt-Another fast-paced sport is go-karts. In Warsaw, race the kids over to Imola, where they also feature paintball (ul. Pulawska 33, Piaseczno; Tel. 648 27 14). If your kids like bowling, you’ll find facilities in all the major cities. One place they’re sure to like in Kraków is Atomic World Entertainment Center (ul. Zakopianska 62; Tel. 
261 31 14), which not only has swanky lanes but loads of video games, billiards, and air hockey. In the Malta lake district of Poznan, there are many facilities ideal for children, including a manmade ski slope and a exhilerating toboggan run.
```



### -c (--count):
`-c` is able to condense necessary commands in the way of counting the number of matching instances there are in a given file(s)/directory.

If given a directory, `-c` will return the number of matching instances per each file (including files within the directory that do not have any matching instances).
Where, as an example, searching written_2/travel_guides/ for "snow":
```
grep -c 'snow' written_2/travel_guides/berlitz*/*.txt | grep -v "0" 
```
where `| grep -v "0"` filters out all of the files returned with 0 as the returned number of matching instances.

The commands results in:
```
written_2/travel_guides/berlitz1/HistoryFWI.txt:1
written_2/travel_guides/berlitz1/HistoryItaly.txt:1
written_2/travel_guides/berlitz1/IntroItaly.txt:1
written_2/travel_guides/berlitz1/IntroJapan.txt:1
written_2/travel_guides/berlitz1/IntroLakeDistrict.txt:1
written_2/travel_guides/berlitz1/WhatToJapan.txt:2
written_2/travel_guides/berlitz1/WhatToLakeDistrict.txt:1
written_2/travel_guides/berlitz1/WhatToLasVegas.txt:3
written_2/travel_guides/berlitz1/WhatToLosAngeles.txt:1
written_2/travel_guides/berlitz1/WhereToDublin.txt:1
written_2/travel_guides/berlitz1/WhereToFrance.txt:6
written_2/travel_guides/berlitz1/WhereToItaly.txt:2
written_2/travel_guides/berlitz1/WhereToJapan.txt:2
written_2/travel_guides/berlitz1/WhereToLosAngeles.txt:1
written_2/travel_guides/berlitz1/WhereToMadeira.txt:1
written_2/travel_guides/berlitz1/WhereToMalaysia.txt:1
written_2/travel_guides/berlitz2/Berlin-WhatToDo.txt:1
written_2/travel_guides/berlitz2/Boston-WhereToGo.txt:1
written_2/travel_guides/berlitz2/Budapest-History.txt:1
written_2/travel_guides/berlitz2/Budapest-WhereoGo.txt:1
written_2/travel_guides/berlitz2/California-History.txt:1
written_2/travel_guides/berlitz2/California-WhereToGo.txt:2
written_2/travel_guides/berlitz2/Canada-History.txt:5
written_2/travel_guides/berlitz2/Canada-WhereToGo.txt:8
written_2/travel_guides/berlitz2/CanaryIslands-WhereToGo.txt:5
written_2/travel_guides/berlitz2/China-WhereToGo.txt:2
written_2/travel_guides/berlitz2/Costa-WhereToGo.txt:3
written_2/travel_guides/berlitz2/Crete-WhereToGo.txt:2
written_2/travel_guides/berlitz2/CstaBlanca-WhereToGo.txt:1
written_2/travel_guides/berlitz2/Nepal-WhatToDo.txt:2
written_2/travel_guides/berlitz2/Nepal-WhereToGo.txt:2
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt:1
```
This is advantageous because it not only returns the count for each file, but includes only one unique line for any amount of matching returns -- this, very quickly able to be read into `wc` to determine how many *files* have matching content, and which ones.

For use within an individual file --as a separate example-- we can search "snow" within file WhereToFrance.txt
```
grep -c "snow" written_2/travel_guides/berlitz1/WhereToFrance.txt
```
Which simply results in
```
6
```

### Citations:
- man grep : built-in documentation for grep
- [written_2/](https://github.com/ucsd-cse15l-w23/skill-demo1-data): collection of text files used in examples
- [Computer Hope (2019)](https://www.computerhope.com/unix/ugrep.htm#): general grep purpose, syntax, examples
- [Goran Jevtic (via phoenixNAP, 2020)](https://phoenixnap.com/kb/grep-multiple-strings): Syntax reference for passing multiple content/pattern arguments into a grep search
