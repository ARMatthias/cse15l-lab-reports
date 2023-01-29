# Lab Report 2 - Servers and Bugs (Week 3)

## Part 1 - StringServer

StringServer keeps a single string that is added to by each successive request.
```
// This server was written to operate with Server.java as support
// The source for Server.java in its original project can be found here: 
// https://github.com/ucsd-cse15l-f22/wavelet
// which was written by Joe Politz (guthub: jpolitz) and MohaElder (github: MohaElder169)
import java.io.IOException;
import java.net.URI;
class Handler implements URLHandler {
    String list = "";
    public String handleRequest(URI url) {
        if (url.getPath().equals("/")) {
            return list;
        }
        else if (url.getPath().contains("/add-message")) {
            String[] param = url.getQuery().split("=",2);
                list += (param[1] + "\n");
                return list;
        }
        else {
            return "404: REQUEST NOT FOUND";
        }
    }
}
public class StringServer {
    public static void main(String[] args) throws IOException {
        if (args.length == 0) {
            System.out.println(
                "PORT NUMBER REQUIRED.\nEnter port number and try again");
            return;
        }
        int port = Integer.parseInt(args[0]);
        Server.start(port, new Handler());
    }
}
```

![image](https://user-images.githubusercontent.com/122485183/215293869-41f798ec-f640-4bc3-902e-1324c135e847.png)

Invalid input (such as a string not pecluded by add-message) returns an error. The argument to this input is the URL which is passed into handleRequest. Since this url.getPath() does not equal "/" nor does it contain "/add-message", it is an invalid input and handleRequest defaults on the error message. No values of StringServer or Handler are altered or changed by this since the handler behavior for an invlaid input is only to display an error message, and not change anything about what is contained in the single variable list (continaing all input strings from previous and current requests).


![image](https://user-images.githubusercontent.com/122485183/215293918-809b743c-741f-4ff0-8402-a596aae1f1c2.png)

But String Server will successfully add and display a valid input. The argument, the url, gets passed into class Handler and then run through handleRequest, which correctly identifies that the url path is not just "/" but contains "/add-message". After this, it alters the list value,which concatenates this input string plus a "/n" (new line) in order to prepare for the next request.  


![image](https://user-images.githubusercontent.com/122485183/215293940-70754462-eb57-47c8-bec4-70d673bec555.png)

And since handleRequest uses an int limited .split, = (equals signs) can be written into the string and displayed without error! And, as can be seen here, a new request will be displayed after previous requests in chronological order, as all "/add-message" requests are first urls grabbed by Server's through class Handler using urlHandler and then identified for their specific request through StringServer's handleRequest method and processed to add the string to variable list and returned to the web server to display. 


![image](https://user-images.githubusercontent.com/122485183/215294396-201216a3-a1e9-468c-b339-fac59b5268b1.png)

And, of course, visiting the site with just "/" as the request will call method handleRequest, which will idenitfy the path as just "/" and won't change anything about the variable list but will retunr list to the web server to be displayed, unaltered.

## Part 2 - Debugging

One of the bugs addressed int this lab was the behavior of the averageWithoutLowest method in ArrayExamples -- which, when given a double array of multiple equal elements (ex: new int[] = {8.0,8.0,8.0,8.0} ), it would compute the average of the array as 0.0, when it should be 8.0. 

The tests used to demonstrate this bug's symptoms are as follows: 
```
  // deci
  double[] dEmpty = {};
  double[] dSolo = {3.0};
  double[] dFull = {1.0,2.0,3.0,4.0,5.0};
  double[] same = {8.0,8.0,8.0,8.0};
  
  @Test
  /**
   * test averageWithoutLowest() for input that is: empty, a single item,
   * 5 items, and a list of all the same item, respectively
   */
  public void testAverageWithoutLowest() {
    assertEquals("Check average", 0.0, ArrayExamples.averageWithoutLowest(dEmpty),0);
    assertEquals("Check average", 0.0, ArrayExamples.averageWithoutLowest(dSolo),0);
    assertEquals("Check average", 3.5, ArrayExamples.averageWithoutLowest(dFull), 0);
    assertEquals("Check average", 3.5, ArrayExamples.averageWithoutLowest(new double[] {3, 4, 1, 5, 2}),0);
    assertEquals("Check average", 8.0, ArrayExamples.averageWithoutLowest(same), 0);
  }
```

Where, the first 4 tests (with inputs dEmpty, dSolo, dFull, and a scrambled dFull, in that order) passing, but the last test (with input same) failing. 

![image](https://user-images.githubusercontent.com/122485183/215297783-d5fa2903-3321-43c8-8ecc-378277e5f285.png)

The output of an incorrect input was a symptom to the actual bug, which was a logic error regarding how the program was coded to exclude the lowest number of the input array. Since the code prior to debugging would use a for each loop to iterate through the elements of the input array and with each, used an if conditional to check and see if the number of that current iteration was the lowest number of the array. If it was, the loop would skip it, and that number would not be included in the summantion of the elements. This becomes a problem, evidently, when there is any more than 1 instance of that element in the array -- as the algorithm would then skip however many instances there are of that element, when the program is only meant to skip one. 

The original code is as follows:

```
  static double averageWithoutLowest(double[] arr) {
    if(arr.length < 2) { return 0.0; }
    double lowest = arr[0];
    for(double num: arr) {
      if(num < lowest) { lowest = num; }
    }
    double sum = 0;
    for(double num: arr) {
      if(num != lowest) { sum += num; }
    }
    return sum / (arr.length - 1);
  }
```

The solution to this is to create a boolean variable that tracks within the for each loop if the first instance of the lowest element has been iterated upon and skipped already, and then setting that as the conditional for if a num evaluated as equal to lowest should be added to sum or not.

Debugged code is as follows:

```
  // Averages the numbers in the array (takes the mean), but leaves out the
  // lowest number when calculating. Returns 0 if there are no elements or just
  // 1 element in the array
  static double averageWithoutLowest(double[] arr) {
    if (arr.length < 2) {
      return 0.0; 
    }
    else {
      double lowest = arr[0];
      double sum = 0;
      boolean lowestCheck = false;
      for (double num : arr) {
        if (num < lowest) { 
          lowest = num; 
        }
      }
      for (double num: arr) {
        if (num != lowest) {
          sum += num;
        }
        else {
          if (lowestCheck) {
            sum += num;
          }
          else {
            lowestCheck = true;
          }
        }
      }
      return (sum / (arr.length - 1) );      
      }
  }
```

The boolean lowestCheck then enables the rest of potential instances of lowest while iterating through the input array to be added to the sum -- effectively only skipping one occurence of lowest in the event that there are multiple.

![image](https://user-images.githubusercontent.com/122485183/215297860-353d4dbb-a82e-4f86-b8ad-b0cd5b4298cc.png)


## Part 3 - New Concept Reflection

Previous to the last 2 labs (being Labs 2 and 3), I hadn't known about local servers or the relationship between urls and requests other than the thought that servers were big computer-y boxes in basements scattered around the placce and that url paths are just directories for website organization but not that the queries themselvees could relay information to the host server, respectively. Setting up and running a couple simple servers has vastly improved by comprehension of the *how* which in turn strengthened my grasp of the *what*.
