# Lab Report 9: Grade.sh
For this lab report, I've revisited lab 6 and finished [my grade.sh script](https://github.com/ARMatthias/list-examples-grader.git)! This report will serve as a showcase for the resulting script (and its accompanying files).

### grade.sh 
As found [here](https://github.com/ARMatthias/list-examples-grader/blob/main/grade.sh), looks like this!
```
#!/bin/bash
#
# grades a student submission ListExamples.java using an input github link to the submission repositiory.
#
# Name: Rae Matthias
# Email: amatthias@ucsd.edu
# PID: A17495586

# classpath config depending on operating system
if [[ $OSTYPE == "linux-gnu" ]] 
    then
        CPATH=".:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar"
elif [[ $OSTYPE == "msys" ]] 
    then
        CPATH=".;lib/junit-4.13.2.jar;lib/hamcrest-core-1.3.jar"
fi

# clear previous submission
rm -rf student-submission
truncate -s 0 gradeReports/*.txt
if [[ -e ListExamples.java ]]
    # if exists, remove previous ListExamples.java
    then 
        rm ListExamples.java
fi

# retrieve student submission
git clone $1 student-submission

# check if ListExamples.java is in student submission
if [[ -e student-submission/ListExamples.java ]]
    # if exists, print found, copy student submission out
    then
        echo "ListExamples.java found"
        cp student-submission/ListExamples.java ./
else
    # if not, print error, exit
    echo "Error: File ListExamples.java not found"
    exit 1
fi

# compile and check if compilation is successful
javac -cp $CPATH *.java 2> gradeReports/javac-errs.txt
if [[ $? -ne 0 ]] 
    # if unsuccessful, print standard error, exit
    then 
        echo "Error: failure during compilation"
        echo "Compilation feedback:"
        cat gradeReports/javac-errs.txt 
        exit 1
fi

# run and record outputs to corresponding files
java -cp $CPATH org.junit.runner.JUnitCore TestListExamples 2> gradeReports/run-err.txt 1> gradeReports/jUnit-out.txt
OUT=$(cat gradeReports/run-err.txt)

# check for successful runtime
if [[ $? -ne 0 ]]
    # if unsuccessful, print standard error, exit
    then
        echo "Error: failure during runtinme"
        echo $OUT
        exit 1
else
    #if successful, grade based off of total passed tests of TestListExamples.java
    if grep -q "FAILURES" gradeReports/jUnit-out.txt;
        then 
            cat gradeReports/jUnit-out.txt

            ERROUT=$(grep 'Tests run' gradeReports/jUnit-out.txt)

            IFS=", " read -a SCORES <<< $ERROUT

            FAILED="${SCORES[4]}"
            PASSED=$((9 - $FAILED))

            GRADE=`perl -e "print $PASSED/9"`
            GRADE=`perl -e "print $GRADE*100"`

            SWITCH=`echo ${GRADE:0:1}`

            case $SWITCH in 
                9)
                    LETTER="A"
                    ;;
                8)
                    LETTER="B"
                    ;;
                7)
                    LETTER="C"
                    ;;
                6)
                    LETTER="D"
                    ;;
                *)
                    LETTER="F"
                    ;;
            esac

            echo ${GRADE:0:5}"% "$LETTER            
    else
        echo "100% A"
    fi
fi

exit
```
And works in conjunction with [TestListExamples.java](https://github.com/ARMatthias/list-examples-grader/blob/main/TestListExamples.class):
```
/*
 * Name: Rae Matthias
 * Email: amatthias@ucsd.edu
 * PID: A17495586
 * 
 * This file tests ListExamples.java for correctness
 */

import static org.junit.Assert.*;
import org.junit.*;
import java.util.Arrays;
import java.util.List;

/*
 * This class creates a custom StringChecker that checks if a string is "moon"
 */
class IsMoon implements StringChecker {
  public boolean checkString(String s) {
    return s.equalsIgnoreCase("moon");
  }
}

/*
 * This class creates a custom StringChecker that checks if a string is "a"
 */
class IsA implements StringChecker {
  public boolean checkString(String s) {
    return s.equalsIgnoreCase("a");
  }
}

/*
 * This class aims to test ListExamples using test data
 */
public class TestListExamples {

  // test data initialization
  List<String> threeStrF = Arrays.asList("a", "b", "c");
  List<String> fiveStr = Arrays.asList("b", "c", "d", "e", "f");
  List<String> threeStrB = Arrays.asList("e", "f", "g");
  List<String> partial = Arrays.asList("a","moon","b","a","moon","moon");
  List<String> empty = Arrays.asList();

  StringChecker moon = new IsMoon();
  StringChecker isA = new IsA();

  List<String> test;
  List<String> expected;

  // ==== Test ListExamples.merge(List<String> list1, List<String> list2) ====

  /**
   * Tests merge() effecting the first half of the greater list
   */
  @Test(timeout = 500)
  public void testMergeFront() {

    test = ListExamples.merge(threeStrF, fiveStr);
    expected = Arrays.asList("a", "b", "b", "c", "c", "d", "e", "f");

    assertEquals(test, expected);
  }

  /**
   * Tests merge() effecting the latter half of the greater list
   */
  @Test(timeout = 500) 
  public void testMergeBack() {

    test = ListExamples.merge(threeStrB, fiveStr);
    expected = Arrays.asList("b","c","d","e","e","f","f","g");

    assertEquals(test, expected);
  }

  /**
   * Tests merge() of a populated string and an empty string
   */
  @Test(timeout = 500)
  public void testMergeEmpty() {

    test = ListExamples.merge(threeStrF, empty);

    assertEquals(test,threeStrF);
  }

  // ===== Test ListExamples.filter(List<String> list, StringChecker sc) =====

  /**
   * Tests filter() with StringChecker moon if none of the 
   * list elements are moon
   */
  @Test(timeout = 500)
  public void testFilterNotMoon() {

    test = ListExamples.filter(threeStrF, moon);

    assertEquals(Arrays.asList(), test);
  }

  /**
   * Tests filter() with StringChecker moon if some of the 
   * list elements are moon
   */
  @Test(timeout = 500)
  public void testFilterPartialMoon() {

    test = ListExamples.filter(partial,moon);

    assertEquals(test, Arrays.asList("moon","moon","moon"));
  }

  /**
   * Tests filter() with StringChecker moon if all of the 
   * list elements are moon
   */
  @Test(timeout = 500)
  public void testFilterFullMoon() {

    test = ListExamples.filter(Arrays.asList("moon","moon"),moon);

    assertEquals(test, Arrays.asList("moon","moon"));
  }

  /**
   * Tests filter() with StringChecker a if none of the
   * list elements are a
   */
  @Test(timeout = 500)
  public void testFilterNotA() {
    
    test = ListExamples.filter(threeStrB, isA);

    assertEquals(test, Arrays.asList());
  }

  /**
   * Tests filter() with StringChecker a if some of the
   * list elements are a
   */
  @Test(timeout = 500)
  public void testFilterPartialA() {

    test = ListExamples.filter(partial, isA);

    assertEquals(test, Arrays.asList("a", "a"));
  }

  /**
   * Tests filter() with StringChecker a if all of the
   * list elements are a
   */
  @Test(timeout = 500)
  public void testFilterFullA() {

    test = ListExamples.filter(Arrays.asList("a","a","a"), isA);

    assertEquals(test, Arrays.asList("a","a","a"));
  }

}
```
And some output-recording files in directory:

![image](https://user-images.githubusercontent.com/122485183/224530786-fe7d72e4-ffea-4bff-9611-d9faa0b6d014.png)

Where such files are cleared and populated (in that order) during each running of grade.sh

Running grade.sh on an example student submission (in this case, lab 3's original code on a windows system) with an input:
```
sh grade.sh https://github.com/ucsd-cse15l-f22/list-methods-lab3.git
```
results in output:

![image](https://user-images.githubusercontent.com/122485183/224530895-8b0d3a23-6367-4db8-a382-0cf88cc9a46a.png)

And running url `http://localhost:4000/grade?repo=https://github.com/ucsd-cse15l-f22/list-methods-lab3.git` on GradeServer 4000 results in

![image](https://user-images.githubusercontent.com/122485183/224532412-09b2216f-e80e-44b1-8a56-b8d974c0222c.png)

Whose last line of output `88.88% B` determines that the student submission --having code that passed 8 out of 9 tests-- got an overall B grade of 88.88%.

### testGrade.sh
But! As you could imagine, testing this script with the provided 7 example test cases becomes tedious and annoying very quickly -- what with having to type out `sh grade.sh` followed by each example link and then read over their respective outputs. 
And so! Within the repositiory is another two files: 

[testGrade.sh](https://github.com/ARMatthias/list-examples-grader/blob/main/testGrade.sh):
```
#!/bin/bash
#
# Tests grade.sh using 7 example student submissions (links stored in links.txt)
#
# Name: Rae Matthias
# Email: amatthias@ucsd.edu
# PID: A17495586

# classpath config depending on operating system
if [[ $OSTYPE == "linux-gnu" ]] 
    then 
        CPATH=".:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar"
elif [[ $OSTYPE == "msys" ]] 
    then
        CPATH=".;lib/junit-4.13.2.jar;lib/hamcrest-core-1.3.jar"
fi

# makes an array of the titles of the cases, in the order they will be tested
TITLES="Original (Lab 3):Correct:Compile Error:Incorrect Method Signature:Incorrect Filename:Nested File:Subtle Error"
IFS=":" read -a CASES <<< $TITLES

# detemines which set of links to use (ssh(git@git) or local(https))
echo $SHH_CLIENT > gradeReports/javac-errs.txt
if grep -q "." gradeReports/javac-errs.txt;
    then 
        LIST=$(grep "git@git" links.txt)
else
    LIST=$(grep "https" links.txt)
fi

# makes an array of the links of the cases, in the order they will be tested
IFS="&" read -a LINKS <<< $LIST

# runs grade.sh for each case, labeling each for their respective case in terminal output
for i in ${!LINKS[@]}; do
    echo "============= ${CASES[$i]} ============="

    # clear previous submission
    rm -rf student-submission
    truncate -s 0 gradeReports/*.txt
    if [[ -e ListExamples.java ]]
        then 
            rm ListExamples.java
    fi

    # retrieve student submission
    git clone ${LINKS[$i]} student-submission

    # check if ListExamples.java is in student submission
    if [[ -e student-submission/ListExamples.java ]]
        # if exists, print found, copy student submission out
        then
            echo "ListExamples.java found"
            cp student-submission/ListExamples.java ./
    else
        # if not, print error, continue to next case
        echo "Error: File ListExamples.java not found"
        continue
    fi

    # compile and check if compilation is successful
    javac -cp $CPATH *.java 2> gradeReports/javac-errs.txt
    if [[ $? -ne 0 ]] 
        # if unsuccessful, print standard error, continue to next case
        then 
            echo "Error: failure during compilation"
            echo "Compilation feedback:"
            cat gradeReports/javac-errs.txt 
            continue
    fi

    # run and record outputs to corresponding files
    java -cp $CPATH org.junit.runner.JUnitCore TestListExamples 2> gradeReports/run-err.txt 1> gradeReports/jUnit-out.txt
    OUT=$(cat gradeReports/run-err.txt)

    # check for successful runtime
    if [[ $? -ne 0 ]]
        # if unsuccessful, print standard error, continue to next case
        then
            echo "Error: failure during runtinme"
            echo $OUT
            continue
    else
        #if successful, grade based off of total passed tests of TestListExamples.java
        if grep -q "FAILURES" gradeReports/jUnit-out.txt;
            then 
                cat gradeReports/jUnit-out.txt

                ERROUT=$(grep 'Tests run' gradeReports/jUnit-out.txt)

                IFS=", " read -a SCORES <<< $ERROUT

                FAILED="${SCORES[4]}"
                PASSED=$((9 - $FAILED))

                GRADE=`perl -e "print $PASSED/9"`
                GRADE=`perl -e "print $GRADE*100"`

                SWITCH=`echo ${GRADE:0:1}`

                case $SWITCH in 
                    9)
                        LETTER="A"
                        ;;
                    8)
                        LETTER="B"
                        ;;
                    7)
                        LETTER="C"
                        ;;
                    6)
                        LETTER="D"
                        ;;
                    *)
                        LETTER="F"
                        ;;
                esac

                echo ${GRADE:0:5}"% "$LETTER            
        else
            echo "100% A"
        fi
    fi
    done

exit
```
Which loops the content of grade.sh for each example case (whose links and case titles are stored in arrays) with the exits replaced by continue statements to simply pass on to the next test case 

and [links.txt](https://github.com/ARMatthias/list-examples-grader/blob/main/links.txt)
```
git@github.com:ucsd-cse15l-f22/list-methods-lab3.git&git@github.com:ucsd-cse15l-f22/list-methods-corrected.git&git@github.com:ucsd-cse15l-f22/list-methods-compile-error.git&git@github.com:ucsd-cse15l-f22/list-methods-signature.git&git@github.com:ucsd-cse15l-f22/list-methods-filename.git&git@github.com:ucsd-cse15l-f22/list-methods-nested.git&git@github.com:ucsd-cse15l-f22/list-examples-subtle.git
https://github.com/ucsd-cse15l-f22/list-methods-lab3.git&https://github.com/ucsd-cse15l-f22/list-methods-corrected.git&https://github.com/ucsd-cse15l-f22/list-methods-compile-error.git&https://github.com/ucsd-cse15l-f22/list-methods-signature.git&https://github.com/ucsd-cse15l-f22/list-methods-filename.git&https://github.com/ucsd-cse15l-f22/list-methods-nested.git&https://github.com/ucsd-cse15l-f22/list-examples-subtle.git
```
Which is just a text file of two lines: the first line being the ssh links for each test case in order, the second line being the same links and order but https version -- both have each link separated by an `&` for ease of parsing into arrays within testGrade.sh

Now, without having to type anything else but 
```
sh testGrade.sh 
```
and pressing enter, the beautiful output testing each example student submission case is:

![image](https://user-images.githubusercontent.com/122485183/224531292-df9bee15-6ca2-4992-9953-f2d97a50aae7.png)
![image](https://user-images.githubusercontent.com/122485183/224531315-6b1bd2b6-18be-4361-a572-4bd81d9df878.png)
![image](https://user-images.githubusercontent.com/122485183/224531377-545173fc-85c6-4f81-9600-6529700caf15.png)
![image](https://user-images.githubusercontent.com/122485183/224531396-c1cbb9c7-d798-401b-82dd-607cc53b397e.png)

and having modified [GradeSever.java](https://github.com/ARMatthias/list-examples-grader/blob/main/GradeServer.java) to include lines 57 and 58
```
       else if (url.getPath().equals("/testAll")) {
          return ExecHelpers.exec(new String[]{"bash", "testGrade.sh"});
```

which looks like this when ran on GradeServer 4000

![image](https://user-images.githubusercontent.com/122485183/224532770-258753b9-ed61-4ce8-b9fe-3f21c7a572e0.png)
![image](https://user-images.githubusercontent.com/122485183/224532795-a81c6640-d73a-43aa-be6e-2b3d5574a1f3.png)

Which demonstrates, all at once, that:
- [Original (Lab 3)](https://github.com/ucsd-cse15l-f22/list-methods-lab3) gets a B with 88.88%.
- [Correct](https://github.com/ucsd-cse15l-f22/list-methods-corrected) gets a A with 100%.
- [Compile Error](https://github.com/ucsd-cse15l-f22/list-methods-compile-error) exits with a compile error and the error output.
- [Incorrect Method Signature](https://github.com/ucsd-cse15l-f22/list-methods-signature) exits with a compile error and the error output.
- [Incorrect Filename](https://github.com/ucsd-cse15l-f22/list-methods-filename) exits with a "ListExamples.java not found" error message.
- [Nested File](https://github.com/ucsd-cse15l-f22/list-methods-nested) exits with a "ListExamples.java not found error message.
- [Subtle Error](https://github.com/ucsd-cse15l-f22/list-examples-subtle) gets a C with 77.77%.

### Reflection
Revisiting Lab 6 and completing this grading script was actually very valuable to my understanding of bash. Prior to completeing this script, I didn't have a good grasp of arrays, using terminal commands as conditional statements, and computing math operations in bash -- as well as generally strengthened my competency in the language in general (such as certain space-sensitivity in variable initializations and being introduced to the utility of case staements, which function like the bash equivalent of switch staements). Beyond strengthening my confidence in bash, writing a grading bash script specifically helped me in understanding what TAs do on the back end of Programming Assignments in the CSE series and all the strange condtions that have to be considered when dealing with a bunch of different student sbmissions of the same intended implementation.
