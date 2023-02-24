# Done Quick Challenge

As its in good practice to look for ways to speed up general, commonly used routines/commands, the ["Done Quick" challenge](https://ucsd-cse15l-w23.github.io/week/week7/) serves to prompt participants to brainstorm ways they might streamline their common setup and workflow tasks in the terminal. My own approach to the challenge follows.

The steps listed below proceed with the assumption that the terminal and ssh environments are proprely set up as explained in the link above. This includes having set the ssh account password to **Enter** and having generated and added to GitHub a local access key. If this is not your first run through of the challenge, setup also will include deleting the previously cloned repository on the ssh (this is easily done with `rm -rf lab7` in the directory location of the previous cloned repository) and the previously used fork of the main repository on GitHub (this will need to be deleted from your GitHub account by navigating to the forked lab7 repository, navigating to **Settings**, and then scrolling down to the **Delete** option). The last step in setup is to create a new fork of [the repository](https://github.com/ucsd-cse15l-w23/lab7) and then ready your timer!
Note: This challenge is a lot quicker and easier after you've already run through it before -- because then, your command history will have saved your previously typed-out commands, which are accessible by the up and down arrow keys.

1. Log into ieng6

```
ssh cs15lwi23ZZZ@ieng6.ucsd.edu
<ENTER>
```

![image](https://user-images.githubusercontent.com/122485183/221118631-5a1dfafb-feac-4e59-8ad6-cef291256798.png)

Where the **ZZZ** is your unique combination of letters for your course-specific account.


2. Clone your fork of the repository from your GitHub account.

```
git clone [YOUR FORK LINK]
<ENTER>
```

![image](https://user-images.githubusercontent.com/122485183/221118726-fb4f695f-1624-4371-8c7d-906a329c6408.png)

Where the ssh link to your fork of the repository is accessible from your GitHub account by navigating to that repository, clicking on **Code** and click on **SSH** which will then generate a .git link that is accessible by ssh.

`git clone` functionally creates a copy of this **lab7** directory onto the machine.


3. Run the tests, demonstrating that they fail

```
cd lab7
<ENTER>
javac -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar *.java 
<ENTER>
java -cp .:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar org.junit.runner.JUnitCore ListExamplesTests
<ENTER>
```

![image](https://user-images.githubusercontent.com/122485183/221118866-345143d9-f4b9-4967-a977-6100436891a3.png)

Where the first command `cd lab7` changed directory into the newly cloned directory, and the two following commands route JUnit to the .java fils in the directory for JUnit to operate the testing, and then runs ListExampleTests.


4. Edit the code file to fix the failing test

```
nano ListExamples.java
<ENTER>
<DOWN ARROW> x2
import java.util.Collections;
<DOWN ARROW> x22
<LEFT ARROW> x3
<DELETE> x17
list1
[RIGHT ARROW]
[ENTER]
result.addAll(list2);
<ENTER>
Collections.sort(result);
<ENTER>
/*
<DOWN ARROW> x19
<END>
<ENTER>
*/
<CTRL + O>
<ENTER>
<CTRL + X>

```

![image](https://user-images.githubusercontent.com/122485183/221119111-a84c7ae5-bf2c-4cab-8815-5dd7bcf23393.png)

This looks like a lot of commands, but that's just a record of keystrokes it takes to edit the code into this (for ease of reading, the lines with **##** next to them were edited, but the actual inclusion of the comments is not present in the tutorial steps because its superfluous and would waste serious time on this challenge, of course):

```
import java.util.ArrayList;
import java.util.List;
import java.util.Collections; ## Added import Collections
interface StringChecker { boolean checkString(String s); }

class ListExamples {

  // Returns a new list that has all the elements of the input list for which
  // the StringChecker returns true, and not the elements that return false, in
  // the same order they appeared in the input list;
  static List<String> filter(List<String> list, StringChecker sc) {
    List<String> result = new ArrayList<>();
    for(String s: list) {
      if(sc.checkString(s)) {
        result.add(0, s);
      }
    }
    return result;
  }


  // Takes two sorted list of strings (so "a" appears before "b" and so on),
  // and return a new list that has all the strings in both list in sorted order.
  static List<String> merge(List<String> list1, List<String> list2) {
    List<String> result = list1; ## Edits result's definition 
    result.addAll(list2); ## Newline, adds the line's contents
    Collections.sort(result);  ## Newline, adds the line's contents
    /*     ## Newline, begins to comment out the rest of the method
    int index1 = 0, index2 = 0;
    while(index1 < list1.size() && index2 < list2.size()) {
      if(list1.get(index1).compareTo(list2.get(index2)) < 0) {
        result.add(list1.get(index1));
        index1 += 1;
      }
      else {
        result.add(list2.get(index2));
        index2 += 1;
      }
    }
    while(index1 < list1.size()) {
      result.add(list1.get(index1));
      index1 += 1;
    }
    while(index2 < list2.size()) {
      result.add(list2.get(index2));
      index1 += 1;
    }
    */    ## Newline, finishes commenting out the rest of the method
    return result;
  }


}

```

(Honestly, I couldn't figure out why the code as-is was bugging because it must've been something small and in one spot, but the reason why java has imports is to make your life as a programmer simpler and easier -- so consider my method of approaching the bug as "optimization")


5. Run the tests, demonstrating that they now succeed

```
<UP ARROW> x3
<ENTER>
<UP ARROW> x3
<ENTER>
```

![image](https://user-images.githubusercontent.com/122485183/221119268-768a7d8b-aae2-4729-adc9-c6556ba01d47.png)

Since the `javac` command was three commands ago in terminal history, it can be accessed first by three **Up Arrow** keystrokes, and then the same for `java` by the time that first command is done running.


6. Commit and push the resulting change to your GitHub account

```
git add ListExamples.java
<ENTER>
git commit -m "debugging ListExamples"
<ENTER>
git push origin main
<ENTER>

```

![image](https://user-images.githubusercontent.com/122485183/221119537-7301b31f-cbb6-428b-a3bd-0ef5948afb88.png)

`git add` is very important, here, because prior to this command, none of the changes made to ListExamples.java will have been tracked to be able to commit back to GitHub. The `-m` is also very important to the `commit` as well, since it becomes a whole production of writing a message and whatnot (which can be helpful if we were doing something more substantial a coding task that a paragraph or even multi-paragraph commit message would be warranted, but since it isn't), its better here just to use the `-m` flag followed by a string surrounded by **" " (double quotations** for a simple, one word commit message. `git push origin main` of course, finally pushes the local commit to the origin, hosted on GitHub. And that concludes the challenge!  

![image](https://user-images.githubusercontent.com/122485183/221121964-38de44e0-e73e-4a25-8421-36e7814fff4c.png)

I would have included a greater use of terminal command history (in the form of visibly spamming the up arrow key in-terminal) more if I hadn't messed up a bit in the middle of making this tutorial. I goofed to think the local machine ssh key I'd generated for the computer in the CSE basement would allow access for my local machine here in my dorm, so I had to run the regular **keygen** commands and blow through a bunch of typos in order to get it to work smoothly again; plus I was misremembering how I'd previously debugged the code and had to keep re-compiling it which messed up a perfect-run command terminal history order. In reflection, actually, I think all of those flub ups I'd encountered over the course of writing this report are further testament to the utility of these shortcuts and terminal history especially: the less a human operator has to type commands, the less a human operator causes human error.
